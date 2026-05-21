# PhotoForge 系统架构概要设计

> 创建日期：2026-05-21
> 状态：概要设计（快速验证版）

---

## 系统模块总览

PhotoForge 由 4 个核心模块构成：

| 模块 | 职责 | 技术栈 | 线程模型 |
|------|------|--------|----------|
| **UI Layer** | 用户交互、参数编辑、图像预览 | Qt 6 (Widgets + QML) | 主线程（Qt 事件循环） |
| **Core Engine** | 图像数据存储、滤镜算法、图层合成、编解码 | C++17 / OpenCV | 工作线程池（并行像素处理） |
| **AI Engine** | 人脸检测、人像分割、AI 修图推理 | C++17 / ONNX Runtime | GPU 推理线程 + CPU 后处理 |
| **Batch System** | 任务队列管理、批量处理、参数模板 | C++17 | 独立后台线程池 |

---

## 1. Core Engine 模块

### 职责

Core Engine 是 PhotoForge 的图像数据核心，负责管理所有图像的像素数据、图层结构、混合模式、滤镜算法和编解码操作。它不感知 UI 和 AI 的存在，仅提供纯数据计算接口。

### 核心数据模型

```
PhotoImage                  -- 顶层图像容器
  ├── Layer (多个)           -- 图层
  │   ├── Mask              -- 图层蒙版
  │   ├── BlendMode         -- 混合模式
  │   └── FilterStack       -- 滤镜栈
  ├── UndoRedoManager       -- 撤销/重做管理器
  └── Codec                 -- 编解码器
```

### 关键子模块

- **Image Core**: `PhotoImage`, `Layer`, `Mask`, `Blend` — 数据结构与合成逻辑
- **Filters**: `Exposure`, `Curves`, `ColorBalance` — 像素级调色算法
- **Transform**: `Crop`, `Rotate`, `Flip`, `Resize` — 几何变换
- **Codec**: JPEG/PNG/WebP/TIFF 读写（基于 OpenCV `imdecode`/`imencode`）
- **UndoRedo**: `Command` + `CommandManager` — 命令模式实现撤销/重做

### 线程安全设计

- 每个 `PhotoImage` 是独立的可拷贝对象，不共享内部 `cv::Mat`
- 耗时操作（滤镜、合成、编解码）在调用侧自行选择线程调度，Core Engine 本身不管理线程
- 通过 `const` 正确性保证：不修改输入图像，返回新 `PhotoImage` 副本

---

## 2. AI Engine 模块

### 职责

AI Engine 封装 ONNX Runtime C++ API，加载预训练的 `.onnx` 模型，对人像图像进行 AI 分析（人脸检测、关键点定位、人像分割）和 AI 修图（磨皮、祛痘、五官微调）。AI Engine 不感知 UI，输入 `cv::Mat` 或 `PhotoImage`，输出分析结果 `AIResult` 或处理后的图像。

### 核心类结构

```
Session                  -- ONNX Runtime 会话封装（加载/推理）
FaceDetector             -- 人脸检测模型推理
LandmarkDetector         -- 106 关键点定位
Segmentator              -- 人像分割
SkinRetoucher             -- AI 磨皮/祛痘
FaceAdjuster             -- 五官微调
```

### 数据流

```
PhotoImage ──→ FaceDetector ──→ bbox + landmarks
                  │
                  ▼
           Segmentator ──→ skin_mask + hair_mask
                  │
          ┌───────┴───────┐
          ▼               ▼
    SkinRetoucher    FaceAdjuster
          │               │
          └───────┬───────┘
                  ▼
            PhotoImage (处理后)
```

### 线程模型

- `Session` 使用 ONNX Runtime 的 `Run()` 方法，调用侧需保证线程安全
- GPU 推理使用单独线程（CUDA/TensorRT EP），避免阻塞主线程
- 后处理（mask 上采样、边缘羽化）在 CPU 工作线程完成

---

## 3. 核心模块数据流关系

### 主数据通路

```
┌──────────┐   编辑参数    ┌─────────────┐  调用滤镜算法   ┌──────────┐
│          │ ────────────→ │             │ ─────────────→ │          │
│ UI Layer │               │ Core Engine │                │ 图像数据 │
│  (Qt)    │ ←──────────── │ (C++/OpenCV)│ ←───────────── │ (cv::Mat)│
│          │  信号: 处理完成  │             │  返回新图像      │          │
└────┬─────┘               └──────┬──────┘               └──────────┘
     │                            │
     │ 请求AI分析                  │ 请求AI修图
     ▼                            ▼
┌─────────────────────────────────────────────────────────────┐
│                     AI Engine                                │
│            (ONNX Runtime C++ / GPU)                         │
│                                                             │
│  FaceDetect → Landmark → Segment → Retouch → Adjust        │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ AI 结果回调
                             ▼
                    ┌─────────────────┐
                    │   Batch System  │
                    │  (任务队列/模板)  │
                    └─────────────────┘
```

### 数据流分类

#### 用户编辑流程（UI → Core Engine → UI）

```
1. 用户在 UI 面板调整参数（如曝光 +0.5EV）
2. UI 发送 ApplyFilter(FilterParams) 到 Core Engine
3. Core Engine 对当前图层应用滤镜，返回新 PhotoImage
4. UI 收到 ImageProcessed() 信号，更新预览
```

#### AI 修图流程（UI → AI Engine → Core Engine → UI）

```
1. 用户点击"AI 美颜"
2. UI 调用 AIEngine::RetouchSkin() / AdjustFace()
3. AI Engine 加载图像 → ONNX 推理 → 生成 mask → 修图
4. AI Engine 返回修图后的 PhotoImage
5. Core Engine 将其叠入图层栈
6. UI 更新预览
```

#### 批量处理流程（UI → Batch System → [AI Engine + Core Engine]）

```
1. 用户在批量面板选择多张图片 + 参数模板
2. UI 调用 BatchProcessor::EnqueueTask(BatchTask)
3. Batch System 工作线程循环：
   a. Codec 读取图片（Core Engine）
   b. AI Engine 分析/修图
   c. Core Engine 应用模板参数
   d. Codec 导出图片
4. Batch System 通过信号通知 UI 进度
```

### 模块间数据依赖关系

| 方向 | 数据内容 | 传输方式 | 异步? |
|------|----------|----------|-------|
| UI → Core Engine | `FilterParams`, `TransformParams` | 直接函数调用 | 建议异步 |
| Core Engine → UI | `PhotoImage` (处理结果) | Qt 信号 | 是 |
| UI → AI Engine | `PhotoImage` | 直接函数调用 | 必须异步 |
| AI Engine → Core Engine | `ai::AIResult`, `cv::Mat` | 返回值 | 是 |
| UI → Batch System | `BatchTask` | 函数调用 | 是 |
| Batch System → Core Engine | 路径/参数 | 内部调用 | 后台线程 |
| Batch System → AI Engine | `PhotoImage` | 内部调用 | 后台线程 |
| Batch System → UI | 进度/完成/失败 | Qt 信号 | 是 |

---

## 4. 命名空间规划

```cpp
namespace photoforge::ui       // UI 层
namespace photoforge::engine   // Core Engine
namespace photoforge::ai       // AI Engine
namespace photoforge::batch    // 批量处理
namespace photoforge::common   // 公共工具
```

---

## 架构决策记录

| 决策 | 选项 | 选择理由 |
|------|------|----------|
| Core Engine 不管理线程 | 模块内线程池 vs 无线程 | 调用方（UI/Batch）最了解上下文，避免线程模型耦合 |
| AI Engine 返回新图像而非原地修改 | 原地修改 vs 返回新副本 | 符合不可变性原则，便于撤销/重做 |
| Batch System 使用独立线程池 | 共享线程池 vs 独立 | 批量处理是长任务，不应抢占交互操作的资源 |
| 模块间数据传递使用 `PhotoImage` 值语义 | 指针/引用 vs 拷贝 | 避免悬挂指针，简化生命周期管理 |

---

> **说明**：本文档为快速架构概要验证版本，仅包含模块概述和数据流关系。详细的接口定义（`.h` 头文件签章）和开发任务分解见 `docs/api.md` 和 `docs/plan.md`。

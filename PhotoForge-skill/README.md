# PhotoForge — AI 商业修图软件开发系统

> 从 0 开发一款**像素蛋糕**同类的 AI 人像修图桌面软件。

## 技术栈

| 层级 | 技术选型 |
|------|----------|
| UI 框架 | Qt 6.x (Widgets + QML) |
| 核心语言 | C++17 |
| AI 推理 | ONNX Runtime (Windows: +TensorRT) |
| AI 训练 | Python 3.10 + PyTorch 2.x |
| 图像处理 | OpenCV 4.8+ + GLSL shader |
| 构建系统 | CMake + vcpkg |

## 项目结构

```
PhotoForge-skill/
├── SKILL.md           # 主智能体提示词（技能入口）
├── LICENSE            # MIT 开源许可
├── README.md          # 本文件
├── todo.md            # 开发待办清单
└── agents/            # 子智能体定义
    ├── photo-forge-architect.md  # 架构设计
    ├── photo-forge-dev-core.md   # 图像引擎开发
    ├── photo-forge-dev-ai.md     # AI 推理集成
    ├── photo-forge-dev-ui.md     # Qt 界面开发
    ├── photo-forge-dev-batch.md  # 批量处理开发
    └── photo-forge-tester.md     # 测试验证
```

## 使用方式

### 作为 Claude Code Skill 安装

```bash
mkdir -p ~/.claude/skills
cp -r PhotoForge-skill ~/.claude/skills/photo-forge/
```

然后在 Claude Code 中通过 `/photo-forge` 触发，或在提到"AI人像修图"、"像素蛋糕"等关键词时自动激活。

### 多智能体协作架构

主智能体（编排者）协调 6 个子智能体逐模块完成开发：

1. **photo-forge-architect** — 定义模块接口、类设计、数据流
2. **photo-forge-dev-core** — C++17 + OpenCV 实现图层、滤镜、撤销
3. **photo-forge-dev-ai** — ONNX Runtime 集成、人脸检测/分割/AI 修图
4. **photo-forge-dev-ui** — Qt6 Widgets/QML 实现专业修图界面
5. **photo-forge-dev-batch** — 批量处理管线、任务队列、参数模板
6. **photo-forge-tester** — 代码审查、编译检查、性能测试（只读）

### 开发流程

```
架构设计 → 逐模块开发循环 → 集成测试
```

每个模块经历：开发 → 测试 → 修正（最多 3 轮）的完整循环。

## 当前状态

参见 [todo.md](todo.md) 了解详细的开发进度和待办事项。

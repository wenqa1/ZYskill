# TODO - PhotoForge 开发待办

> 技能触发时优先读取此文件，按优先级从上到下执行。

## Phase 1: 架构设计

- [ ] **P0** 架构设计 — 启动 photo-forge-architect 产出 docs/architecture.md、docs/api.md、docs/plan.md

## Phase 2: 核心引擎开发

- [ ] **P0** Core Engine — 图像数据结构 (Image/Layer/Mask/Blend)
- [ ] **P0** Core Engine — 基础滤镜 (Exposure/Curves/ColorBalance)
- [ ] **P1** Core Engine — 几何变换 (Crop/Rotate/Flip)
- [ ] **P1** Core Engine — 编解码 (JPEG/PNG/WebP/TIFF)
- [ ] **P1** Core Engine — 撤销/重做系统

## Phase 3: AI 引擎集成

- [ ] **P0** AI Engine — ONNX Runtime 会话封装
- [ ] **P1** AI Engine — 人脸检测模型集成
- [ ] **P1** AI Engine — 关键点定位模型集成
- [ ] **P2** AI Engine — 人像分割模型集成
- [ ] **P2** AI Engine — AI 磨皮/祛痘
- [ ] **P2** AI Engine — 五官微调

## Phase 4: UI 界面

- [ ] **P1** UI — 主窗口框架 (MainWindow + 菜单 + 快捷键)
- [ ] **P1** UI — 图像预览组件
- [ ] **P2** UI — 编辑面板 (参数调节)
- [ ] **P2** UI — 图层面板
- [ ] **P3** UI — 批量处理面板

## Phase 5: 批量处理

- [ ] **P2** Batch — 任务队列与后台工作线程
- [ ] **P3** Batch — 参数模板系统

## Phase 6: 集成与测试

- [ ] **P1** 编写 CMakeLists.txt 完整构建配置
- [ ] **P2** 单元测试 (Core Engine)
- [ ] **P2** 集成测试 (各模块联调)
- [ ] **P3** 性能测试 (单图 < 1s)

# TODO — 微信小程序多智能体开发 Skill 优化清单

> 触发 skill 时，主Agent 应优先读取此文件，按优先级依次处理待办项。
> 完成一项后，将 `[ ]` 改为 `[x]` 并注明完成日期。

---

## P0 — 核心功能缺口

- [x] **独立分包增强**（2026-05-25）
  - wmp-dev: 独立分包运行规则、getApp({allowDefault: true})、延迟生命周期说明、分包异步化示例
  - wmp-tester-structure: SP1-SP6 分包检查项（注册、隔离、预下载、异步化）
- [x] **app.json 全局 usingComponents**（2026-05-25）
  - wmp-dev: 全局注册使用说明与权衡
  - wmp-tester-structure: J7 全局 usingComponents 检查
- [x] **分包预下载配置**（2026-05-25）
  - wmp-planner: preloadRule 配置说明
  - wmp-dev: preloadRule 示例代码 + 预下载额度（2MB）说明
  - wmp-tester-structure: SP4 预下载路径有效性检查
- [x] **onShareTimeline 支持**（2026-05-25）
  - wmp-tester-style: A5 分享到朋友圈检查项
- [x] **页面生命周期清理检查**（2026-05-25）
  - wmp-tester-performance: 3.9 生命周期释放检查（LC1-LC6: 定时器/监听器/WebSocket/observer/后台态）

## P1 — 重要增强

- [x] **基础库版本兼容性策略**（2026-05-25）
  - wmp-planner: 基础库版本策略表（含各特性的最低版本要求）
  - wmp-tester-structure: V1-V4 版本兼容检查（API 匹配、canIUse、Skyline、分包异步化版本）
- [x] **Behavior 深度复用模式**（2026-05-25）
  - wmp-dev: 完整 Behavior 代码模板（登录态 + 埋点上报）含合并冲突说明
  - wmp-tester-structure: C1b Behavior 字段冲突检查
- [x] **theme.json 暗黑模式完整方案**（2026-05-25）
  - wmp-planner: app.json darkmode 配置说明 + theme.json 模板文件（含 light/dark 色板）+ wxss 变量引用说明
- [x] **自定义 tabBar 组件**（2026-05-25）
  - wmp-planner: app.json custom tabBar 配置说明 + 基础库版本约束
  - wmp-dev: custom-tab-bar/index 完整组件代码（json/js/wxml）+ 关键规则
  - wmp-dev: app.json tabBar.list 配置时区分自定义模式
- [x] **启动性能分析**（2026-05-25）
  - wmp-planner: app.js onLaunch 避免同步阻塞操作指引 + 按需 require 建议

## P2 — 新特性覆盖

- [ ] **npm 包管理支持**：当前 skill 未涉及第三方依赖
  - wmp-planner 应在需求含 npm 包时初始化 package.json
  - wmp-dev 需了解小程序中使用 npm 包的约束（仅支持纯 JS 包，需构建）
  - 测试应检查引用的 npm 包是否在 package.json 中声明
- [ ] **插件接入**：小程序插件（地图、客服、直播等）接入流程
  - app.json 中 `plugins` 字段声明
  - 测试应验证 `usingComponents` 中的插件组件路径格式正确
- [ ] **Workers 多线程**：小程序 Worker 用于密集计算
  - workers 目录约定与 `app.json` 配置
  - 主线程与 Worker 通信模式（`worker.postMessage` + `worker.onMessage`）
- [ ] **自动化测试集成**：使用 miniprogram-automator 做 E2E
  - 建议在 Phase 3 收尾后触发自动化测试（可选步骤）
  - 需要基础测试模板
- [ ] **getCurrentPages 导航栈管理**：目前仅依赖 wx.navigateTo
  - 导航栈深度限制（最多 10 层）
  - 超过限制应使用 `redirectTo` 或 `reLaunch`
  - 测试应检查深层跳转场景是否处理了栈溢出

## P3 — 质量提升

- [ ] **真机调试检查清单**：Phase 3 收尾中缺少真机验证步骤
  - iPhone X+ 安全区适配验证
  - Android 键盘弹起遮挡验证
  - 弱网/断网场景验证
  - 不同基础库版本兼容验证
- [ ] **骨架屏与加载态建议**：wmp-dev 产出应包含骨架屏（如适用）
  - 首屏加载使用 skeleton 组件占位
  - 测试应检查加载/空态/错误态三者完备
- [ ] **图片 CDN 与 WebP 策略**：当前仅提及懒加载
  - wmp-dev 应在 wxml 中标注 CDN 尺寸参数占位
  - 推荐使用 WebP 格式（较 JPG 小 30-50%）
  - 测试应检查 image 标签是否有合理的 mode 和 lazy-load
- [ ] **微信支付接入模式**：缺少支付流程的最佳实践
  - 前端调起支付参数由后端生成，wmp-dev 只负责 `wx.requestPayment` 调用
  - 支付结果以后端回调为准，前端仅做 UI 反馈
  - 测试应检查支付回调中有无重复下单风险
- [ ] **订阅消息模板管理**：当前仅提及触发约束
  - 模板 ID 应集中在 constants 文件中管理
  - 订阅前检查是否已授权（`wx.getSetting`）
  - 一次订阅多次发送（`tmplIds` 数组 ≤ 3 个）

## P4 — 打磨优化

- [ ] **AI 能力集成指引**：小程序 AI 接口（VK 视觉、人脸识别、OCR 等）
  - 涉及 `wx.createVKSession`、`wx.startFacialRecognitionVerify` 等
  - 需用户主动触发 + 隐私合规（已有规则 32-34 覆盖）
- [ ] **硬件 API 最佳实践**：蓝牙、NFC、Wi-Fi 等
  - 连接类 API 的生命周期管理（`closeBLEConnection` 等）
  - 测试应检查资源释放
- [ ] **动画 API 优化建议**：当前仅原则性提及
  - CSS animation 优先，`wx.createAnimation` 次之，setData 驱动最后
  - 复杂动画用 Worklet（Skyline）或 WXS 响应事件（WebView）
- [ ] **小程序码/链接生成**：文档的 scene 参数生成与解析
  - `scene` 参数长度限制（32 字节）
  - 通过 `options.scene` 在 onLoad 中解析
- [ ] **ECharts / 图表组件**：数据可视化场景
  - 使用 ec-canvas 组件封装，注意 canvas 同层渲染
  - 测试应检查 canvas 组件是否设置了正确的 `type`

---

## 完成记录

| 日期 | 事项 |
|------|------|
| 2026-05-25 | P0 全部完成（独立分包 / 全局 usingComponents / preloadRule / onShareTimeline / 生命周期清理） |
| 2026-05-25 | P1 全部完成（基础库版本策略 / Behavior 模板 / dark mode / 自定义 tabBar / 启动性能） |

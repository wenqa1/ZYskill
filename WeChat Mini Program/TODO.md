# TODO — 微信小程序多智能体开发 Skill 优化清单

> 触发 skill 时，主Agent 应优先读取此文件，按优先级依次处理待办项。
> 完成一项后，将 `[ ]` 改为 `[x]` 并注明完成日期。

---

## P0 — 核心功能缺口

- [ ] **独立分包增强**：wmp-dev 对独立分包（`independent: true`）的支持不足
  - 独立分包不能 require 主包任何 JS，需用 `getApp({allowDefault: true})`
  - 独立分包启动时主包 onLaunch/onShow 延迟触发
  - 测试中需增加独立包隔离性检查
- [ ] **app.json 全局 usingComponents**：支持全局注册组件，减少 page 级重复声明
  - app.json 顶层 `usingComponents` 字段（基础库 2.20.1+）
  - 需权衡：全局注册 vs 按需声明（影响代码可分析性）
- [ ] **分包预下载配置**：wmp-planner 初始 app.json 应可选预生成 `preloadRule`
  - 高频入口 page 的后台分包预下载
  - 测试应检查预下载配置是否合理
- [ ] **onShareTimeline 支持**：三个测试均缺少对"分享到朋友圈"的支持检查
  - style 检查添加 `onShareTimeline` 实现
  - 与 `onShareAppMessage` 并列检查
- [ ] **页面生命周期清理检查**：测试只检查了创建，未检查销毁
  - onUnload/detached 中应清理：定时器、自定义事件监听、observer 反注册、WebSocket 连接

## P1 — 重要增强

- [ ] **基础库版本兼容性策略**：当前固定 2.32.3，需策略性升级
  - 按 API 最低版本要求自动推断推荐 libVersion
  - 测试应检查代码调用的 API 与配置的基础库版本是否兼容
  - 添加 `wx.canIUse` 降级模式的推荐
- [ ] **Behavior 深度复用模式**：当前仅提及，缺少具体实现指引和测试
  - 常见 behavior 模板：登录态、埋点上报、下拉刷新、表单校验
  - 测试应检查 behavior 的字段/方法是否与组件冲突（命名空间污染）
- [ ] **theme.json 暗黑模式完整方案**：style 测试仅检查 wxss 中的媒体查询
  - wmp-planner 应在需求含暗黑模式时生成 `theme.json`
  - wxss 中使用 CSS 变量 + theme.json 映射，而非 `@media` 硬编码
  - 测试需同时验证浅色/深色两套样式
- [ ] **自定义 tabBar 组件**：目前只能用基础 tabBar 配置
  - 自定义 tabBar 需要 `custom-tab-bar/index` 组件（基础库 2.5.0+）
  - wmp-dev 应支持在需求明确时使用自定义 tabBar
  - 测试需检查自定义 tabBar 的 `setSelected` 方法
- [ ] **性能面板与启动分析**：缺少对小程序启动性能的验证
  - 检查首屏依赖数（`lazyCodeLoading` 已配但未验证效果）
  - 检查同步 setStorageSync 调用对启动耗时的影响
  - 检查 app.js onLaunch 中是否有阻塞操作

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
| — | — |

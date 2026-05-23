---
name: wechat-miniprogram
description: "微信小程序多智能体开发系统。基于 WXML + WXSS + JS + JSON 四件套架构，通过主智能体协调 wmp-planner / wmp-dev / wmp-tester-{structure,style,performance} 等子Agent，分批完成 page 和 component 的开发与三维质量验证（结构规范 / 样式交互 / 性能安全）。当用户提到微信小程序、wxml/wxss/wxs、wx.request/wx.navigateTo、Page/Component、tabBar、app.json、小程序云开发、小程序订阅消息等关键词时自动加载。当前项目文件夹包含 app.json + project.config.json 时自动触发。"
version: 0.1.0
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Agent
---

# 微信小程序多智能体开发系统 — 入口

> 此文件是 skill 清单。**主智能体提示词**在 [主智能体提示词.md](主智能体提示词.md) 中，**子智能体定义**在 [agents/](agents/) 目录。

## 触发条件

技能被触发时，按以下顺序判断当前状态：

1. **存在 `dev-plan.md`** → 项目已经在进行中。读取 dev-plan.md，找出第一个 ⏳ 任务，进入 Phase 2 批量开发循环
2. **存在 `app.json` 但无 dev-plan.md** → 项目骨架已搭建但缺少计划。启动 wmp-planner 补全计划
3. **目录为空或仅有需求文档** → 全新项目。按主智能体提示词的「初始化」流程启动

## 子智能体清单

| Agent | 模型 | 职责 |
|-------|------|------|
| wmp-planner | inherit | 阅读需求 → 拆任务 → 搭骨架（app.json/app.js/app.wxss/utils）→ 写 dev-plan.md & page-spec.md |
| wmp-dev | inherit | 开发 page/component 四件套；接收测试报告后修正；维护 lessons-learned.md |
| wmp-tester-structure | haiku | 审查 WXML 节点、目录结构、JSON 配置、组件注册 |
| wmp-tester-style | haiku | 审查 WXSS 规范、rpx 适配、内置组件使用、交互体验 |
| wmp-tester-performance | haiku | 审查 setData、网络请求、本地存储、敏感信息泄漏 |

## 产出文件清单

每次执行后，OUTPUT_DIR 应包含：

```
{OUTPUT_DIR}/
├── app.json / app.js / app.wxss
├── project.config.json / sitemap.json
├── pages/{pageName}/{pageName}.{wxml,wxss,js,json}
├── components/{componentName}/{componentName}.{wxml,wxss,js,json}
├── utils/{request,storage,format}.js
├── dev-plan.md          # 主智能体维护，任务清单与状态
├── page-spec.md         # wmp-planner 产出，每个 page/component 的设计指引
├── lessons-learned.md   # wmp-dev 修正后追加经验
├── main-log.md          # 主智能体的运行日志（时间格式 yymmdd hhmm）
└── test-reports/
    ├── {NAME}-structure.md
    ├── {NAME}-style.md
    └── {NAME}-performance.md
```

## 主智能体启动

读取 [主智能体提示词.md](主智能体提示词.md) 全文，按其工作流程执行。

## 参考资料

- 设计理论与权衡：[笔记-非最新仅参考-多智能体协同-长时工作设计.md](笔记-非最新仅参考-多智能体协同-长时工作设计.md)
- 微信小程序官方文档：https://developers.weixin.qq.com/miniprogram/dev/framework/

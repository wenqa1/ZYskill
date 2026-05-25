---
name: wechat-miniprogram
description: "微信小程序 + 全栈 Web 多智能体开发系统。支持两种项目类型：(1) 微信小程序 —— WXML + WXSS + JS + JSON 四件套架构，WebView/Skyline 渲染引擎，WXS 脚本，组件高级特性，分包异步化，隐私合规；(2) 全栈 Web —— React 19 + TypeScript + Vite 8 + Tailwind CSS 4 前端 + Express 5 + TypeScript + Prisma ORM + SQLite/MySQL 后端 Monorepo。通过主智能体协调 wmp-*（小程序）和 fs-*（全栈）子Agent，分批完成开发与多维质量验证。当用户提到微信小程序、wxml/wxss/wxs、wx.request/wx.navigateTo、Page/Component、tabBar、app.json、Skyline 等触发小程序流程；当提到 React、Vite、Tailwind CSS、Express、Prisma、TypeScript、全栈 Web、RESTful API、Monorepo 等触发全栈流程。项目文件夹包含 app.json + project.config.json 时自动触发小程序；包含 prisma/schema.prisma 或 vite.config.ts 时自动触发全栈。"
version: 0.1.0
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Agent
---

# 微信小程序多智能体开发系统 — 入口

> 此文件是 skill 清单。**主智能体提示词**在 [主智能体提示词.md](主智能体提示词.md) 中，**子智能体定义**在 [agents/](agents/) 目录。

## 触发条件

技能被触发时，按以下顺序判断当前状态：

0. **优先读取 TODO.md** — 检查 [TODO.md](TODO.md) 中的待办事项。如有 P0/P1 级待办项，先处理（优化 skill 自身的文件），处理完成后更新 TODO.md 标记完成，再执行后续步骤。这是 skill 自优化的入口，每次触发都应检查
1. **存在 `dev-plan.md`** → 项目已经在进行中。读取 dev-plan.md 首行 `type:` 标记：
   - `type: wmp` → 微信小程序流程。读取 dev-plan.md，找出第一个 ⏳ 任务，进入 Phase 2 批量开发循环
   - `type: fullstack` → 全栈 Web 流程。读取 dev-plan.md，找出第一个 ⏳ 模块，进入全栈 Phase 2 循环
2. **探测项目目录**：
   - 存在 `app.json` + `project.config.json`（非 Monorepo）→ 微信小程序全新项目。按「主智能体提示词」的 WMP 初始化流程启动
   - 存在 `prisma/schema.prisma` 或 `vite.config.ts` 或 Monorepo `package.json`（含 workspaces 字段）→ 全栈 Web 全新项目。按全栈初始化流程启动
   - 目录为空或仅有需求文档 → 询问用户项目类型

> **注意**：TODO.md 的优化工作只作用于 skill 自身（即 `WeChat Mini Program/` 目录下的文件），不影响正在开发的小程序项目文件。

## 子智能体清单

### 微信小程序子智能体

| Agent | 模型 | 职责 |
|-------|------|------|
| wmp-planner | inherit | 阅读需求 → 拆任务 → 搭骨架（app.json/app.js/app.wxss/utils）→ 写 dev-plan.md & page-spec.md |
| wmp-dev | inherit | 开发 page/component 四件套；接收测试报告后修正；维护 lessons-learned.md |
| wmp-tester-structure | haiku | 审查 WXML 节点、目录结构、JSON 配置、组件注册 |
| wmp-tester-style | haiku | 审查 WXSS 规范、rpx 适配、内置组件使用、交互体验 |
| wmp-tester-performance | haiku | 审查 setData、网络请求、本地存储、敏感信息泄漏 |

### 全栈 Web 子智能体

| Agent | 模型 | 职责 |
|-------|------|------|
| fs-planner | inherit | 读需求 → 设计 DB/API/组件 → 搭 Monorepo 骨架（Vite+React+Express+Prisma+Tailwind）→ 写 dev-plan/api-spec/component-spec |
| fs-dev-backend | inherit | 后端开发：Express 5 + Prisma RESTful API、中间件、认证、数据库操作 |
| fs-dev-frontend | inherit | 前端开发：React 19 + Vite 8 + Tailwind CSS 4 页面与组件、Hooks、路由 |
| fs-tester-backend | haiku | 后端测试：API 路由正确性、Prisma 查询、输入验证、错误处理、安全 |
| fs-tester-frontend | haiku | 前端测试：组件完整性、TypeScript 类型、Tailwind 使用、响应式、API 集成 |

## 产出文件清单

### 微信小程序项目产出

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

### 全栈 Web 项目产出（Monorepo）

```
{OUTPUT_DIR}/
├── package.json                          # Monorepo 根（workspaces）
├── packages/
│   ├── client/                           # 前端 - React + Vite + Tailwind
│   │   ├── package.json
│   │   ├── vite.config.ts
│   │   ├── tsconfig.json
│   │   ├── index.html
│   │   └── src/
│   │       ├── main.tsx                  # 入口
│   │       ├── App.tsx                   # 路由配置
│   │       ├── pages/                    # 页面组件
│   │       ├── components/               # 共享 UI 组件
│   │       ├── hooks/                    # 自定义 Hooks
│   │       ├── api/                      # API 客户端函数
│   │       ├── types/                    # 共享类型定义
│   │       └── styles/index.css          # 全局样式（Tailwind 入口）
│   └── server/                           # 后端 - Express + Prisma
│       ├── package.json
│       ├── tsconfig.json
│       ├── .env
│       ├── prisma/
│       │   └── schema.prisma             # 数据模型定义
│       └── src/
│           ├── index.ts                  # 入口
│           ├── app.ts                    # Express 应用
│           ├── routes/                   # API 路由模块
│           ├── services/                 # 业务逻辑层
│           └── middleware/               # 中间件（auth, error 等）
├── dev-plan.md          # 开发计划（type: fullstack）
├── api-spec.md          # fs-planner 产出，API 接口契约
├── component-spec.md    # fs-planner 产出，前端组件设计指南
├── lessons-learned.md   # 经验库
├── main-log.md          # 主智能体运行日志
└── test-reports/
    ├── {module}-backend.md
    └── {module}-frontend.md
```

## 主智能体启动

读取 [主智能体提示词.md](主智能体提示词.md) 全文，按其工作流程执行。

## 参考资料

- 设计理论与权衡：[笔记-非最新仅参考-多智能体协同-长时工作设计.md](笔记-非最新仅参考-多智能体协同-长时工作设计.md)
- **Skill 自优化入口**：[TODO.md](TODO.md) — 待办事项清单，每次触发 skill 时优先读取
- 微信小程序官方文档：https://developers.weixin.qq.com/miniprogram/dev/framework/

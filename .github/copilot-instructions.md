# 🤖 Copilot/AI Agent Instructions for Solara

## 项目架构与核心组件
- 前端静态资源位于 `js/`、`css/`、`index.html`、`login.html`，核心播放器逻辑在 `js/index.js`。
- 后端代理与 API 聚合通过 Cloudflare Pages Functions 实现，主入口为 `functions/_middleware.ts`，API 路由在 `functions/api/`，音频代理在 `functions/proxy.ts`。
- 取色算法见 `functions/palette.ts`，用于动态主题。
- 所有 API 基地址在 `index.html` 的 `API.baseUrl` 处集中配置。

## Cloudflare Pages 部署与开发
- 推荐部署方式为 Cloudflare Pages，支持 Functions（Serverless）与静态资源托管。
- 若部署 Functions，需在根目录添加 `wrangler.json`，指定入口：
  ```json
  {
    "name": "solara-worker",
    "compatibility_date": "2025-11-27",
    "main": "functions/_middleware.ts"
  }
  ```
- 若仅部署静态站点，需先构建（如有），再用 `npx wrangler deploy --assets=./dist`。
- 本地开发可用 `npx wrangler dev` 预览 Functions 行为。

## 数据存储与 D1 数据库
- 支持 Cloudflare D1 数据库，绑定名为 `DB`，表结构见 `README.md`。
- 播放状态、收藏等优先写入 D1，未绑定时回退到浏览器 localStorage。
- 相关逻辑在 `functions/api/storage.ts`。

## 访问控制
- 通过 Cloudflare Pages 环境变量 `PASSWORD` 控制访问，未登录自动跳转 `/login`。
- 登录页逻辑在 `login.html`，校验逻辑在 `functions/_middleware.ts`。

## 约定与开发模式
- 移动端适配在 `js/mobile.js` 和 `css/mobile.css`，桌面端样式在 `css/desktop.css`。
- 探索雷达分类可在 `js/index.js` 的 `EXPLORE_RADAR_GENRES` 数组自定义。
- 所有 API 代理、跨域处理均通过 Cloudflare Functions 实现，避免前端直接请求第三方。

## 关键开发命令
- 本地预览 Functions: `npx wrangler dev`
- 部署到 Cloudflare: `npx wrangler deploy functions/_middleware.ts` 或 `npx wrangler deploy --assets=./dist`

## 参考文件
- `README.md`：详细功能、部署、数据库、访问控制说明
- `functions/_middleware.ts`：后端主入口与访问控制
- `functions/api/`：各曲库 API 代理
- `functions/proxy.ts`：音频直链代理
- `js/index.js`：播放器核心逻辑

---
如需自定义数据源、主题或功能，优先查阅上述文件。遵循现有 API 路由与状态管理模式，保持前后端解耦。
# 常用三方包对照（TS 前端 → Flutter）

这一章我会带你：把你常用的前端库/框架按“解决的问题”对位到 Flutter 生态，帮助你快速做技术选型。

## 1) 你提到的工具/库对位表

| TS/前端生态 | 解决的问题 | Flutter/Dart 对位（常见选择） | 备注 |
|---|---|---|---|
| React | 声明式 UI、组件化 | Flutter Widgets | 概念接近，但布局不是 CSS |
| Next.js | 路由/SSR/全栈框架 | go_router（路由）+ Flutter App | Flutter Web 可做站点，但移动端更常见 |
| TailwindCSS | tokens + utility + 响应式 | ThemeData/ThemeExtension + 自建 tokens + 封装组件 | 推荐“tokens → 组件库”路径 |
| SCSS | 样式组织/变量/混入 | tokens + 小组件/函数组合 | Flutter 没有选择器，但可编程样式更强 |
| shadcn/ui | 组件配方 + tokens | Material 3 + 自建 `AppButton/AppCard/...` | 重点是“可复制组件配方”，不是库本身 |
| Emotion | CSS-in-JS + Theme | Flutter 原生就是样式对象化 + Theme | 用 `ThemeExtension` 做自定义 tokens |
| nanoid | ID 生成 | `nanoid`（Dart 包）/`uuid` | 移动端离线创建记录很常用 |
| axios | HTTP client + 拦截器 | `dio` / `http` | `dio` 最像 axios |
| react-query | server state：缓存/刷新/重试 | Riverpod/BLoC + 自己定义缓存/失效；或 `cached_query`/`flutter_query` | 建议先用 Riverpod 跑通模式 |
| Biome | format + lint | `dart format` + `dart analyze` + lints | 用 `analysis_options.yaml` 固化规则 |

## 2) Flutter 项目常用基础包（工程化起手）

### 网络与数据

- `dio`：请求、拦截器、取消、上传下载
- `retrofit`（可选）：生成式 API client（更像 OpenAPI client）
- `freezed` + `json_serializable` + `build_runner`：不可变模型 + JSON + union

### 状态与路由

- `flutter_riverpod`（或 `hooks_riverpod`）：状态管理
- `go_router`：路由与深链

### UI 与体验

- `flutter_localizations` + `intl`：多语言与格式化
- `flutter_svg`：SVG
- `cached_network_image`：图片缓存

### 持久化（按复杂度递进）

- `shared_preferences`：轻量 KV
- `hive`：本地 NoSQL（简单好用）
- `sqflite`：SQLite（结构化更强）

## 3) 选型建议（偏 TS 开发者友好）

- 想要“像 react-query 一样写”：优先 Riverpod 的 `AsyncNotifier` 模式（把 query/mutation 收敛到 controller）。  
- 想要“像 shadcn 一样统一 UI”：先定 tokens（ThemeData/ThemeExtension），再封装组件（AppButton 等），最后拼页面。  
- 想要“像 Biome 一样一键质量检查”：把 `dart format`/`dart analyze`/`flutter test` 固化成 CI 的三连。  


# 如何使用本指南

我建议你按以下顺序来学习：**运行环境 / 配套代码 / 官方文档 / AI 辅助**

## 运行环境（你需要准备什么）

### Flutter/Dart 工具链

- Flutter SDK（建议用 FVM 固定版本，避免团队环境漂移）
- Android Studio / Xcode（移动端构建必需）
- VS Code（可选，但和前端工作流很像）

### 推荐的“前端式”工作流

- 我会像锁 `node` 版本一样锁 Flutter 版本（FVM）
- 我会像 `biome check` 一样固化质量命令：`dart format` / `dart analyze` / `flutter test`
- 我会像管理 `.env` 一样管理多环境：`--dart-define`

## 配套代码（如何练）

我会在书里大量使用：

- 语言级小例子（可复制到 DartPad / 本地 Dart 运行）
- Flutter 组件片段（可复制到 `main.dart` 快速验证）
- Todo 项目蓝图（第四部分）：把“主题/响应式/i18n/数据请求”串起来

如果你希望我把“第四部分 Todo 蓝图”落成可运行的两个项目目录（Next.js + Flutter），你告诉我选型（例如 Riverpod vs BLoC、是否做持久化），我就能直接生成脚手架代码。

## 查阅官方文档（别靠猜）

- Dart language tour：语法与类型系统（尤其空安全、patterns）
- Flutter docs：布局、渲染、状态、导航、i18n、性能
- 包文档：dio、riverpod、go_router、freezed 等

遇到“看起来像 JS、但行为不一样”的语法（例如 `late`、`const widget`、`switch pattern`），我建议你先查 language tour，少靠猜。

## AI 辅助学习（怎么问才有效）

你问 AI 的方式我建议尽量“工程化”：

- 给出目标 + 约束：例如“我要一个类似 react-query 的缓存与刷新模式，用 riverpod 怎么组织？”
- 给出你熟悉的类比：例如“Tailwind tokens 对应 Flutter 的什么？ThemeExtension 怎么写？”
- 给出失败症状：例如“Row 里 Text 溢出，为什么 Expanded 才能解决？”

把问题从“语法是什么”升级到“**在项目里怎么落**”，学习速度会明显提升。

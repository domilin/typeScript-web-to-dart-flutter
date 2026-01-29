# 关于本指南

## 目标

我不会把这本书写成“从零教编程”的教程——你已经是 TS/前端工程师了。我更想做的是：把你已经熟悉的工程经验（React/Next/Tailwind/Emotion、axios/react-query、Biome 等）**翻译**成 Dart/Flutter 的工程化能力：

- 语言层：语法、类型、空安全、异步、模式匹配
- 框架层：Widget 构建、布局系统、Theme、i18n、路由、状态管理
- 工程层：包管理、代码生成、测试、质量、CI、多环境配置

## 为什么要用“对照学习”

我知道你在 TS 世界已经形成了很多高效直觉，但其中一些在 Flutter 会“失效或变形”：

- CSS 布局直觉 → Flutter 的 constraints 布局模型
- 结构化类型（TS） → 名义化 + 健全空安全（Dart）
- hooks + react-query 的 data flow → Riverpod/BLoC 的 provider/controller 模式

所以我会坚持用对照写法：帮你更快定位 **同一件事，在 Flutter 为什么这么写**。

## 我这本书的“工程化偏置”

相较于纯语言教程，我会更强调：

- 目录组织（feature-first / clean-ish）
- 可测试性（unit/widget/golden）
- 可维护性（不可变数据、sealed 状态、错误归一化）
- 可扩展性（主题 token、多语言、响应式、组件库封装）

# TypeScript 开发者的 Dart/Flutter 工程化指南（对照学习版）

我把这本书写给：**React/Next.js/TypeScript 开发者**。如果你想系统掌握 **Dart 语言 + Flutter 工程化开发**，并且能在真实项目里快速做出下面这些能力，我希望这本书能帮到你：

- 响应式 UI（断点、自适应布局）
- 主题系统（浅色/深色/系统跟随 + design tokens）
- 多语言（intl/arb）
- 数据请求与缓存（dio + 状态管理，对位 axios + react-query）
- 组件体系（对位 shadcn/ui + Tailwind/SCSS/Emotion）
- 质量体系（对位 Biome：format/lint/test/CI）

我会用一个固定节奏带你学：**先解释（Dart/Flutter 的规则）→ 再对比（TS/React 的直觉）→ 最后落地（项目写法/坑位/练习）**。

## 版本假设

- TypeScript：5.x（偏现代写法：`satisfies`、更严格的 `unknown` 等）
- Dart：3.x（records、pattern matching、sealed class）
- Flutter：3.x（Material 3）

## 阅读方法（建议）

- 如果你想最快能做项目：我建议你先读第三部分的“工程化”与第四部分的 Todo 案例，再回头补第一/二部分的语言细节。
- 如果你想系统打基础：就按目录顺序读，配合每章末尾练习。

## 目录（书结构）

- 前言：`preface/01-about-this-guide.md`、`preface/02-how-to-use-this-guide.md`
- 第一部分 - Dart 基础（对照 TS）：`part-1-basics/00-index.md`
- 第二部分 - Dart 进阶（更贴项目）：`part-2-intermediate/00-index.md`
- 第三部分 - Dart/Flutter 高级与工程化（偏前端）：`part-3-advanced/00-index.md`
- 第四部分 - 综合案例（Todo）：`part-4-case-studies/00-index.md`
- 附录：`appendix/00-index.md`

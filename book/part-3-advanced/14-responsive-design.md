# 14｜响应式设计：Tailwind breakpoints ↔ Flutter breakpoints

这一章我会带你：建立一个可复制的 Flutter 响应式策略：断点、布局分裂点、组件级适配，以及避免“全局缩放”带来的灾难。

> 我建议你用两句对照来记：  
> - `MediaQuery` 更像“看浏览器视口”（viewport）  
> - `LayoutBuilder` 更像“做容器查询”（container query：看父容器给了你多大空间）  
>
> 断点不要贪多：先从 2 个断点开始（比如 640/1024），把布局骨架做对，再慢慢打磨细节。

## 概念解释（先讲策略）

### 1) Web 的响应式是“CSS 规则随宽度变化”

Tailwind 的 `sm/md/lg` 本质是：在不同宽度下启用不同 class。

### 2) Flutter 的响应式是“你自己决定布局”

常用信息源：

- `MediaQuery.sizeOf(context).width`
- `LayoutBuilder` 的 constraints

策略建议：

- 页面级：决定结构（单列/双列/sidebar/tab）
- 组件级：调整细节（padding/字号/行数）

## 一个可复用的断点工具（示意）

```dart
class Breakpoints {
  static const sm = 640.0;
  static const lg = 1024.0;
}

bool isSm(BuildContext context) => MediaQuery.sizeOf(context).width < Breakpoints.sm;
```

## 实战：页面级布局分裂

```dart
Widget build(BuildContext context) {
  final w = MediaQuery.sizeOf(context).width;
  if (w < 640) return const TodoPageSingleColumn();
  return const TodoPageTwoColumn();
}
```

## 我在项目里怎么做（避免常见误区）

- 不要把响应式做成“全局缩放比例”（把 320 当基准等比放大）：  
  - 字体/间距会在平板上显得很怪  
  - 可读性与可点击区域会出问题  
- 断点来自布局需求，不来自设备型号。  
- Flutter Web 与 mobile 的输入方式不同（鼠标 hover/滚轮 vs 触摸），响应式也包括交互差异。  

## 我给你留个练习

1) Todo 页面：<640 单列；>=640 左侧列表、右侧详情（master-detail）。  
2) 把按钮尺寸做响应式：小屏 `small`，大屏 `medium`，并保持触控可点击区域足够大。  

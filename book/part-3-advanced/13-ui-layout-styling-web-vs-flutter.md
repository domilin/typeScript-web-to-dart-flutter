# 13｜UI/布局/样式：Tailwind/SCSS/Emotion ↔ Flutter Widgets/Theme

这一章我会带你：把“写 CSS/布局”的前端直觉迁移到 Flutter 的 constraints 布局系统，并给出常见布局/样式的翻译表与实战片段。

> 我先给你 3 句“救命口诀”：  
> 1) Flutter 里 80% 的布局问题，本质都是 **constraints 不符合预期**。  
> 2) `Row/Column` 不会自动换行；需要换行就用 `Wrap`，需要滚动就用 `ListView/SingleChildScrollView`。  
> 3) `Row` 里长文本要么 `Expanded/Flexible`，要么就等着 `RenderFlex overflow`。  

## 概念解释（先讲关键差异）

### 1) CSS 布局 vs Flutter constraints

CSS：浏览器布局引擎负责排版，你更多是在声明规则。  
Flutter：父给子 constraints（最小/最大宽高），子决定 size，再回传给父。

很多 Flutter “布局不生效/溢出”问题本质是：你对 constraints 的预期不对。

如果你只记住一个流程，我希望是这个：

1) 父组件告诉子组件：**你最多/最少能多大**（constraints）  
2) 子组件在这个范围里选一个尺寸（size）  
3) 父组件把子组件放到某个位置（position）  
4) 如果子组件“硬要”超过上限，就会出现溢出/异常（你看到的各种红黄条、overflow 提示）  

为什么 `Expanded` 能救 `Row` 的溢出？你可以把它理解成：我告诉 `Row` ——“这块内容请你按剩余空间分配，并强制它在分到的空间里排版”。这和 Web 里 `flex: 1; min-width: 0;` 的组合直觉非常像。

### 2) 盒模型对照

| Web/CSS | Flutter |
|---|---|
| margin | 通常用外层 `Padding` / `SizedBox` 模拟间距（更显式） |
| padding | `Padding` / `Container(padding: ...)` |
| border/radius | `BoxDecoration(border: ..., borderRadius: ...)` |
| box-shadow | `BoxShadow` |
| gap | `SizedBox` / 自建 `Gap` |

## 常用布局翻译表（高频）

| CSS/HTML | Flutter |
|---|---|
| flex row/column | `Row` / `Column` |
| flex-grow | `Expanded` / `Flexible` |
| flex-wrap | `Wrap` |
| position: relative/absolute | `Stack` + `Positioned` |
| grid | `GridView` / `SliverGrid` |
| overflow: hidden | `ClipRRect` / `ClipRect` |
| text-overflow: ellipsis | `Text(maxLines: 1, overflow: TextOverflow.ellipsis)` |

## 实战片段（从 Tailwind 翻译到 Flutter）

### 1) 一行布局：checkbox + text + button（对位 flex + gap）

Web（Tailwind）：

```tsx
<div className="flex items-center gap-3 rounded-md border p-3">
  <input type="checkbox" />
  <p className="min-w-0 flex-1 truncate">title</p>
  <button>删除</button>
</div>
```

Flutter：

```dart
Container(
  padding: const EdgeInsets.all(12),
  decoration: BoxDecoration(
    border: Border.all(color: Theme.of(context).dividerColor),
    borderRadius: BorderRadius.circular(8),
  ),
  child: Row(
    children: [
      Checkbox(value: done, onChanged: (_) => onToggle()),
      const SizedBox(width: 12),
      Expanded(
        child: Text(
          title,
          maxLines: 1,
          overflow: TextOverflow.ellipsis,
        ),
      ),
      const SizedBox(width: 12),
      TextButton(onPressed: onDelete, child: const Text('删除')),
    ],
  ),
)
```

对照要点：

- `min-w-0 flex-1 truncate` ↔ `Expanded + maxLines + overflow`
- `gap-3` ↔ `SizedBox(width: 12)`

### 2) Grid 卡片（对位 CSS grid）

Flutter：

```dart
GridView.count(
  crossAxisCount: width < 640 ? 2 : 4,
  mainAxisSpacing: 12,
  crossAxisSpacing: 12,
  children: [
    for (final item in items) Card(child: Center(child: Text(item.title))),
  ],
)
```

## 样式组织：Tailwind/SCSS/Emotion 在 Flutter 里怎么“对位”

### 1) Tailwind：tokens + 组合效率

Flutter 的对位方案是：

- `ThemeData/ColorScheme/TextTheme`：颜色/字体 tokens
- `ThemeExtension<AppTokens>`：扩展 tokens（间距、圆角、阴影…）
- 自建组件（`AppButton/AppCard/...`）：对位 shadcn/ui 的“组件配方”

### 2) SCSS：复杂样式规则

Flutter 没有选择器，但你可以用“可编程样式”替代：

- 把复杂样式组合成函数/小组件
- 用 `ThemeExtension` 管变量

### 3) Emotion：CSS-in-JS

Flutter 本质上是“全量 CSS-in-code”，主题注入走 `Theme`，组合方式非常接近。

## 我给你留个练习

1) 把一个你熟悉的 shadcn Card + Button 组合，翻译成 Flutter 组件（包含圆角/边框/hover 的等价交互）。  
2) 做一个“长标题不溢出”的 TodoRow，要求：小屏一行截断，大屏显示两行。  

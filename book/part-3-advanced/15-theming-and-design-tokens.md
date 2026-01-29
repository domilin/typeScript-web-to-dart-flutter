# 15｜主题与设计 tokens：shadcn/tailwind 直觉在 Flutter 的落点

这一章我会带你：把你在 Web 端做主题（CSS variables、dark class、ThemeProvider）与组件体系（shadcn/tokens）的经验迁移到 Flutter：ThemeData + ThemeExtension + 自建组件库。

> 我建议你把 Flutter 主题当成三层：  
> 1) `ThemeData/ColorScheme/TextTheme`：框架内建 tokens（对位 shadcn 的基础色板/排版）  
> 2) `ThemeExtension<AppTokens>`：你自己的 tokens（间距、圆角、阴影、品牌色扩展）  
> 3) `AppButton/AppCard/...`：组件配方（对位 shadcn/ui 的组件实现）  
>
> 一个小自检：如果你在业务页面里到处写 `Colors.red`/`EdgeInsets.all(17)`，基本就说明 tokens 还没收敛好。

## 概念解释（先讲对位）

### 1) Web：tokens（CSS variables）驱动组件

shadcn/ui 的本质是：

- 一组可复制的组件实现
- 一组 tokens（颜色/间距/圆角/阴影…）
- 主题切换只是切换 tokens

### 2) Flutter：ThemeData/ColorScheme 是内建 tokens

你会把：

- 颜色系统：放在 `ColorScheme`
- 字体系统：放在 `TextTheme`
- 组件默认样式：放在 `ThemeData` 的组件主题（ButtonTheme 等）

你自定义的 tokens（间距、圆角、品牌色扩展）放在 `ThemeExtension`。

## 实例：最小主题切换

```dart
MaterialApp(
  theme: ThemeData(colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo)),
  darkTheme: ThemeData(
    colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo, brightness: Brightness.dark),
  ),
  themeMode: ThemeMode.system, // 用状态管理控制
)
```

## ThemeExtension：把 Tailwind 的 spacing/radius 变成强类型 tokens

```dart
@immutable
class AppTokens extends ThemeExtension<AppTokens> {
  const AppTokens({required this.radius, required this.gap});
  final double radius;
  final double gap;

  @override
  AppTokens copyWith({double? radius, double? gap}) {
    return AppTokens(radius: radius ?? this.radius, gap: gap ?? this.gap);
  }

  @override
  AppTokens lerp(ThemeExtension<AppTokens>? other, double t) {
    if (other is! AppTokens) return this;
    return AppTokens(
      radius: radius + (other.radius - radius) * t,
      gap: gap + (other.gap - gap) * t,
    );
  }
}
```

使用：

```dart
final tokens = Theme.of(context).extension<AppTokens>()!;
```

## 我在项目里怎么做（组件库怎么做像 shadcn）

推荐顺序：

1) tokens（ThemeData + ThemeExtension）  
2) 基础组件封装：`AppButton/AppTextField/AppCard`  
3) 业务组件组合：`TodoRow/TodoList`  

这样你得到的是“可演进的设计系统”，而不是一次性写死样式。

## 我给你留个练习

1) 实现 `AppCard`：统一圆角/边框/阴影，并支持 `variant`（outlined/filled）。  
2) 实现主题切换：系统/浅色/深色三态，并持久化到本地（shared_preferences）。  

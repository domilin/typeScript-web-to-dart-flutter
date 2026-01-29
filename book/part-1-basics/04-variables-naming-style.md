# 04｜变量命名规则与代码风格：对位 TS/ESLint 的“约定”

这一章我会带你：把 Dart 社区的命名/风格习惯一次性对齐，减少“看别人 Flutter 代码不顺眼”的摩擦。

## 我先把概念讲清楚

### 1) 命名风格（核心规则）

- 变量/函数：`lowerCamelCase`
- 类型（class/enum/typedef）：`UpperCamelCase`
- 文件名：`snake_case.dart`
- 私有（库内可见）：以下划线开头 `_name`

> 重点：Dart 的“私有”是以 **库（library / 文件）为边界** 的，不是 class-private。

### 2) 格式化与 lint 是“语言内建的一部分”

前端里你习惯：

- Prettier/Biome 统一格式
- ESLint/Biome 把风格固化

Dart/Flutter 里等价物是：

- `dart format`（格式化）
- `dart analyze` + lints（静态检查）
- `analysis_options.yaml`（规则入口）

## 然后我用对照例子讲

### 1) 私有可见性：TS 的 `private` vs Dart 的 `_`

TS：

```ts
class A {
  private secret = 1;
}
```

Dart：

```dart
class A {
  final int _secret = 1; // 仅在同一 library（通常是同文件）内可访问
}
```

### 2) 常量命名

TS 里你可能用 `UPPER_SNAKE_CASE` 表示常量；Dart 里更常见：

```dart
const maxRetries = 3;
```

UI 常量（例如间距）也建议 lowerCamelCase（配合静态分析更顺手）。

## 我在项目里怎么做（团队一致性）

- 用 `analysis_options.yaml` 固化规则；把 `dart format` 作为提交前固定步骤（pre-commit/CI）。
- 文件组织上，优先“按 feature 分目录”，并用 barrel export（`export '...'`）控制对外 API。

## 我给你留个练习

1) 给项目加 `analysis_options.yaml` 并 include `flutter_lints`。  
2) 在 CI（或本地脚本）里强制执行 `dart format --set-exit-if-changed .` + `dart analyze`。  


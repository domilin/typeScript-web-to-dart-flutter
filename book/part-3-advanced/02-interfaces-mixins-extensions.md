# 02｜接口、mixin、extension：Dart 的“组合能力”

这一章我会带你：对位 TS 的 `interface`/mixin 模式与工具函数，掌握 Dart 的 `abstract class/implements`、mixin、extension，并知道在 Flutter 项目里怎么用得“像前端工程”。

## 我先把概念讲清楚

### 1) interface：用 `abstract class` 表达契约

Dart 没有 TS 那种纯编译期 `interface`；通常用：

- `abstract class` 定义契约
- `implements` 强制实现

### 2) mixin：复用实现（受控多继承）

`mixin` 允许你把一段实现“贴”到多个类上（对位 TS mixin pattern），并且可以用 `on` 限制适用对象。

### 3) extension：给现有类型加方法（对位工具函数/原型扩展）

Flutter 项目里 extension 非常常见：

- `BuildContextX`：`context.textTheme`、`context.colors`
- `StringX`：`isBlank`、`capitalize`
- `AsyncValueX`：常用状态判断

> 经验法则：extension 用于提升可读性，但不要把业务逻辑塞进去（避免“到处都有魔法方法”）。

## 然后我用对照例子讲

### 1) TS interface ↔ Dart abstract class

TS：

```ts
interface Storage {
  get(key: string): Promise<string | null>;
  set(key: string, value: string): Promise<void>;
}
```

Dart：

```dart
abstract class Storage {
  Future<String?> get(String key);
  Future<void> set(String key, String value);
}
```

### 2) mixin：加 timestamps

```dart
mixin HasTimestamps {
  DateTime get createdAt;
}

class Todo with HasTimestamps {
  Todo(this.createdAt);
  @override
  final DateTime createdAt;
}
```

### 3) extension：BuildContext 快捷访问 Theme

```dart
import 'package:flutter/material.dart';

extension BuildContextX on BuildContext {
  TextTheme get text => Theme.of(this).textTheme;
  ColorScheme get colors => Theme.of(this).colorScheme;
}
```

使用时：

```dart
Text('Title', style: context.text.titleMedium);
```

对位 Emotion 的 `useTheme()`：把主题访问“语法级简化”。

## 我在项目里怎么做

- interface 用于“可替换实现”的边界（例如 storage、api、analytics），对位前端的 adapter/service。
- mixin 适合“无状态能力复用”（日志、序列化辅助、diagnostics），不要把复杂状态塞 mixin。
- extension 用于可读性（尤其 UI 层），但要控制范围：  
  - core 里统一定义（避免每个 feature 自己写一套 `context.xxx`）  
  - 命名清晰，避免冲突  

## 我给你留个练习

1) 写一个 `abstract class Clock { DateTime now(); }`，并在测试中注入 fake clock。  
2) 写一个 `extension StringX`：`String get orUntitled`（trim 后为空则返回 `'(untitled)'`）。  


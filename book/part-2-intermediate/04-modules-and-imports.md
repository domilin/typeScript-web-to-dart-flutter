# 04｜模块与导入：ESM ↔ Dart library（以及 `_private` 的边界）

这一章我会带你：掌握 Dart 的 import/export、选择性导入、别名、以及“库级私有”的边界规则。

## 我先把概念讲清楚

### 1) Dart 以“库（library）”为边界

通常一个 `.dart` 文件就是一个库。以下划线开头的标识符仅在同一库内可见：

```dart
final _secret = 1;
```

这和 TS 的 `private`（类成员级别）不一样。

### 2) import：package 与相对路径

```dart
import 'package:dio/dio.dart';
import '../core/http/http_client.dart';
```

项目中建议：

- 优先 `package:your_app/...` 形式导入（路径稳定）
- 少用 `../..`（易碎）

### 3) 选择性导入：`show` / `hide`

```dart
import 'package:some_lib/some_lib.dart' show Foo, Bar;
import 'package:some_lib/some_lib.dart' hide InternalThing;
```

### 4) 别名：`as`

```dart
import 'package:dio/dio.dart' as dio;
```

和 TS 的 `import * as X from '...'` 类似。

## 然后我用对照例子讲

TS：

```ts
import { nanoid } from "nanoid";
export type Todo = { id: string };
```

Dart：

```dart
import 'package:nanoid/nanoid.dart';

class Todo {
  const Todo({required this.id});
  final String id;
}
```

## 我在项目里怎么做（模块组织）

- barrel export：你可以像前端 `index.ts` 一样写一个 `todos.dart` 统一 export：

```dart
export 'domain/todo.dart';
export 'presentation/todo_page.dart';
```

- 但不要“过度 barrel”：过度会让依赖边界变糊、增量编译变慢；建议 feature 级别做聚合即可。

## 我给你留个练习

1) 在一个 feature 目录下创建 `todos.dart` 做 barrel export，并让外部只 import 这一处入口。  
2) 用 `show/hide` 控制你只导入需要的符号（模拟“公共 API vs 内部实现”）。  


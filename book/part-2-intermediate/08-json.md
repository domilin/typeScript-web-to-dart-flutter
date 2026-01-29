# 08｜JSON：`JSON.parse` ↔ `dart:convert`（以及生成式模型）

这一章我会带你：把“运行时 JSON → 强类型模型”的工程化路径讲清楚：手写解析、生成工具、以及边界校验。

## 我先把概念讲清楚

### 1) Dart 的 JSON 解析是两段式

- `jsonDecode(String)`：把字符串解析成 `dynamic`（通常是 `Map/List`）
- 你需要手动把 `dynamic` 转成强类型（或用生成工具）

### 2) 推荐的边界类型

- `Map<String, Object?>`（推荐）
- `List<Object?>`

避免在业务层传 `dynamic`。

## 然后我用对照例子讲

### 1) TS：JSON.parse（但类型不安全）

```ts
const obj = JSON.parse(text) as unknown;
// 通常你会加 zod：schema.parse(obj)
```

### 2) Dart：jsonDecode + 手写解析

```dart
import 'dart:convert';

Todo parseTodo(String text) {
  final obj = jsonDecode(text) as Map<String, Object?>;
  return Todo.fromJson(obj);
}
```

### 3) 工程化：json_serializable/freezed（推荐用于真实项目）

你会得到：

- `fromJson/toJson`
- 不可变数据类
- `copyWith`
-（可选）sealed union（表达状态/结果）

## 我在项目里怎么做（对位 TS：Zod + 类型）

- TS 常见组合：OpenAPI/ts types + Zod 运行时校验  
- Flutter 常见组合：生成模型 + 自定义校验（或在 repository 层做容错/默认值）  

“类型正确”与“数据可信”是两件事：Dart 类型再强，也不能替代后端数据校验；你仍需要边界层防御式解析。

## 我给你留个练习

1) 写一个 `Todo.fromJson`：对缺失字段做默认值，对类型错误抛异常。  
2) 写一个 `List<Todo> parseTodos(String text)`：解析数组，并忽略解析失败的元素（统计失败数量）。  


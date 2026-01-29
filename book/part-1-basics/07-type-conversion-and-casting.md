# 07｜类型转换与断言：TS 的 `as` 不是 Dart 的 `as`

这一章我会带你：把“类型断言/转换”的风险点讲透：在 Dart 里很多操作是运行时发生的，一旦写错会直接 crash。

## 我先把概念讲清楚

### 1) TS：`as` 多数是编译期断言

```ts
const x = something as Foo; // 可能并没有任何运行时检查
```

### 2) Dart：`as` 是运行时 cast（失败会抛异常）

```dart
final foo = x as Foo; // 运行时检查，失败抛 TypeError
```

### 3) 推荐优先用 `is` 收窄

```dart
if (x is Foo) {
  // 这里 x 被收窄为 Foo
}
```

## 然后我用对照例子讲

### 1) 字符串转数字

TS：

```ts
const n = Number.parseInt("42", 10);
```

Dart：

```dart
final n = int.parse('42');
final d = double.parse('3.14');
```

安全版：

```dart
final n = int.tryParse('x'); // -> null
```

### 2) JSON（动态）到强类型

TS（常见）：

```ts
const dto = res.data as any; // 或 unknown + zod
```

Dart（常见边界类型）：

```dart
final json = res.data as Map<String, Object?>;
final id = json['id'] as String;
```

更稳的写法是：对每个字段做校验/默认值（或用生成工具 + 自定义校验层）。

### 3) 集合强转：`cast<T>()`

```dart
final xs = <dynamic>[1, 2, 3];
final ints = xs.cast<int>(); // 运行时会检查元素类型
```

## 我在项目里怎么做（真实项目怎么做）

- DTO → Domain：像你在 TS 里用 Zod 一样，Dart 也需要“边界校验”。  
  - 轻量：手写解析 + `tryParse` + 默认值  
  - 工程化：`json_serializable` 生成 + 自定义校验/转换  
- 不要让 `dynamic` 进入 UI 层：它会把 “类型错误” 变成 “用户点击后崩溃”。  

## 我给你留个练习

1) 写一个 `Todo fromJson(Map<String, Object?> json)`：对 `title` 做空字符串兜底，对 `done` 缺省兜底为 `false`。  
2) 把 `List<dynamic>` 安全地转换成 `List<String>`（忽略非 string 元素）。  


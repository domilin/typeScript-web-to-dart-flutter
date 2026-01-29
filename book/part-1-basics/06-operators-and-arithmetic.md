# 06｜运算符与算术：没有 `===`，以及 `??`/`?.` 的正确使用

这一章我会带你：补齐 Dart 运算符的“关键差异点”，避免把 JS 直觉硬套过来。

## 我先把概念讲清楚

### 1) 相等：Dart 没有 `===`

JS/TS：

- `==`：宽松相等（会做类型转换，不推荐）
- `===`：严格相等（推荐）

Dart：

- `==`：调用对象的相等语义（可被重写），通常用于“值相等”
- `identical(a, b)`：同一引用（更接近 JS 的 `===` 在“引用层面”的直觉）

> 经验法则：业务判断用 `==`；需要判断“是不是同一个对象”用 `identical`。

### 2) 除法：`/` 返回 double，整除用 `~/`

```dart
5 / 2   // 2.5
5 ~/ 2  // 2
```

### 3) 空安全运算符：`?. ?? ??= !`

- `a?.b`：a 为 null 时返回 null
- `a ?? b`：a 为 null 用 b
- `a ??= b`：a 为 null 时赋值
- `a!`：断言非空（把风险推到运行时，慎用）

## 然后我用对照例子讲

### 1) `??` 与默认值

TS：

```ts
const title = input ?? "untitled";
```

Dart：

```dart
final title = input ?? 'untitled';
```

### 2) 可选链

TS：

```ts
const len = user?.profile?.name?.length ?? 0;
```

Dart：

```dart
final len = user?.profile?.name?.length ?? 0;
```

### 3) 类型测试与转换：`is`/`as`

TS：

```ts
if (typeof x === "string") x.toUpperCase();
```

Dart：

```dart
if (x is String) x.toUpperCase();
```

注意：Dart 的 `as` 是运行时 cast（失败会抛异常），TS 的 `as` 多数时候只是编译期断言。

## 我在项目里怎么做（Flutter 常见坑）

- `a!` 用多了等价于“把 null bug 留到线上”：优先用显式分支或 `??` 默认值。
- `==` 的语义可被重写：如果你用不可变模型（例如 `freezed`），它会生成正确的值相等；否则 class 默认是引用相等（你会发现 list 更新/去重很奇怪）。

## 我给你留个练习

1) 写一个 `int safeDivide(int a, int b)`：当 b 为 0 返回 0，否则返回 `a ~/ b`。  
2) 写一个 `String? safeTrim(String? s)`：null 直接返回 null，否则返回 `trim()`。  


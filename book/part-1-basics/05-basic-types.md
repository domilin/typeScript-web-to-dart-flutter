# 05｜基本数据类型：TS 的 `number/string/boolean` ↔ Dart 的 `int/double/String/bool`

这一章我会带你：覆盖你在日常业务里 90% 会用到的 Dart 基本类型与“类型边界”。

## 我先把概念讲清楚

### 1) 数字类型：Dart 把 number 拆成 `int/double`

- `int`：整数
- `double`：浮点数
- `num`：`int | double` 的父类型（注意：不是 TS 的联合类型，是继承体系）

### 2) `Object` / `dynamic`：对位 TS 的 `unknown` / `any`

- `dynamic`：像 `any`，会把错误推迟到运行时（谨慎用）
- `Object`：像 `unknown`，你必须先收窄（推荐）

### 3) `Null` / `Never`：理解空安全与“不会返回”

- `Null`：空值的类型（但你通常只在泛型/类型推导里间接看到）
- `Never`：不会返回（抛异常/死循环）

## 然后我用对照例子讲

### 1) 数字

TS：

```ts
let a: number = 1;
let b = 1.5;
```

Dart：

```dart
int a = 1;
double b = 1.5;
num c = 1;     // 也可以是 1.5
```

### 2) 字符串（插值）

TS：

```ts
const name = "Ada";
const s = `hi ${name}`;
```

Dart：

```dart
final name = 'Ada';
final s = 'hi $name';
final s2 = 'len=${name.length}';
```

### 3) `unknown/any` vs `Object/dynamic`

TS：

```ts
let x: unknown = 1;
// x.toFixed(2) // ❌
if (typeof x === "number") x.toFixed(2);
```

Dart：

```dart
Object x = 1;
// x.toStringAsFixed(2); // ❌
if (x is num) {
  x.toStringAsFixed(2);
}
```

## 我在项目里怎么做（边界类型的选择）

- API/JSON 边界：优先用 `Object?`/`Map<String, Object?>`，然后显式解析到强类型模型。
- 业务层：尽量避免 `dynamic`，否则你会在 UI 层踩到运行时错误（比 TS 更痛，因为 UI 线程直接报错影响体验）。

## 我给你留个练习

1) 写一个 `formatCount(Object value)`：当 value 是 `num` 时输出两位小数，否则输出 `'-'`。  
2) 给一个 JSON（Map）写最小解析：把 `createdAt` 的 ISO 字符串转成 `DateTime`。  


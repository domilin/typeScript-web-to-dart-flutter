# 01｜函数的定义与调用：签名、返回值与表达式函数

这一章我会带你：把你在 TS 里写函数（类型标注、默认值、重载、工具函数）的经验迁移到 Dart 的函数体系里。

## 我先把概念讲清楚

### 1) Dart 的函数是“一等公民”

你可以把函数当参数传递、当返回值返回（高阶函数），语法上和 TS 很接近。

### 2) 返回类型与类型推导

- Dart 可以显式写返回类型：`String f() { ... }`
- 也可以依赖推导：`final f = (int x) => x + 1;`

在公共 API（库/模块对外暴露）里更推荐显式类型，和 TS 导出类型的习惯一致。

### 3) `=>` 表达式函数

当函数体只有一个表达式时常用：

```dart
int add(int a, int b) => a + b;
```

和 TS 的箭头函数非常像，但 Dart 的 `=>` 只用于“单表达式返回”。

## 然后我用对照例子讲

### 1) 普通函数

TS：

```ts
export function add(a: number, b: number): number {
  return a + b;
}
```

Dart：

```dart
int add(int a, int b) {
  return a + b;
}
```

### 2) 表达式函数（对位箭头函数）

TS：

```ts
const add = (a: number, b: number) => a + b;
```

Dart：

```dart
int add(int a, int b) => a + b;
```

### 3) 可选返回（对位 `T | null`）

TS：

```ts
function first<T>(xs: T[]): T | null {
  return xs.length ? xs[0] : null;
}
```

Dart：

```dart
T? first<T>(List<T> xs) {
  return xs.isEmpty ? null : xs.first;
}
```

### 4) “函数重载”怎么办？

TS：

```ts
function f(x: string): string;
function f(x: number): number;
function f(x: string | number) {
  return x;
}
```

Dart 没有同名重载；常见替代：

- 命名参数/可选参数（见下一章）
- `Object` + `is`/pattern matching
- 分两个不同命名的函数

## 我在项目里怎么做（Flutter）

- UI 层的 helper 尽量写成纯函数（无副作用），便于测试与复用（对位你在 React 里抽 util/hook）。
- 公共库函数尽量显式标注返回类型，减少推导带来的“签名漂移”。

## 我给你留个练习

1) 写一个 `String normalizeTitle(String input)`：trim、把连续空格压成一个空格。  
2) 写一个 `T? findById<T extends HasId>(List<T> xs, String id)`（定义 `HasId` 接口）。  


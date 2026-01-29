# 02｜匿名函数与闭包：不用再担心 `this` 绑定

这一章我会带你：掌握 Dart 的匿名函数、闭包捕获、函数引用（tear-off），并对位 TS/JS 常见陷阱（`this`、bind）。

## 我先把概念讲清楚

### 1) 匿名函数

```dart
(int x) => x + 1
```

也可以写成块体：

```dart
(int x) {
  return x + 1;
}
```

### 2) 闭包捕获是“词法作用域”

Dart 闭包捕获变量的行为更接近你在 TS 里写箭头函数的直觉：不会出现 JS 那种“函数作为回调时 this 变了”的问题。

### 3) tear-off：把方法当函数传（语法更干净）

```dart
final lengths = names.map((s) => s.length).toList();
// 可以写成：
final lengths2 = names.map((s) => s.length).toList();
```

对方法/构造器，也可以：

```dart
final todos = jsonList.map(Todo.fromJson).toList();
```

## 然后我用对照例子讲

### 1) 高阶函数：map

TS：

```ts
const lens = names.map((s) => s.length);
```

Dart：

```dart
final lens = names.map((s) => s.length).toList();
```

### 2) 闭包捕获

TS：

```ts
function makeAdder(a: number) {
  return (b: number) => a + b;
}
```

Dart：

```dart
int Function(int) makeAdder(int a) {
  return (int b) => a + b;
}
```

### 3) `this` 直觉对齐

在 TS/JS 里你会写：

```ts
class Counter {
  n = 0;
  inc = () => { this.n += 1 };
}
```

Dart 里通常不需要这种“用箭头锁 this”的写法；闭包不会改变 `this`（而且 Flutter 更推荐不可变数据与明确状态更新）。

## 我在项目里怎么做

- 避免在 UI build 里创建复杂闭包链（大量 `.map(...).where(...)`）；可以提到 build 外或提取小 widget，减少 rebuild 压力。
- 想要“hook 风格”的组合能力时，把闭包放在 controller/provider 层（第三部分会讲 riverpod 的组织方式）。

## 我给你留个练习

1) 写一个 `makeFilter(String keyword)`：返回一个函数，用于过滤 todo title 是否包含 keyword（忽略大小写）。  
2) 在 Flutter 里写一个 `ValueNotifier<int>` 的 demo：按钮点击时更新值，并用 `ValueListenableBuilder` 渲染。  


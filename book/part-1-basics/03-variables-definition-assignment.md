# 03｜变量的定义与赋值：`let/const` ↔ `var/final/const`

这一章我想先给你一个“够用一辈子”的选择规则：**默认用 `final`，需要重新赋值才用 `var`，能写 `const` 就尽量写（尤其是 Widget）**。然后我会把 `var/final/const/late` 的区别讲清楚，并用 TS 的 `let/const` 做对照。

> 你先记住一句话：TS 的 `const` 更像“不可重新赋值的引用”，而 Dart 的 `const` 更像“编译期常量对象”（很多时候也意味着不可变）。

## 我先把概念讲清楚

### 1) Dart 的三种常用声明

- `var`：类型推导 + 可重新赋值
- `final`：只能赋值一次（引用不可变），值可以在运行时确定
- `const`：编译期常量（值必须能在编译期求出），并且会创建“常量对象”

我在项目里会这么用：

- 业务代码 80%：`final`
- 需要累加/重赋值：`var`
- UI/常量配置：能 `const` 就 `const`（Flutter 里能减少重建时的对象创建）

一个很重要的小细节：`var` 只有在**有初始化值**时才会推导出一个“固定类型”。如果你写：

```dart
var x; // 没初始化 -> dynamic（很像 TS 的 any）
```

那你就等于把类型系统关掉了一半；我建议你尽量避免这种写法。

### 2) `final` vs `const`：用“能不能等到运行时”来区分

```dart
final now = DateTime.now(); // ✅ 运行时才能知道
// const now = DateTime.now(); // ❌ 不是编译期常量

const pageSize = 20; // ✅ 编译期就能确定
```

### 2) `late`：我保证稍后初始化

Dart 的空安全要求非空变量必须立即初始化；`late` 允许延迟初始化，但它不是“可空”，而是“你承诺一定会赋值”：

```dart
late final String token;
```

如果你在赋值前读取，会抛 `LateInitializationError`。

我常见的使用场景是：

- 依赖注入：对象需要在 `main()` 或启动流程后才能拿到
- Flutter 生命周期：某些值要等到 `initState`/`didChangeDependencies` 才能初始化

但我也会提醒你：`late` 不是免费的，它只是把“必须初始化”的压力从编译期推到运行时，所以要用得克制。

## 然后我们用对照例子来加深直觉

### 1) 可变 vs 不可变引用

TS：

```ts
let count = 0;
count += 1;

const baseUrl = "/api";
// baseUrl = "/x"; // ❌
```

Dart：

```dart
var count = 0;
count += 1;

final baseUrl = '/api';
// baseUrl = '/x'; // ❌
```

### 2) `const` 的差异：TS `const` 不是编译期常量对象

TS：

```ts
const obj = { x: 1 };
obj.x = 2; // ✅
```

Dart：

```dart
const obj = {'x': 1};
// obj['x'] = 2; // ❌ const 集合不可变
```

（Dart 里 `const` 集合字面量是不可变的；如果你写 `final obj = {'x': 1};` 则仍可修改内容。）

### 3) 类型推导

TS：

```ts
const title = "todo"; // string
```

Dart：

```dart
final title = 'todo'; // String
```

## 我在 Flutter 项目里怎么落地

- `final` 用得越多，你越容易写出“不可变数据 + 明确更新路径”的代码（可维护性更高）。
- Widget 构造尽量使用 `const`：例如 `const SizedBox(height: 12)`、`const Text('...')`。

## 常见坑（我帮你提前踩掉）

1) 把 `const` 当成 TS 的 `const`：Dart 的 `const` 约束更强，很多值（比如 `DateTime.now()`）根本不能 `const`。  
2) 过度使用 `late`：它会让空安全失去意义，最后变成运行时崩溃。  
3) 写 `var x;`：这会让 `x` 变成 `dynamic`，等价于把 TS 的 `any` 放进业务代码。  

## 我给你留两个小练习

1) 把一个已有 Flutter 页面里的所有局部变量尽量改成 `final`，只保留必须可变的 `var`。  
2) 找 3 个可以写成 `const` 的 widget，并改成 `const`，观察是否影响行为。  

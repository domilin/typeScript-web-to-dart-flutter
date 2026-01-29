# 02｜第一个程序：Dart 控制台 vs Flutter App

这一章我会带你：建立“入口函数、运行方式、热重载、UI 入口”的最小心智模型。

## 我先把概念讲清楚

### 1) Dart 程序入口

Dart 的入口是 `main()`：

```dart
void main() {}
```

### 2) Flutter 程序入口

Flutter 的入口仍是 `main()`，但通常会调用 `runApp()` 把一个 Widget 树挂到渲染管线：

```dart
void main() => runApp(const MyApp());
```

对照 React：你可以把 `runApp(<App />)` 类比为把根组件挂到 DOM，但 Flutter 没有 DOM/HTML/CSS，布局与绘制由 Flutter 自己完成。

### 3) 热重载（Hot Reload）

前端的 HMR（Hot Module Replacement）解决的是 JS 模块替换；Flutter 的 hot reload 更像“在 Dart VM 上增量更新代码并尽量保留状态”。

## 然后我用对照例子讲

### 1) Hello World（控制台）

TS：

```ts
console.log("Hello TypeScript");
```

Dart：

```dart
void main() {
  print('Hello Dart');
}
```

### 2) Hello World（UI）

React：

```tsx
export default function App() {
  return <div>Hello React</div>;
}
```

Flutter：

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: Scaffold(body: Center(child: Text('Hello Flutter'))),
    );
  }
}
```

## 我在项目里怎么做（你会遇到的第一批问题）

### 1) `const` 在 Flutter 里“很有用”

在 Flutter 里写 `const Widget(...)` 能让框架在重建时复用对象（减少不必要的实例创建），这点和 TS 里写 `const` 并不一样。

### 2) 你会很快遇到“布局溢出”

第一次写 Flutter UI，你很可能遇到：

- `RenderFlex overflowed`（Row/Column 里内容超出）

这不是 CSS 的问题，而是 Flutter constraints 模型的问题。第三部分会专门讲“Row/Column + Expanded 的正确心智”。

## 我给你留个练习

1) 用 Dart 写一个小程序：读取一个字符串，输出去空格后的长度（不用任何 package）。  
2) 用 Flutter 写一个页面：中间显示一行文字，下面一个按钮，点击后文字计数 +1（只用 `setState`）。  


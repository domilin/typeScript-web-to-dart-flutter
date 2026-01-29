# 05｜Stream 与取消：对位 RxJS/事件流（Flutter 很常用）

这一章我会带你：掌握 Dart `Stream` 的基础与常用模式：订阅/取消、变换、`async*`，并对位前端常见：RxJS、EventEmitter、WebSocket。

## 我先把概念讲清楚

### 1) Stream：多次异步事件

- `Future<T>`：一次性结果
- `Stream<T>`：一串事件（0..n 次）

### 2) 订阅与取消

```dart
final sub = stream.listen((event) {});
await sub.cancel();
```

取消是 Stream 的核心能力之一（对位 RxJS subscription）。

### 3) `async*`：异步生成 stream

```dart
Stream<int> ticker() async* { ... }
```

### 4) 单订阅 vs 广播

- single-subscription：默认，只能一个 listener
- broadcast：允许多个 listener（类似多播事件）

## 然后我用对照例子讲

### 1) async generator（TS） ↔ async*（Dart）

TS：

```ts
async function* ticker() {
  let i = 0;
  while (true) {
    await new Promise(r => setTimeout(r, 1000));
    yield i++;
  }
}
```

Dart：

```dart
Stream<int> ticker() async* {
  var i = 0;
  while (true) {
    await Future<void>.delayed(const Duration(seconds: 1));
    yield i++;
  }
}
```

### 2) debounce（概念对照）

前端里你可能用 RxJS：`input$.pipe(debounceTime(300))`。  
Dart 标准库没有内建 debounce，但常见做法是：

- 用 `Timer` 在业务层做 debounce
- 或引入 `rxdart`（如果你团队接受 Rx）

## 我在项目里怎么做（Flutter）

- UI 层直接消费 Stream：`StreamBuilder`  
- 更工程化：状态管理层把 Stream 包装成 state（Riverpod 的 stream provider / BLoC 的 stream）  

## 我给你留个练习

1) 写一个 `Stream<int>`：每 200ms emit 一次计数，5 次后结束。  
2) 在 Flutter 里用 `StreamBuilder` 渲染这个计数，并实现一个“停止”按钮（cancel subscription）。  


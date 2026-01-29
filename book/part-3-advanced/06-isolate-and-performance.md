# 06｜Isolate 与性能：对位 Web Worker（避免卡 UI）

这一章我会带你：理解 Flutter “掉帧/卡顿”的根因，并掌握把 CPU 密集任务挪到后台的方式：Isolate。

## 我先把概念讲清楚

### 1) UI isolate：主线程的等价物

Flutter 的 UI 运行在主 isolate 上；如果你在这里做重计算，会导致：

- 掉帧（jank）
- 滚动卡顿
- 动画不流畅

### 2) Isolate：Dart 的并行单位

Isolate 之间不共享内存，通过消息传递通信（和 Web Worker 类似）。

### 3) 何时需要 Isolate

常见触发点：

- 大 JSON 解析
- 图片/文件处理
- 大量排序/聚合计算

## 然后我用对照例子讲

### 1) Isolate.run（最直接）

```dart
import 'dart:isolate';

int fib(int n) => n <= 1 ? n : fib(n - 1) + fib(n - 2);

Future<int> fibInBg(int n) {
  return Isolate.run(() => fib(n));
}
```

### 2) Flutter 的 `compute`（常用）

Flutter 提供 `compute(fn, message)` 帮你把简单函数丢到后台执行（适合 JSON 解析这类纯函数任务）。

## 我在项目里怎么做（性能诊断）

- 先用 Flutter DevTools 看时间线（UI/CPU），确认瓶颈再上 Isolate。
- 优先优化算法/减少重复计算；Isolate 是“把负担挪走”，不是“免费加速”。

## 我给你留个练习

1) 写一个 JSON 解析函数：把 1 万条 todo 的 JSON 字符串解析成 list，分别在主 isolate 与后台 isolate 测试耗时。  
2) 把一个“过滤 + 排序 + 分组”的计算挪到 isolate，并在 UI 上显示计算进度/结果。  


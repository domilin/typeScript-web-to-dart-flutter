# 10｜math：`Math` ↔ `dart:math`（random、min/max、几何）

这一章我会带你：覆盖你在业务/动画/布局计算里常见的数学工具：随机数、范围裁剪、幂运算等。

## 我先把概念讲清楚

### 1) `dart:math`

常用：

- `min/max`
- `pow`
- `Random`

## 然后我用对照例子讲

TS：

```ts
const x = Math.min(10, 20);
const r = Math.random();
```

Dart：

```dart
import 'dart:math' as math;

final x = math.min(10, 20);
final r = math.Random().nextDouble();
```

范围裁剪（clamp）在 Dart 里很常用：

```dart
final v = 120.clamp(0, 100); // -> 100
```

## 我在项目里怎么做（UI 相关）

- 响应式/动画里经常需要 `clamp`（避免超出范围）。
- 随机数不要用于安全用途；生成 id 推荐 nanoid/uuid（见第四部分 Todo）。

## 我给你留个练习

1) 写一个 `double lerp(double a, double b, double t)`（线性插值，t 需要 clamp 到 0..1）。  
2) 写一个函数：把屏幕宽度映射成 padding（最小 12，最大 24），宽度从 320 到 1024 线性变化。  


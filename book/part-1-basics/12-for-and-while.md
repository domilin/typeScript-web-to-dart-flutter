# 12｜for / while：循环与遍历（对位 `for-of`）

这一章我会带你：掌握 Dart 的遍历方式，并理解与 TS/JS 的 `for-of/for-in` 的差异。

## 我先把概念讲清楚

### 1) `for-in` 遍历集合（高频）

```dart
for (final x in xs) {
  print(x);
}
```

### 2) 经典 for（需要 index 时）

```dart
for (var i = 0; i < xs.length; i++) {
  print(xs[i]);
}
```

### 3) `while/do-while`

```dart
while (cond) {}
do {} while (cond);
```

## 然后我用对照例子讲

TS：

```ts
for (const x of xs) console.log(x);
```

Dart：

```dart
for (final x in xs) print(x);
```

## 我在项目里怎么做（避免 UI 里写复杂循环）

Flutter UI 更推荐：

- 用 `for (...)` 写在 collection literal 里（见第 13 章）
- 或把循环逻辑提到 build 之外（类似把 JSX 里复杂 map 抽成 `useMemo`/helper）

## 我给你留个练习

1) 写一个函数：把 `List<Todo>` 分成 `done` 与 `active` 两个 list（返回 record 或 Map）。  
2) 用循环生成 50 个假数据 Todo（id 用 nanoid/uuid 的思路），用于 UI 渲染。  


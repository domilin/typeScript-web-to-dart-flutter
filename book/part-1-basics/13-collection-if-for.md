# 13｜集合字面量里的 if/for：对位 React 的条件渲染与列表渲染

这一章我会带你：掌握 Dart 的一个“非常工程化”的能力：在 `List/Set/Map` 字面量里写 `if`/`for`，这会直接改变你写 Flutter UI 的方式。

## 我先把概念讲清楚

### 1) Dart 支持 collection-if / collection-for

```dart
final xs = [
  if (cond) 1,
  for (final n in numbers) n * 2,
];
```

### 2) 展开（spread）与空安全展开（null-aware spread）

```dart
final xs = [...a, ...?maybeB];
```

## 然后我用对照例子讲

### 1) React：条件 + map

```tsx
return (
  <div>
    {items.length === 0 && <Empty />}
    {items.map((i) => (
      <Row key={i.id} item={i} />
    ))}
  </div>
);
```

### 2) Flutter：children 里直接写 if/for

```dart
return Column(
  children: [
    if (items.isEmpty) const Empty(),
    for (final i in items) TodoRow(item: i),
  ],
);
```

### 3) Map 字面量也支持（生成索引/映射很方便）

```dart
final byId = <String, Todo>{
  for (final t in todos) t.id: t,
};
```

## 我在项目里怎么做（Flutter 写 UI 的“默认姿势”）

把它当成 Flutter 的“语法级 JSX 辅助”：

- 少写 `.map(...).toList()` 嵌在 widget 树里
- 少写三元把 UI 逻辑塞得很碎
- 多用 `if/for` 把结构写直观

同时注意：如果 `items` 很大，不要用 `Column(children: [...])`；该用 `ListView.builder`。

## 我给你留个练习

1) 写一个 Flutter Widget：当 `filter=active/done/all` 时，`children` 用 collection-if/for 渲染不同集合。  
2) 写一个 `Map<String, Widget>` 的字面量，用来把 `enum Filter` 映射到不同按钮组件（体验一下 map 里的 `for`/`if`）。  


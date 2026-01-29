# 08｜List：Array 的平替，但更“强类型”

这一章我会带你：覆盖 Dart `List` 的高频用法，并对位你在 TS 里写数组处理的习惯。

## 我先把概念讲清楚

### 1) `List<T>` 是泛型容器

和 TS 的 `T[]` 对应，但更严格；如果你滥用 `dynamic`，会失去优势。

### 2) 字面量、展开与控制流

Dart list 字面量支持：

- 展开：`...items` / `...?items`
- `if`：`[if (cond) x]`
- `for`：`[for (final x in xs) f(x)]`

这对 Flutter UI 组合非常关键（对位 React 的条件渲染与列表渲染）。

## 然后我用对照例子讲

### 1) 创建与追加

TS：

```ts
const xs: number[] = [1, 2];
xs.push(3);
```

Dart：

```dart
final xs = <int>[1, 2];
xs.add(3);
```

### 2) map/filter/reduce

TS：

```ts
const titles = todos.filter(t => !t.done).map(t => t.title.trim());
```

Dart：

```dart
final titles = todos
    .where((t) => !t.done)
    .map((t) => t.title.trim())
    .toList();
```

### 3) 不可变（运行时防改）

```dart
final safe = List.unmodifiable(xs);
```

### 4) 展开与空安全展开

```dart
final all = [...a, ...b];
final all2 = [...a, ...?maybeB];
```

## 我在项目里怎么做（Flutter 列表渲染）

- 大列表优先 `ListView.builder`（懒构建），不要一次性把 `children: [...]` 堆成几千个 widget。
- 对位 React 的“key”：Flutter 列表里如果涉及重排/动画/可编辑项，考虑使用 `Key`（例如 `ValueKey(todo.id)`）保证稳定性。

## 我给你留个练习

1) 实现 `List<Todo> toggle(List<Todo> todos, String id)`：返回新 list（不可变更新），只切换匹配项的 done。  
2) 写一个 Flutter `ListView.builder`，渲染 1000 行文本，并确保滚动流畅。  


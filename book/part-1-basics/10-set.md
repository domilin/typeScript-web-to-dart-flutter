# 10｜Set：去重、集合运算与性能直觉

这一章我会带你：对位 JS 的 `Set`，掌握 Dart `Set<T>` 的常用 API 与集合运算。

## 我先把概念讲清楚

### 1) `Set<T>`：无序、元素唯一

和 JS `Set` 类似，但泛型更强，常用于：

- 去重
- membership 判断（`contains`）
- 集合运算（并/交/差）

## 然后我用对照例子讲

### 1) 去重

TS：

```ts
const uniq = [...new Set(ids)];
```

Dart：

```dart
final uniq = ids.toSet().toList();
```

### 2) 并/交/差（Dart 很直接）

```dart
final a = {1, 2, 3};
final b = {3, 4};

final union = a.union(b);        // {1,2,3,4}
final inter = a.intersection(b); // {3}
final diff = a.difference(b);    // {1,2}
```

## 我在项目里怎么做

- `contains` 在 Set/Map 上通常比 List 更快（大集合时差异明显）。
- 权限/功能开关、标签系统等场景，Set 很好用。

## 我给你留个练习

1) 给定两个 tag 列表，输出“新增 tags”“删除 tags”（用 Set 差集实现）。  
2) 写一个函数：判断一组 todo 是否存在重复 id。  


# 09｜Map：别把 JS object 直觉硬套

这一章我会带你：对位 TS/JS 的对象与字典写法，理解 Dart `Map<K, V>` 在类型与运行时行为上的差异。

## 我先把概念讲清楚

### 1) TS/JS：object 很万能，但不总是字典

在 JS 里对象既承载“结构化数据”（DTO），又经常被当成字典（key-value）。在 TS 里你用 `Record<string, T>` 让它“看起来像字典”。

### 2) Dart：`Map` 就是字典

`Map<String, Object?>` 是 Dart 里最常见的 JSON 载体类型（配合空安全）。

## 然后我用对照例子讲

### 1) 创建与读写

TS：

```ts
const m: Record<string, number> = { a: 1 };
m["b"] = 2;
```

Dart：

```dart
final m = <String, int>{'a': 1};
m['b'] = 2;
```

### 2) 遍历

TS：

```ts
for (const [k, v] of Object.entries(m)) console.log(k, v);
```

Dart：

```dart
for (final e in m.entries) {
  print('${e.key} ${e.value}');
}
```

### 3) 常用 API：`putIfAbsent`

```dart
m.putIfAbsent('c', () => 3);
```

## 我在项目里怎么做（JSON 边界）

- `Map<String, dynamic>` 在 Dart 里能用，但更推荐 `Map<String, Object?>`：  
  - 更接近 “unknown + 收窄” 的习惯  
  - 避免 dynamic 污染  
- DTO 解析后尽快进入强类型模型；不要在业务层把 Map 当实体对象用。  

## 我给你留个练习

1) 把 `List<Todo>` 转成 `Map<String, Todo>`（key 为 id）。  
2) 写一个 `merge`：给定 `Map<String, Object?> base` 与 `patch`，返回合并结果（patch 覆盖 base）。  


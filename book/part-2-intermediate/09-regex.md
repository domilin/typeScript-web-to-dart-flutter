# 09｜正则：JS RegExp ↔ Dart RegExp（以及 raw string）

这一章我会带你：掌握 Dart `RegExp` 的常用 API，并解决“正则转义很烦”的问题（raw string）。

## 我先把概念讲清楚

### 1) `RegExp` 类

```dart
final r = RegExp(r'\\d+');
```

### 2) raw string：`r'...'`

raw string 不处理反斜杠转义，正则会舒服很多：

```dart
final email = RegExp(r'^[^@]+@[^@]+\\.[^@]+$');
```

## 然后我用对照例子讲

TS：

```ts
const r = /\d+/g;
const m = "a1b22".match(r); // ["1","22"]
```

Dart：

```dart
final r = RegExp(r'\\d+');
final m = r.allMatches('a1b22').map((e) => e.group(0)).toList();
```

## 我在项目里怎么做（表单校验）

在 Flutter 做表单校验时，正则常用于：

- email/手机号格式
- 密码强度

但注意：正则只做“格式粗筛”，真正校验逻辑更适合放在 domain 层（例如用户名是否可用、手机号是否已注册）。

## 我给你留个练习

1) 写一个正则：提取字符串里的所有 `#tag`（tag 只允许字母数字下划线）。  
2) 写一个函数：把输入里的连续空白替换成一个空格（用 RegExp）。  


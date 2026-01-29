# 03｜参数系统：命名参数是 Dart 的“默认姿势”

这一章我会带你：把 TS 里常见的“对象参数 + 解构 + 默认值”迁移到 Dart 的命名参数/可选参数体系上。

## 我先把概念讲清楚

### 1) 位置参数（positional）

```dart
int add(int a, int b) => a + b;
```

### 2) 可选位置参数（optional positional）

```dart
String greet(String name, [String? title]) => title == null ? name : '$title $name';
```

### 3) 命名参数（named）

```dart
String greet(String name, {String? title}) => title == null ? name : '$title $name';
```

### 4) 必填命名参数：`required`

```dart
Todo createTodo({required String title}) => ...;
```

> 经验法则：业务函数/构造器优先用命名参数（可读性强），并用 `required` 明确必填项。

## 然后我用对照例子讲

### 1) TS：对象参数 + 默认值

```ts
function request(url: string, opts?: { method?: "GET" | "POST"; timeoutMs?: number }) {
  const method = opts?.method ?? "GET";
  const timeoutMs = opts?.timeoutMs ?? 3000;
}
```

### 2) Dart：命名参数 + 默认值

```dart
void request(String url, {String method = 'GET', int timeoutMs = 3000}) {
  // ...
}
```

### 3) Flutter 构造器：命名参数几乎是标准

```dart
class TodoRow extends StatelessWidget {
  const TodoRow({super.key, required this.title, required this.done});
  final String title;
  final bool done;
  // ...
}
```

## 我在项目里怎么做

- 命名参数提升可读性：你在 review Flutter PR 时，看到 `EdgeInsets.symmetric(horizontal: 16, vertical: 12)` 会比 `padding(16, 12)` 更不容易误解。
- 避免“参数爆炸”：当命名参数超过 6–8 个，考虑把它们收敛成配置对象（class/record）或拆分函数。

## 我给你留个练习

1) 写一个 `Todo copyTodo(Todo t, {String? title, bool? done})`（对位 TS 的 `copyWith`）。  
2) 写一个 `Future<T> retry<T>(Future<T> Function() task, {required int maxRetries})`：失败后重试（先不做指数退避）。  


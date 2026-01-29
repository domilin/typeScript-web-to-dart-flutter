# 07｜异常与 Result：把“可预期错误”从 throw 里拿出来

这一章我会带你：对位 TS 的错误处理（`unknown`、Error boundary、Result/Either），建立 Flutter 项目里更可维护的错误建模方式。

## 我先把概念讲清楚

### 1) try/catch：Dart 的 catch 会给你 stack trace

```dart
try {
  await task();
} catch (e, st) {
  // e: Object, st: StackTrace
}
```

### 2) 不要把“业务可预期错误”都 throw

前端项目里你通常不会用 throw 表示“表单校验失败”；更常见：

- 返回错误对象/Result
- UI 根据错误类型展示

Flutter/Dart 同理。把错误分成两类：

- 可预期（expected）：网络 4xx、校验失败、权限不足 → 用 Result/错误类型表达
- 不可预期（unexpected）：空指针、类型错误、越界 → 让它 crash（开发期暴露）或统一上报（生产）

## 然后我用对照例子讲

### 1) TS：Result（判别联合）

```ts
type Result<T> = { ok: true; value: T } | { ok: false; error: AppError };
```

### 2) Dart：sealed Result

```dart
sealed class Result<T> { const Result(); }

final class Ok<T> extends Result<T> {
  const Ok(this.value);
  final T value;
}

final class Err<T> extends Result<T> {
  const Err(this.error);
  final AppError error;
}

sealed class AppError { const AppError(); }
final class NetworkError extends AppError { const NetworkError(this.message); final String message; }
final class ValidationError extends AppError { const ValidationError(this.message); final String message; }
```

使用：

```dart
String render(Result<int> r) => switch (r) {
  Ok(value: final v) => 'v=$v',
  Err(error: final e) => 'e=$e',
};
```

## 我在项目里怎么做（网络错误归一化）

请求层（dio）通常会抛各种错误；建议在 repository 层把它们归一化为 `AppError`，UI 只处理你定义的错误集合（对位前端的 error boundary + error mapping）。

## 我给你留个练习

1) 定义 `AppError`：Network/Unauthorized/Validation/Unknown 四种，并写一个 `String message(AppError e)`。  
2) 写一个 `Result<Todo> createTodo(String title)`：title 为空返回 ValidationError，其他异常归一化为 Unknown。  


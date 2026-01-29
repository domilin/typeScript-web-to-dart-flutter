# 03｜sealed 与模式匹配：TS 联合类型在 Dart 的正确打开方式

这一章我会带你：把 TS 的判别联合（discriminated union）迁移到 Dart 3 的 `sealed class + switch pattern`，用于建模：请求状态、业务状态、错误结果。

## 我先把概念讲清楚

### 1) Dart 没有 `A | B`，用 sealed class 表达“闭合集合”

在 TS 里你会写：

```ts
type Result<T> = { ok: true; value: T } | { ok: false; error: string };
```

在 Dart 里更推荐：

- `sealed class Result<T>`
- `final class Ok<T> extends Result<T>`
- `final class Err<T> extends Result<T>`

### 2) switch 表达式 + 模式匹配

Dart 的 `switch` 可以是表达式，并能匹配：

- 类型：`String s`
- 对象字段：`Ok(value: final v)`
- guard：`when ...`
- 兜底：`_`

并且对 sealed 类型可以做穷尽检查（exhaustive）。

## 然后我用对照例子讲

### 1) TS 判别联合

```ts
type RemoteData<T> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "error"; message: string }
  | { status: "data"; data: T };

function render(x: RemoteData<number>) {
  switch (x.status) {
    case "loading": return "loading";
    case "error": return x.message;
    case "data": return String(x.data);
    default: return "idle";
  }
}
```

### 2) Dart sealed + pattern matching

```dart
sealed class RemoteData<T> {
  const RemoteData();
}

final class Idle<T> extends RemoteData<T> {
  const Idle();
}

final class Loading<T> extends RemoteData<T> {
  const Loading();
}

final class Error<T> extends RemoteData<T> {
  const Error(this.message);
  final String message;
}

final class Data<T> extends RemoteData<T> {
  const Data(this.data);
  final T data;
}

String render(RemoteData<int> x) => switch (x) {
  Loading() => 'loading',
  Error(message: final m) => m,
  Data(data: final v) => '$v',
  Idle() => 'idle',
};
```

## 我在项目里怎么做（为什么它很像 TS union，但更适合 Flutter）

- UI 状态天然是“闭合集合”（loading/error/data/empty…），sealed 非常匹配。
- 模式匹配能把“收窄 + 解构 + 分支”写得很直观（比到处 `if (x is ...)` 好维护）。
- 真实项目建议用 `freezed` 生成 union + `when/map`（减少样板），但理解语言原理更重要。

## 我给你留个练习

1) 为 Todo 列表定义 `sealed class TodoFilter`（All/Active/Done），并写一个 `switch` 返回对应过滤后的 list。  
2) 定义 `Result<T>`（Ok/Err），并写一个 `map` 方法：Ok 时映射值，Err 透传错误。  


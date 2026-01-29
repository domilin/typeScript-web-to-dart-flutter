# 04｜Future/async/await：对位 Promise，但别忽略细节

这一章我会带你：在你已经会 `async/await` 的前提下，补齐 Dart/Flutter 项目里真正会用到的细节：并发、超时、取消策略、错误传播、UI 集成。

## 我先把概念讲清楚

### 1) `Future<T>` 对位 `Promise<T>`

- `Future` 表示一次性异步结果
- `async/await` 语法高度相似

### 2) 并发：`Future.wait`

和 `Promise.all` 类似，但注意类型推导与错误处理方式（通常需要自己拆开写以获得更好类型）。

### 3) 超时：`timeout`

Dart 的 `Future.timeout` 很直接。

### 4) 取消：Future 本身不可取消（和 Promise 类似）

取消通常通过：

- dio 的 `CancelToken`（请求层取消）
- stream subscription（流取消）
- 自己在业务层做“忽略过期结果”（常见于搜索联想）

## 然后我用对照例子讲

### 1) async/await

TS：

```ts
const todos = await listTodos();
```

Dart：

```dart
final todos = await listTodos();
```

### 2) 并发：Promise.all ↔ Future.wait

TS：

```ts
const [profile, todos] = await Promise.all([loadProfile(), listTodos()]);
```

Dart（可读性更好的写法）：

```dart
final profileFuture = loadProfile();
final todosFuture = listTodos();
final profile = await profileFuture;
final todos = await todosFuture;
```

### 3) 超时

Dart：

```dart
final todos = await listTodos().timeout(const Duration(seconds: 2));
```

## 我在项目里怎么做（Flutter UI 与 async 的边界）

- 避免在 widget 树里堆 `FutureBuilder`：  
  - 少量 demo 可以  
  - 真实项目更建议把异步状态收敛到状态层（Riverpod/BLoC）  
- 把 “loading/error/data” 当成一等状态（对位 react-query 的 status），UI 只是渲染状态。  

## 我给你留个练习

1) 写一个 `Future<T> withTimeout<T>(Future<T> f, Duration d)`，超时抛自定义异常。  
2) 写一个“搜索联想”的 demo：输入变化时发请求，只展示最后一次请求结果（忽略过期结果）。  


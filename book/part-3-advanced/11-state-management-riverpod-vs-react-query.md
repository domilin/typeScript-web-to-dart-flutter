# 11｜状态管理：用 Riverpod 复刻 react-query 的“server state”体验

这一章我会带你：你熟悉 react-query（TanStack Query）的核心：server state 的缓存、刷新、mutation、乐观更新。Flutter 没有绝对标准 1:1，但 Riverpod 足够强大来承载同样的模式。

> 我先给你一张“翻译表”，后面所有代码都在落实这几条：  
> - `useQuery` → `build()`（第一次加载）  
> - `queryKey` → provider 的身份（同一个 provider 会复用/缓存）  
> - `invalidateQueries` → `ref.invalidate(...)`（让它重新加载）  
> - `useMutation` → controller 的方法（`create/toggle/remove`）  
> - optimistic update → 先改 `state`，失败就 rollback，再把错误交给 UI 提示  

## 概念解释（先拆问题）

### 1) react-query 解决的是“server state”

典型能力：

- query：`useQuery({ queryKey, queryFn })`
- mutation：`useMutation({ mutationFn, onMutate, onError, onSettled })`
- cache：按 queryKey 缓存
- invalidation：失效后 refetch

### 2) Riverpod 是“通用状态系统”

Riverpod 的核心是 provider：

- provider 本身是一个“可组合的依赖图”
- `AsyncValue<T>` 天然表达 loading/error/data

你要做的是：把 react-query 的模式“映射到 provider 组合”上。

## 实例：一个“Todos Query + Mutations”模板

### 1) Query：用 `AsyncNotifier` 管理列表（对位 useQuery）

```dart
class TodosController extends AsyncNotifier<List<Todo>> {
  @override
  Future<List<Todo>> build() async {
    return ref.read(todoRepoProvider).listTodos();
  }

  Future<void> refresh() async {
    state = const AsyncLoading();
    state = await AsyncValue.guard(() => ref.read(todoRepoProvider).listTodos());
  }
}
```

你会得到：

- `state` 是 `AsyncValue<List<Todo>>`
- UI 里渲染 `loading/error/data` 很直接

### 2) Invalidate：对位 `invalidateQueries`

```dart
ref.invalidate(todosControllerProvider);
```

### 3) Mutation + 乐观更新（对位 onMutate/rollback）

```dart
Future<void> toggle(String id) async {
  final prev = state.valueOrNull ?? const <Todo>[];
  final idx = prev.indexWhere((t) => t.id == id);
  if (idx < 0) return;

  final optimistic = [...prev];
  optimistic[idx] = optimistic[idx].copyWith(done: !optimistic[idx].done);
  state = AsyncData(optimistic);

  try {
    final updated = await ref.read(todoRepoProvider).toggleTodo(id);
    state = AsyncData([for (final t in optimistic) if (t.id == id) updated else t]);
  } catch (e, st) {
    state = AsyncData(prev); // rollback
    Error.throwWithStackTrace(e, st);
  }
}
```

## 差异点（你需要调整预期）

### 1) staleTime/cacheTime 并不是内建概念

Riverpod 默认会缓存 provider 的结果，但“多久算过期”需要你自己定义策略（例如带上 timestamp，或按生命周期 invalidate）。

### 2) 重试/backoff 更建议放在 repository/网络层

react-query 把 retry 当作 query 能力；Flutter 里更常见用 dio 拦截器或 repository 层封装 retry。

### 3) 你也可以直接用“更像 react-query 的库”

如果团队更想要 1:1 的体验，可以调研：

- `cached_query`
- `flutter_query`

但建议先理解模式，再选库。

## 我给你留个练习

1) 实现 `create/remove` 两个 mutation，并做到：失败回滚 + UI 提示。  
2) 实现一个“筛选 filter”（all/active/done）：filter 改变不应触发网络请求，只是本地派生状态（对位 react-query 的 `select`/客户端筛选）。  

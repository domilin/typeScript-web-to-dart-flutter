# 10｜网络请求：Dio ↔ axios（拦截器、取消、错误归一化）

这一章我会带你：用 axios 的心智学习 dio：client 初始化、拦截器、取消请求、超时、错误映射，并给出“前端式”的分层建议（api → repository → controller）。

## 概念解释（先讲对位）

### 1) 对位表

| axios | dio |
|---|---|
| `axios.create({ baseURL })` | `Dio(BaseOptions(baseUrl: ...))` |
| interceptors | interceptors |
| request cancel（AbortController / CancelToken） | `CancelToken` |
| response typing（TS 泛型） | 手写解析/生成模型（`fromJson`） |

### 2) 为什么建议“错误归一化”

前端里你很可能有一层 `api.ts` 把 axios error 统一转成 `AppError`；Flutter 同理：UI 不应该知道 dio 的各种异常细节。

## 实例（dio 初始化与拦截器）

### 1) 创建一个 Dio client（对位 axios instance）

```dart
final dio = Dio(
  BaseOptions(
    baseUrl: 'https://api.example.com',
    connectTimeout: const Duration(seconds: 10),
    receiveTimeout: const Duration(seconds: 10),
  ),
);
```

### 2) 添加拦截器（对位 axios interceptors）

```dart
dio.interceptors.add(
  InterceptorsWrapper(
    onRequest: (options, handler) async {
      // 注入 token
      options.headers['Authorization'] = 'Bearer ${await tokenStore.read()}';
      handler.next(options);
    },
    onError: (e, handler) {
      // 统一日志/上报
      handler.next(e);
    },
  ),
);
```

### 3) 取消请求

```dart
final token = CancelToken();
final future = dio.get('/todos', cancelToken: token);
token.cancel('user canceled');
await future;
```

## 分层建议（像写前端一样写 Flutter）

### 1) api 层：只做 transport + parse

- 输入：原始参数（string/int）
- 输出：DTO（强类型）
- 不要引入 UI 状态（loading/error）

### 2) repository 层：做错误归一化 + 业务语义

- 捕获 dio 异常，转成 `AppError`
- 做 DTO → Domain 的转换

### 3) controller/state 层：组合 query/mutation

第三部分下一章会把它对位到 react-query 的模式。

## 我给你留个练习

1) 写一个 `TodoApi`：`list/create/toggle/delete` 四个方法，返回强类型 `Todo`。  
2) 写一个 `TodoRepository`：把 dio 异常映射成 `AppError`（Network/Unauthorized/Unknown）。  


# 12｜路由：go_router ↔ Next.js Router（嵌套、守卫、深链）

这一章我会带你：把你对 Next.js 路由（文件路由、layout、redirect、params）的理解迁移到 Flutter 路由：声明式配置、嵌套导航、登录守卫、深链。

## 概念解释（先对位）

### 1) Next.js：文件系统即路由

- `app/` 下的目录结构决定 URL
- layout 层级天然形成嵌套 UI

### 2) Flutter：路由是配置（go_router 最常用）

你会显式声明：

- path
- builder（页面）
- 子路由（嵌套）
- redirect（守卫）

## go_router 示例（核心骨架）

```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => const HomePage(),
      routes: [
        GoRoute(
          path: 'todos/:id',
          builder: (context, state) {
            final id = state.pathParameters['id']!;
            return TodoDetailPage(id: id);
          },
        ),
      ],
    ),
    GoRoute(
      path: '/login',
      builder: (context, state) => const LoginPage(),
    ),
  ],
);
```

跳转：

```dart
context.go('/todos/123');
context.push('/login');
```

## 守卫（auth redirect）思路

Next.js 里你可能在 middleware / server action / layout 里做 redirect。  
go_router 的思路是：根据 auth state 决定 redirect 到哪里。

（与状态管理集成的写法会因 Riverpod/BLoC 方案不同而不同，这里给出概念：）

- 未登录访问受保护页面 → redirect 到 `/login`
- 登录后回跳原页面（保存 from）

## 我在项目里怎么做（导航结构）

- Tab 导航（底部导航栏）通常用 ShellRoute 或 nested navigation（比 Web 的“多 layout”更像）。
- 深链（deeplink）在移动端很重要：URL 不只是浏览器地址栏，更是外部唤起入口。

## 我给你留个练习

1) 在 Todo app 中加一个 `/settings` 页面，并在右上角按钮跳转。  
2) 实现一个最简“登录守卫”：未登录访问 `/settings` 会跳到 `/login`。  


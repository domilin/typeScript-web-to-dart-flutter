# 02｜Todo（Flutter）：Material 3 + Riverpod + Dio + intl

这一章我会带你：给出一个“前端工程化风格”的 Flutter Todo 蓝图：分层、状态、请求、主题、响应式与多语言，并与 Web 版本一一对照。

## 0) 技术栈（推荐组合）

- UI：Material 3（你也可以封装成自己的组件库风格）
- 路由：go_router（对位 Next Router）
- 状态：flutter_riverpod（对位 react-query 的组织方式）
- 网络：dio（对位 axios）
- 模型：freezed + json_serializable（推荐，减少样板）
- i18n：flutter_localizations + intl（arb + gen_l10n）

## 1) 目录结构（feature-first）

```text
lib/
  main.dart
  app.dart                        # MaterialApp + router + theme + l10n
  core/
    http/dio_client.dart
    theme/app_theme.dart
    theme/app_tokens.dart         # ThemeExtension（对位 tailwind tokens）
    l10n/                         # arb + 生成的 AppLocalizations
  features/todos/
    domain/todo.dart              # freezed model（或手写）
    data/todo_api.dart
    data/todo_repository.dart     # 错误归一化（AppError）
    application/todos_controller.dart
    presentation/todo_page.dart
    presentation/widgets/todo_row.dart
```

## 2) 模型：Todo（推荐用 freezed）

> 这里给概念示意：真实项目建议用 `freezed` 生成 `copyWith/==/fromJson`。

手写最小版：

```dart
class Todo {
  const Todo({required this.id, required this.title, required this.done, required this.createdAt});
  final String id;
  final String title;
  final bool done;
  final DateTime createdAt;

  factory Todo.fromJson(Map<String, Object?> json) {
    return Todo(
      id: json['id'] as String,
      title: (json['title'] as String?)?.trim() ?? '',
      done: (json['done'] as bool?) ?? false,
      createdAt: DateTime.parse(json['createdAt'] as String),
    );
  }

  Todo copyWith({String? title, bool? done}) => Todo(
        id: id,
        title: title ?? this.title,
        done: done ?? this.done,
        createdAt: createdAt,
      );
}
```

## 3) 网络：Dio（对位 axios instance）

```dart
final dio = Dio(BaseOptions(baseUrl: 'https://api.example.com'));
```

`TodoApi`（示意）：

```dart
class TodoApi {
  TodoApi(this._dio);
  final Dio _dio;

  Future<List<Todo>> listTodos() async {
    final res = await _dio.get<List<dynamic>>('/todos');
    final data = res.data ?? const [];
    return data.map((e) => Todo.fromJson(e as Map<String, Object?>)).toList();
  }

  Future<Todo> createTodo(String title) async {
    final res = await _dio.post<Map<String, dynamic>>('/todos', data: {'title': title});
    return Todo.fromJson(res.data!.cast<String, Object?>());
  }

  Future<Todo> toggleTodo(String id) async {
    final res = await _dio.patch<Map<String, dynamic>>('/todos/$id');
    return Todo.fromJson(res.data!.cast<String, Object?>());
  }

  Future<void> deleteTodo(String id) async {
    await _dio.delete<void>('/todos/$id');
  }
}
```

## 4) 状态：Riverpod（AsyncNotifier 对位 useQuery + mutations）

```dart
class TodosController extends AsyncNotifier<List<Todo>> {
  @override
  Future<List<Todo>> build() async {
    return ref.read(todoRepoProvider).listTodos();
  }

  Future<void> create(String title) async {
    final prev = state.valueOrNull ?? const <Todo>[];
    final tempId = nanoid(); // from package:nanoid/nanoid.dart（或 uuid）
    state = AsyncData([Todo(id: tempId, title: title, done: false, createdAt: DateTime.now()), ...prev]);
    try {
      final created = await ref.read(todoRepoProvider).createTodo(title);
      state = AsyncData([created, ...prev]);
    } catch (e, st) {
      state = AsyncData(prev); // rollback
      Error.throwWithStackTrace(e, st);
    }
  }

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
      state = AsyncData(prev);
      Error.throwWithStackTrace(e, st);
    }
  }
}
```

## 5) UI：布局/样式/响应式

对应关系（Web ↔ Flutter）：

- `flex + gap + truncate` ↔ `Row + SizedBox + Expanded + ellipsis`
- Tailwind breakpoints ↔ `MediaQuery/LayoutBuilder` 断点判断
- shadcn tokens ↔ `ThemeData + ThemeExtension`

TodoRow（示意）：

```dart
class TodoRow extends StatelessWidget {
  const TodoRow({super.key, required this.todo, required this.onToggle, required this.onDelete});

  final Todo todo;
  final VoidCallback onToggle;
  final VoidCallback onDelete;

  @override
  Widget build(BuildContext context) {
    final w = MediaQuery.sizeOf(context).width;
    final padding = EdgeInsets.all(w < 640 ? 12 : 16);

    return Container(
      padding: padding,
      decoration: BoxDecoration(
        border: Border.all(color: Theme.of(context).dividerColor),
        borderRadius: BorderRadius.circular(8),
      ),
      child: Row(
        children: [
          Checkbox(value: todo.done, onChanged: (_) => onToggle()),
          const SizedBox(width: 12),
          Expanded(
            child: Text(
              todo.title,
              maxLines: w < 640 ? 1 : 2,
              overflow: TextOverflow.ellipsis,
              style: Theme.of(context).textTheme.bodyMedium?.copyWith(
                    decoration: todo.done ? TextDecoration.lineThrough : null,
                  ),
            ),
          ),
          const SizedBox(width: 12),
          TextButton(onPressed: onDelete, child: const Text('删除')),
        ],
      ),
    );
  }
}
```

## 6) 主题与多语言（最低可用清单）

- Theme：`MaterialApp(theme/darkTheme/themeMode)`
- i18n：arb + `AppLocalizations`
- 设置页：提供 ThemeMode 与 Locale 的切换并持久化（shared_preferences）

## 7) 下一步（从蓝图到可运行项目）

如果你希望我把本章落成一个可直接 `flutter run` 的工程（含 go_router、riverpod、dio、freezed、l10n），我可以在下一步直接生成完整目录与代码。


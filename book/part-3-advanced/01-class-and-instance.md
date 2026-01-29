# 01｜类与对象：从 TS class 到 Dart class（构造器是重点）

这一章我会带你：把你在 TS 里写 class/model 的经验迁移到 Dart：构造器体系、不可变对象、值相等、`copyWith` 的工程写法。

## 我先把概念讲清楚

### 1) Dart 的 class 是运行时实体

TS 的 `interface/type` 多数只存在于编译期；Dart 的 `class` 会进入运行时，因此：

- 你可以可靠地做 `is` 判断
- 你需要更认真地设计“模型层”（否则 UI 层会承受类型/空安全压力）

### 2) 构造器体系（比 TS 更语言级）

Dart 常见构造器：

- 默认构造器：`Todo(...)`
- 命名构造器：`Todo.fromJson(...)`
- 工厂构造器：`factory Todo(...)`（可返回缓存/子类）
- `const` 构造器（Flutter 常用）
- 初始化列表：`: field = ..., super(...)`

### 3) 不可变与更新：`final fields + copyWith`

Flutter 项目中强烈推荐不可变模型：

- 字段 `final`
- 更新通过创建新对象（`copyWith`）

这对位你在 TS/React 里做 immutable update 的习惯（`{...prev, x: next}`）。

### 4) 相等：默认是引用相等

如果你不重写 `==/hashCode`，Dart 的 class 默认是引用相等；这会影响：

- Set/Map key 行为
- 列表去重/比较
- 状态更新是否被 UI 正确识别（取决于状态管理方案）

真实项目通常用 `freezed` 自动生成值相等与 `copyWith`（见 08 工具链）。

## 然后我用对照例子讲

### 1) TS class（只读字段 + 默认值）

```ts
export class Todo {
  constructor(
    public readonly id: string,
    public title: string,
    public done: boolean = false,
    public readonly createdAt: string = new Date().toISOString(),
  ) {}
}
```

### 2) Dart class（不可变模型）

```dart
class Todo {
  const Todo({
    required this.id,
    required this.title,
    required this.done,
    required this.createdAt,
  });

  final String id;
  final String title;
  final bool done;
  final DateTime createdAt;

  Todo copyWith({String? title, bool? done}) {
    return Todo(
      id: id,
      title: title ?? this.title,
      done: done ?? this.done,
      createdAt: createdAt,
    );
  }
}
```

### 3) 命名构造器：fromJson（对位 TS 的静态方法）

```dart
class Todo {
  // ...
  factory Todo.fromJson(Map<String, Object?> json) {
    return Todo(
      id: json['id'] as String,
      title: (json['title'] as String?)?.trim() ?? '',
      done: (json['done'] as bool?) ?? false,
      createdAt: DateTime.parse(json['createdAt'] as String),
    );
  }
}
```

### 4) 初始化列表（常见于依赖注入/派生字段）

```dart
class UserViewModel {
  UserViewModel(User user)
      : id = user.id,
        displayName = user.name.trim().isEmpty ? '(unknown)' : user.name;

  final String id;
  final String displayName;
}
```

## 我在项目里怎么做（模型层怎么写才“前端工程化”）

- “DTO 与 Domain 分离”：  
  - DTO 负责对接后端字段与容错  
  - Domain 负责业务语义（例如 `isOverdue`、`displayTitle`）  
- 用生成工具减少样板：`freezed`/`json_serializable`/`build_runner`。  
- UI 里少出现 `Map<String, Object?>`：就像你不会在 React 组件里到处处理 `any`。  

## 我给你留个练习

1) 为 `Todo` 添加 `copyWith` 并实现 `toggle()`（返回新对象）。  
2) 写一个 `TodoDto`（字段全是 `String/bool`），并提供 `toDomain()` 转换（`createdAt` 转 DateTime）。  


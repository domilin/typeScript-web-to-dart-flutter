# 05｜包管理：`package.json` ↔ `pubspec.yaml`

这一章我会带你：把你在 npm/pnpm 的依赖管理经验平移到 Dart/Flutter：依赖声明、锁文件、升级策略、常见坑（overrides）。

## 语言/工具解释（先讲概念）

### 1) `pubspec.yaml` 是项目依赖入口

- `dependencies`：运行时依赖
- `dev_dependencies`：开发依赖（测试、生成、lint）

锁文件：

- `pubspec.lock`：类似 `pnpm-lock.yaml`/`package-lock.json`

### 2) 常用命令

```bash
flutter pub get
flutter pub outdated
flutter pub upgrade
```

纯 Dart 包也可以用 `dart pub ...`。

### 3) 版本约束与 overrides

pub 同样支持 `^` 语义版本约束：

```yaml
dio: ^5.4.0
```

`dependency_overrides` 类似 npm 的 resolutions（或 pnpm overrides），能救火，但容易埋雷（团队要谨慎使用）。

## 实例对比（再看配置）

TS：

```json
{
  "dependencies": { "axios": "^1.6.0" },
  "devDependencies": { "@biomejs/biome": "^1.5.0" }
}
```

Flutter：

```yaml
dependencies:
  flutter:
    sdk: flutter
  dio: ^5.4.0

dev_dependencies:
  flutter_lints: ^3.0.0
  build_runner: ^2.4.0
```

## 我在项目里怎么做（工程化依赖组合）

你在 TS 项目里常见的 “请求 + 缓存 + 类型 + 校验” 组合，在 Flutter 通常是：

- dio（请求）+ riverpod（状态/缓存）+ freezed/json_serializable（类型/模型）+ 自定义校验

## 我给你留个练习

1) 给一个 Flutter 项目加上 `dio`、`flutter_riverpod`、`intl`，并成功 `flutter pub get`。  
2) 运行 `flutter pub outdated` 看看有哪些可升级项，尝试只升级 minor 版本。  


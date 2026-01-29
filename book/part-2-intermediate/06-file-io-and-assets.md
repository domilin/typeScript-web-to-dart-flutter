# 06｜文件操作与资源管理：Node 的 fs ↔ Dart/Flutter 的 IO/Assets

这一章我会带你：澄清一个前端开发者常见误区：Flutter 不是浏览器，Dart 也不是永远都能 `fs.readFileSync`；你需要区分运行平台。

## 我先把概念讲清楚

### 1) 运行平台决定可用 API

| 平台 | Dart 库 | 典型场景 |
|---|---|---|
| Flutter mobile/desktop | `dart:io` 可用（大多情况） | 读写本地文件、缓存、日志 |
| Flutter web | `dart:io` 不可用 | 需要用浏览器能力（http/IndexedDB 等） |
| Dart CLI | `dart:io` 可用 | 命令行工具、脚本 |

### 2) Flutter Assets：不是“文件系统路径”

在 Flutter 中，`assets` 通常被打包进应用；访问方式是通过 `rootBundle` 读取，而不是 `File('assets/...')`。

## 然后我用对照例子讲

### 1) Node：读取文件

```ts
import { readFile } from "node:fs/promises";
const text = await readFile("./data.json", "utf8");
```

### 2) Dart CLI：读取文件

```dart
import 'dart:io';

Future<void> main() async {
  final text = await File('data.json').readAsString();
  print(text);
}
```

### 3) Flutter：读取 assets（例如 JSON 配置）

`pubspec.yaml`：

```yaml
flutter:
  assets:
    - assets/config.json
```

读取：

```dart
import 'package:flutter/services.dart' show rootBundle;

Future<String> loadConfig() async {
  return rootBundle.loadString('assets/config.json');
}
```

### 4) Flutter：写入应用目录（对位“本地缓存”）

通常配合 `path_provider`（示意）：

```dart
final dir = await getApplicationDocumentsDirectory();
final file = File('${dir.path}/todos.json');
await file.writeAsString(jsonText);
```

## 我在项目里怎么做（Todo 持久化）

- Web（Next）：localStorage / IndexedDB / DB（SSR 场景注意分端）
- Flutter：`shared_preferences`（轻量 KV）/ `hive`（本地 NoSQL）/ `sqflite`（SQLite）/ 文件（最简单但要自己维护结构）

选择顺序建议：先 `shared_preferences`/hive 验证产品形态，再考虑 SQLite/后端同步。

## 我给你留个练习

1) 写一个 Dart CLI：读取一个 JSON 文件并输出其中某个字段（处理字段缺失）。  
2) 在 Flutter 中加入一个 `assets/strings.json`，运行时读取并显示在 UI 上。  


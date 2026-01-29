# 08｜工具链与质量：对位 Biome 的 Flutter 工程化清单

这一章我会带你：给你一套“前端式”的 Flutter 质量体系：format/lint/analyze/test/codegen/CI 该怎么组合，做到团队可复制。

## 工具解释（先讲概念）

### 1) 对位表：Biome / TS 工具链 ↔ Dart/Flutter

| 前端 | Dart/Flutter |
|---|---|
| `biome format` | `dart format` |
| `biome lint` | `dart analyze` + lints |
| `tsc --noEmit` | `dart analyze`（本质上更像类型检查 + lints） |
| codegen（OpenAPI/GraphQL/Prisma） | `build_runner`（freezed/json_serializable/riverpod_generator 等） |
| Jest/Vitest | `flutter test`（包含 unit + widget） |

### 2) 三个你应该固化的命令

```bash
dart format --set-exit-if-changed .
dart analyze
flutter test
```

这三个就像你在前端的 `biome check` + `test`：能上 CI 的“最低门槛”。

### 3) lints：规则入口在 `analysis_options.yaml`

```yaml
include: package:flutter_lints/flutter.yaml
```

需要更强规则时再加：

- `custom_lint`
- `lint` 规则集

### 4) 代码生成：build_runner（真实项目一定会遇到）

常用命令：

```bash
dart run build_runner build --delete-conflicting-outputs
dart run build_runner watch --delete-conflicting-outputs
```

## 项目落地模板（你可以直接照搬）

### 1) CI 阶段建议

1. format（不通过就 fail）
2. analyze（不通过就 fail）
3. test（不通过就 fail）
4. build（可选：验证 `flutter build` 能通过）

### 2) 依赖升级策略

像前端一样保持“小步升级”：

- 先升级 patch/minor
- 大版本升级配合 changelog
- 尽量避免长期依赖 overrides

## 我给你留个练习

1) 写一个脚本 `check`（Makefile/npm scripts 都行），串联 format/analyze/test。  
2) 在项目里引入 `freezed` 并跑通一次 build_runner（生成一个 Todo 模型）。  


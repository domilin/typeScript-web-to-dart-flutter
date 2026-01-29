# 01｜开发环境：把 Flutter 当成“前端工具链”来装

这一章我会带你：用你熟悉的前端工程视角，把 Flutter/Dart 的安装、版本锁定、常用命令、IDE 配置一次性对齐。

## 语言/工具解释（先讲概念）

### 1) 工具链对照：Node/Next vs Flutter

| 你熟悉的前端 | Flutter/Dart |
|---|---|
| `node` + 包管理器（pnpm/yarn） | Flutter SDK + `dart` + `flutter` 命令 |
| `nvm` 锁 node 版本 | FVM 锁 Flutter 版本 |
| `package.json scripts` | `flutter`/`dart` 命令（可在 README/Makefile 统一） |
| `biome`/`eslint`/`prettier` | `dart format` / `dart analyze` + lints |

### 2) 为什么建议用 FVM

Flutter 的 SDK 版本会影响：

- Dart 语言版本（尤其新语法：patterns、records）
- framework 行为（主题、渲染、组件细节）
- 构建链（Android/iOS 产物）

团队协作时，“锁 Flutter 版本”就像你锁定 Node 版本一样重要。

## 实操清单（一步到位）

### 1) 安装 Flutter 并检查环境

常用命令：

```bash
flutter doctor
flutter --version
dart --version
```

`flutter doctor` 是你的“环境验收”入口：Android toolchain / Xcode / device / IDE 插件问题基本都能在这里定位。

### 2) 创建项目（对位 `create-next-app`）

```bash
flutter create my_app
cd my_app
flutter run
```

### 3) 质量命令（对位 `biome check`）

```bash
dart format .
dart analyze
flutter test
```

你可以像前端一样把它们收敛成单一入口（例如 Makefile / npm scripts / justfile），让团队执行一致。

### 4) 推荐依赖（工程化起手）

一个偏“前端工程化”的 Flutter 项目常用：

- lints：`flutter_lints`（基础规则集）
- 网络：`dio`（对位 axios）
- 状态：`flutter_riverpod`（偏组合式，容易对位 react-query 模式）
- i18n：`flutter_localizations` + `intl`
- 模型：`freezed` + `json_serializable` + `build_runner`（生成不可变模型、union、JSON）

（这些会在第三部分细讲，这里先让你知道“工程化落点在哪里”。）

## TS 对照：你需要改的几个习惯

### 1) “我先写 any，后面再改”在 Dart 成本更高

Dart 的空安全与名义化类型更严格，`dynamic` 虽然能像 `any` 一样“先跑起来”，但会让错误推迟到运行时，而且会污染接口边界。

建议一开始就坚持：

- 模型层避免 `Map<String, dynamic>` 在业务里穿透
- 依赖边界清晰：API 层返回 DTO，Domain 层做语义化转换

### 2) 像写 React 一样拆组件

Flutter 的可维护性很依赖组件拆分（小 widget），否则一个 `build` 会迅速变成巨型 JSX。

## 我给你留个练习

1) 用 FVM（或你熟悉的版本管理方案）把 Flutter 固定到一个版本，并写到项目 README。  
2) 创建一个新 Flutter 项目，确保 `dart format` / `dart analyze` / `flutter test` 都能跑通。  
3) 给项目加一个“质量入口”（例如 `make check`），把三条命令串起来。  


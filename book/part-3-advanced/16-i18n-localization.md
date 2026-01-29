# 16｜多语言（i18n/l10n）：next-intl ↔ Flutter gen_l10n + intl

这一章我会带你：把你在 Next.js 里做 i18n 的经验迁移到 Flutter：arb 文件、代码生成、复数/格式化、运行时切换语言。

> 我先给你一个最短路径记忆：**arb 写文案 → gen_l10n 生成类型安全 API → UI 里调用 `AppLocalizations`**。  
> 你可以把它对位成：`messages/*.json` →（生成类型）→ `t('key')`，只是 Flutter 这套是官方推荐路线。

## 概念解释（先讲对位）

### 1) Web：JSON messages + t()

你可能用 `next-intl`：

- messages JSON
- `t('key')` 取文案
- format：日期/数字/复数

### 2) Flutter：arb + gen_l10n

Flutter 官方路径：

- `lib/l10n/*.arb`
- `flutter gen-l10n` 生成 `AppLocalizations`
- UI 里用 `AppLocalizations.of(context)!`

## 最小配置（概念示意）

1) `pubspec.yaml` 加依赖：

```yaml
dependencies:
  flutter_localizations:
    sdk: flutter
  intl: ^0.19.0
```

2) 创建：

- `lib/l10n/app_en.arb`
- `lib/l10n/app_zh.arb`

示例内容：

```json
{ "addTodo": "Add", "delete": "Delete" }
```

3) 在 `MaterialApp` 启用本地化（示意）：

```dart
MaterialApp(
  localizationsDelegates: AppLocalizations.localizationsDelegates,
  supportedLocales: AppLocalizations.supportedLocales,
)
```

4) 使用：

```dart
Text(AppLocalizations.of(context)!.addTodo)
```

## 我在项目里怎么做

- i18n 不只是文案：日期/数字/货币格式也要本地化（`intl` 的 `DateFormat/NumberFormat`）。
- 文案 key 命名保持稳定：像前端一样避免把 key 当文案本身（否则重构会很痛）。
- 运行时切换语言：把 `locale` 放到状态管理（Riverpod/BLoC），并提供设置页切换。

## 我给你留个练习

1) 给 Todo app 加中英文两套文案，并确保“空状态/错误提示/按钮文案”全覆盖。  
2) 做一个复数文案：`{count} items` / `1 item`（用 intl 或 gen_l10n 的复数支持）。  

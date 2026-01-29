# 07｜datetime：JS Date ↔ Dart DateTime（以及 intl 格式化）

这一章我会带你：把你在前端处理时间的经验迁移过来，并提前避开“本地时间/UTC/序列化”的坑。

## 我先把概念讲清楚

### 1) DateTime 与时区

Dart 的 `DateTime` 支持：

- 本地时间：`DateTime.now()`
- UTC：`DateTime.now().toUtc()`

序列化常用：

- `toIso8601String()`
- `DateTime.parse(isoString)`

### 2) Duration

```dart
const d = Duration(minutes: 30);
```

### 3) 格式化：用 `intl`

前端里你可能用 `date-fns/dayjs`；Flutter 里常用 `intl`：

```dart
DateFormat('yyyy-MM-dd').format(dt);
```

## 然后我用对照例子讲

TS：

```ts
const iso = new Date().toISOString();
const dt = new Date(iso);
```

Dart：

```dart
final iso = DateTime.now().toUtc().toIso8601String();
final dt = DateTime.parse(iso);
```

## 我在项目里怎么做（接口字段建议）

- 后端/接口建议统一使用 ISO 字符串（UTC），前端/Flutter 解析后再做本地展示。
- Todo 的 `createdAt`：建议存 ISO（Web/Flutter 都一致），Domain 层再转 `DateTime`。

## 我给你留个练习

1) 写一个 `String formatRelative(DateTime dt)`：1 分钟内显示“刚刚”，1 小时内显示“x 分钟前”，否则显示日期。  
2) 把 Todo 的 `createdAt` 从字符串转成 DateTime，并在 UI 上格式化显示。  


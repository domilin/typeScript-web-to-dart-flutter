# 03｜Todo 体验落地清单：布局、样式、响应式、主题、多语言

这一章我会带你：把“工程能力”变成“产品体验”：你做 Todo 并不是为了 CRUD，而是为了练完整的 UI/架构闭环。

## 1) 响应式（Responsive）

### 断点建议（与 Tailwind 对齐）

- `sm = 640`：手机/窄屏
- `lg = 1024`：平板/桌面

### 验收点

- [ ] <640：单列；>=640：密度变化（padding/字号/两行标题）或双列 master-detail
- [ ] 长标题不溢出：小屏截断，大屏最多两行
- [ ] 触控可点击区域足够（按钮/checkbox 不要太小）

## 2) 主题（Theme）

### Web（shadcn/ui）

- tokens 用 CSS variables
- `dark` class 切换变量集合（next-themes）

### Flutter

- `ThemeData + ColorScheme` 管颜色系统
- `ThemeExtension<AppTokens>` 管间距/圆角/阴影等 tokens

### 验收点

- [ ] 浅色/深色/系统三态切换正确
- [ ] 主题切换后，列表、按钮、边框、文本对比度可读
- [ ] tokens 不散落在页面里（尽量集中）

## 3) 多语言（i18n/l10n）

### Web

- next-intl（或自建 messages）

### Flutter

- arb + gen_l10n + intl

### 验收点

- [ ] en/zh 覆盖：标题、按钮、空状态、错误提示、筛选文案
- [ ] 复数/计数文案正确（`1 item`/`2 items`）

## 4) Loading/Error/Empty 状态（真实项目必须）

对位 react-query 的 `isLoading/isError/data`；Flutter 对位 `AsyncValue`。

验收点：

- [ ] 首次进入：loading skeleton 或 spinner
- [ ] 网络失败：可重试 + 错误文案（可本地化）
- [ ] 空列表：空状态图标/文案 + 引导添加

## 5) 组件体系（对位 shadcn）

建议至少封装：

- `AppButton`：primary/secondary/destructive/ghost（对位 shadcn Button variants）
- `AppCard`：统一圆角/边框/阴影
- `AppTextField`：统一 label/error/helper

验收点：

- [ ] 组件变体统一可控，不在页面里到处写样式
- [ ] 后续加新 feature（例如“编辑 todo”）不会破坏一致性

## 6) 数据一致性（乐观更新与回滚）

验收点：

- [ ] toggle/delete 的乐观更新正确
- [ ] 请求失败会回滚并提示（snackbar/toast）
- [ ] 快速连点不会导致状态乱序（至少保证最终一致）

## 7) 你可以加的“加分项”

- [ ] 本地持久化（Flutter：hive/shared_preferences；Web：localStorage/DB）
- [ ] 搜索与 debounce（Stream/Timer）
- [ ] 动画：添加/删除用 AnimatedList 或 implicit animations
- [ ] 无障碍：语义 label、对比度、可触达大小


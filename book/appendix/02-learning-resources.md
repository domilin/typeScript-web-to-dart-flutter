# 学无止境：推荐路线与资料（面向 TS/前端工程师）

## 1) 官方文档（优先级最高）

- Dart language tour（语法、空安全、patterns、records）
- Flutter docs（布局、渲染、状态、导航、i18n、性能）
- Flutter DevTools（性能分析、内存、时间线）

## 2) 建议学习顺序（可执行）

1) Dart 基础 + 空安全（能写强类型模型）  
2) Flutter 布局（Row/Column/Expanded/Stack/ListView/Sliver）  
3) 主题 + tokens（ThemeData/ThemeExtension）  
4) 状态管理（Riverpod/BLoC 二选一，先跑通 async 状态）  
5) 网络层（dio + 错误归一化）  
6) i18n（arb + intl）  
7) 测试（unit/widget/golden）  

## 3) 工程化补强点（对位前端经验）

- “边界校验”：TS 里你用 Zod，Flutter 里你也需要 DTO 校验/容错（不然数据会把 UI 撞崩）
- “不可变更新”：把 reducer 思维带过来（copyWith/freezed）
- “设计系统”：tokens → 组件库 → 页面（对位 shadcn）

## 4) 练手项目建议（从小到大）

- Todo（我在第四部分带你做）：主题/响应式/i18n/请求/缓存
- Settings（多语言/主题/持久化）
- Master-detail（平板/桌面布局）

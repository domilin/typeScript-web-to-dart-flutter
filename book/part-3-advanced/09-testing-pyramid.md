# 09｜测试金字塔：Vitest/RTL/Playwright ↔ Flutter test 体系

这一章我会带你：用你熟悉的前端测试心智，把 Flutter 的 unit/widget/golden/integration 测试串成可执行策略，并给出常见写法模板。

## 概念解释（先讲框架层）

### 1) 对位关系（心智模型）

| 前端 | 目的 | Flutter |
|---|---|---|
| Vitest/Jest（单测） | 纯逻辑、纯函数、工具类 | `package:test` / `flutter test`（unit） |
| React Testing Library（组件测试） | 组件渲染与交互 | `flutter_test`（widget test） |
| Storybook + 视觉回归（Chromatic 等） | UI 视觉稳定 | golden test |
| Playwright/Cypress（e2e） | 端到端流程 | `integration_test` |

### 2) 推荐测试金字塔

1) unit（最多）：domain/usecase/mapper/validator  
2) widget（其次）：关键交互组件/页面  
3) golden（少量）：设计系统组件、关键页面  
4) integration（最少）：登录/支付/关键主流程  

## 然后我用对照例子讲

### 1) unit：测试不可变更新（对位你在 TS 里测 reducer）

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test('toggle updates immutably', () {
    final t = Todo(id: '1', title: 'a', done: false, createdAt: DateTime(2024));
    final next = t.copyWith(done: !t.done);
    expect(next.done, true);
    expect(identical(next, t), false);
  });
}
```

### 2) widget：渲染与交互（对位 RTL）

```dart
testWidgets('tapping checkbox triggers callback', (tester) async {
  var called = false;

  await tester.pumpWidget(
    MaterialApp(
      home: TodoRow(
        title: 'a',
        done: false,
        onToggle: () => called = true,
      ),
    ),
  );

  await tester.tap(find.byType(Checkbox));
  await tester.pump();

  expect(called, true);
});
```

（上面假设你的 `TodoRow` 暴露了 `onToggle`；这就是“为可测试性设计 API”。）

### 3) Riverpod：provider 测试（对位 hooks/queries 的测试）

思路：用 `ProviderContainer`，override 依赖（例如 API）。

```dart
final container = ProviderContainer(
  overrides: [
    todoApiProvider.overrideWithValue(FakeTodoApi()),
  ],
);
addTearDown(container.dispose);
```

## 我在项目里怎么做

- widget test 很像 RTL：你用 `find` 找 widget，用 `tap/enterText` 模拟交互，用 `pump` 推进一帧。
- golden test 不要滥用：它对主题/字体/平台差异敏感；只给“设计系统组件/关键页面”做即可。
- 集成测试尽量跑在 CI 的稳定设备/模拟器配置上（避免 flaky）。

## 我给你留个练习

1) 给 Todo 的 `toggle/remove` 写 unit test（只测纯逻辑）。  
2) 给 Todo 页面写一个 widget test：输入标题点击添加后，列表出现新项。  


# 桌面书签 UI 规范 (DesktopBookmark UI Design Spec)

当 kel 讨论或修改 DesktopBookmark（桌面书签）项目的界面时，必须遵守以下 UI 规范。这些规范是从多次对话中迭代沉淀的 kel 个人审美要求。

## 核心原则

1. **玻璃流动感** — 折叠态、下拉菜单、卡片全部走毛玻璃路线，`backdrop-filter` + 半透明底色。折叠书签 56×56 正方形，`::after` 伪元素流动光带 + `@keyframes` 呼吸阴影。
2. **零 emoji** — 所有图标用 CSS `::before`/`::after` 手绘，绝不使用 emoji 或系统图标字体。
3. **去 AI 感** — border-radius 克制（6-14px），不要过度圆润。低饱和度色板。
4. **去硬边** — 分隔用 `box-shadow` 替代 `border`。卡片用 `inset` 微光分层。
5. **5 套主题** — 墨色/纸色/朱砂/青黛/暗夜，所有界面元素通过 CSS 变量完整定义，不遗漏任何组件。
6. **自定义下拉** — 分类选择永远不用 `<datalist>` 或系统下拉，必须用自定义 `cat-menu` 毛玻璃菜单，支持 ↓↑ 键导航、Enter 选中、Esc 关闭。
7. **书法字体** — `"LXGW WenKai","STKaiti","KaiTi","Microsoft YaHei"` 降级链。
8. **禁止内联** — 不用 `style=` 属性，全部走 CSS 类。

## 折叠态

- 56×56px 正方形，不要长方形
- `radial-gradient` 内高光 + `backdrop-filter: blur(10px)`
- 居中"引"字，白色半透明
- 可拖动 + 12px 磁吸 + 位置记忆
- 展开后面板右边缘距屏幕 ≥60px

## 分类下拉实现

- `position: absolute`（不用 fixed，Electron 透明窗兼容问题）
- 数据源：`[...state.todos, ...state.ideas, ...state.knowledge]` 实时取分类
- 当前输入值若为新标签，排最前面并标注"新增"
- 无分类时显示提示文本
- 5 套主题各定义 `--menu-bg/--menu-border/--menu-text/--menu-text-active/--menu-hover`

## 报告区

- 日报/周报/年报：三段横向胶囊按钮，`flex:1; border-radius:999px; width:auto`
- 不能是 grid 网格小方块
- 保存报告列表卡片化

## 卡片与条目

- `background: var(--bg-card)` + `box-shadow`，不用 `border`
- hover: `translateY(-1px)` + `shadow-hover`
- 分类标签胶囊形，`var(--accent-light)` + `var(--accent)`

## 错误排查

- 如果下拉不出现：检查 `position`（应 absolute 不是 fixed），检查 z-index，检查数据是否加载（控制台 `[cat]` 日志）
- 如果墨色和纸色一样：检查 `body.theme-ink` CSS 块是否完整未被覆盖

## 不允许

| ❌ | ✅ |
|----|-----|
| emoji 图标 | CSS 伪元素 |
| 原生 datalist | 自定义 cat-menu |
| `position: fixed` 菜单 | `position: absolute` |
| border 分割 | box-shadow |
| 内联 style= | CSS 类 |
| 网格小方块按钮 | 横向胶囊按钮 |
| 折叠态长方形 | 56×56 正方形 |
| 面板靠边 | ≥60px 边距 |

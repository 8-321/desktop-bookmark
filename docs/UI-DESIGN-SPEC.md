# 桌面书签 UI 设计规范

> 从对话中提取的 kel 对 DesktopBookmark 的全部界面美观度要求，供后续开发和 AI 辅助参考。

---

## 一、总原则

| 原则 | 说明 |
|------|------|
| **玻璃流动感** | 折叠态、下拉菜单、卡片全部走毛玻璃路线， backdrop-filter + 半透明底色 |
| **不用 emoji** | 所有图标用 CSS `::before`/`::after` 手绘，零 emoji |
| **去 AI 感** | border-radius 宁小勿大（6-12px），不要过度圆润 |
| **低饱和度** | 色彩克制，纸色/墨色/朱砂/青黛/暗夜都用低饱和色板 |
| **去硬边** | 分隔线用 `box-shadow` 替代 `border`，卡片用内微光 `inset` |
| **书法字体** | `"LXGW WenKai","STKaiti","KaiTi","Microsoft YaHei"` 降级链 |

---

## 二、折叠态书签

- **尺寸**：56×56px 正方形，不要长方形
- **风格**：毛玻璃 + 流动光带 + 呼吸阴影
  - 背景：`radial-gradient` 模拟内高光
  - 遮罩：`backdrop-filter: blur(10px)`
  - 光带：`::after` 伪元素 `linear-gradient` 从左滑到右
  - 呼吸：`@keyframes bkPulse` 循环 box-shadow
- **位置**：可拖动到任意屏幕边缘，12px 磁吸，位置记忆
- **文字**：居中"引"字，白色半透明，text-shadow 微阴影
- **展开/折叠**：点击切换，展开 430×640 面板
- **面板距离**：右边缘距屏幕至少 60px，不要靠边

```css
/* 关键变量 */
--bookmark-size: 56px;
--glass-bg: radial-gradient(circle at 30% 25%, rgba(255,255,255,.12), rgba(255,255,255,.02) 60%, rgba(0,0,0,.3));
--glass-blur: blur(10px);
```

---

## 三、主题系统（5 套）

所有界面元素通过 CSS 变量切换，必须在每个主题块里完整定义：

| 主题 | class | 主色调 | accent | 菜单底 |
|------|-------|--------|--------|--------|
| 墨色 | `theme-ink` | #1a1d2e 深蓝 | #4d6fcf 蓝 | rgba(26,29,46,.96) |
| 纸色 | `theme-paper` | #efe6d8 暖黄 | #7f5240 棕 | rgba(239,230,216,.98) |
| 朱砂 | `theme-cinnabar` | #2a1f1c 红棕 | #c4654a 红 | rgba(42,31,28,.96) |
| 青黛 | `theme-jade` | #172420 墨绿 | #5a9a7a 绿 | rgba(23,36,32,.96) |
| 暗夜 | `theme-dark` | #0a0a0e 黑 | #5f6bcc 紫 | rgba(10,10,14,.97) |

**必须覆盖的变量**：`--bg-panel`, `--bg-card`, `--bg-hover`, `--bg-input`, `--text`, `--text-secondary`, `--text-muted`, `--accent`, `--accent-light`, `--border`, `--border-focus`, `--shadow-card`, `--shadow-hover`, `--menu-bg`, `--menu-border`, `--menu-text`, `--menu-text-active`, `--menu-hover`, `--r`, `--ri`, `--rb`

---

## 四、分类下拉

- **触发**：分类输入框 `focusin` 或按 `↓` 键
- **导航**：`↑↓` 移动高亮，`Enter` 选中，`Esc` 关闭
- **数据源**：实时读取 `state.todos + state.ideas + state.knowledge` 中所有分类
- **新增**：输入框里的新标签自动排最前面，标注"新增"
- **空态**：无分类时显示提示文字"暂无分类 — 输入新标签后按回车保存"
- **样式**：`position:absolute`（不用 fixed，Electron 透明窗兼容），5 套主题毛玻璃底色
- **禁止**：不用原生 `<datalist>`（白底白框），不用系统下拉控件
- **箭头指示**：输入框右侧 CSS SVG 三角箭头

---

## 五、顶部标签栏

- 柔和胶囊分段，不要小方框按钮
- 背景微光 `rgba(255,255,255,.04)`
- 圆角 10-12px，hover 时 accent-light 背景
- 激活态 `box-shadow: inset 0 -2px 0 var(--accent)`
- 分隔用 `box-shadow: 0 1px 0 var(--border)`，不用 `border-bottom`

---

## 六、输入卡片区

- 待办/想法/知识输入区用轻卡片容器包裹
- `background: linear-gradient` 微弱光感
- `border-radius: 16px`
- `box-shadow: inset + 外阴影` 制造分层感
- 输入框本身保持 `var(--bg-input)` + `var(--border)` 细边

---

## 七、报告区

- **按钮**：三段式横向胶囊（日报/周报/年报），不用 grid 网格小方块
- 容器：和输入区一样的轻卡片包裹
- 按钮：`flex:1; border-radius:999px; width:auto`（覆盖全局 btn-primary 的 100%宽度）
- **保存列表**：卡片化展示，每条有 hover 效果
- **不允许**：内联 `style=`，全部走 CSS 类

---

## 八、卡片 & 条目

- 待办/想法/知识条目：`background: var(--bg-card)` + `box-shadow` 分层，不用 `border`
- hover：`transform: translateY(-1px)` + `box-shadow: var(--shadow-hover)`
- 分类标签：胶囊形 `cat-tag`，`var(--accent-light)` 背景 + `var(--accent)` 文字
- 知识图片：`max-height:120px` 缩略图，点击 `expanded` 类放大到 400px

---

## 九、CSS 图标

用 `::before` + `::after` 伪元素绘制，不用 emoji：
- `.icon-todo`：方形 + √ 勾
- `.icon-idea`：灯泡形（圆+矩形）
- `.icon-knowledge`：文件 + 勾线
- `.icon-restore`：弧形箭头
- `.icon-report`：文档 + 横线
- `.icon-search`：圆形 + 斜线

---

## 十、知识图片粘贴

- Ctrl+V 在知识输入框粘贴剪贴板图片
- 转 base64 data URL 存入 state
- 渲染时 `<img>` 标签，点击切换 `.expanded` 放大

---

## 十一、应用图标

- 256×256 PNG + 多分辨率 ICO (256/128/64/48/32/16)
- 深蓝底色 + 毛玻璃书签 + 金色微光边框 + "引"字
- 托盘图标 16×16 同步生成

---

## 十二、禁止清单

| ❌ | ✅ 替代 |
|----|--------|
| emoji 图标 | CSS 伪元素手绘 |
| 粒子特效/飘浮点 | 干净无特效 |
| 原生 datalist 下拉 | 自定义 cat-menu |
| 硬 border 分割线 | box-shadow 分割 |
| 大面积 border-radius | 6-14px 克制圆角 |
| 亮色/高饱和 | 低饱和色板 |
| 内联 style= | CSS 类统治 |
| 折叠态长方形 | 56×56 正方形 |
| 面板靠边吸附 | ≥60px 边距 |

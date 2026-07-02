# 默认包装风格 / Default Packaging Style

## 风格名称 / Style Name

**Remotion Native Material Cards / Remotion 原生材质卡片**

这是 `$video-auto-edit` 的唯一默认视觉包装风格。它不使用旧式科技仪表盘、全屏系统框、扫描网格、仪表盘外壳或整条视频进度条，而是以 Remotion + React + GSAP 可直接复刻的模块卡片、关键词排版卡和信息图环绕卡来解释口播关键词。

This is the only default visual packaging style for `$video-auto-edit`. It does not use old tech dashboard styling, full-screen frames, scan grids, dashboard shells, or global video progress bars. It uses Remotion-native modular cards, kinetic type cards, and infographic orbit cards that can be rebuilt directly with Remotion, React, and GSAP.

## 核心气质 / Core Feel

- 非仪表盘、非扫描系统感、非全屏系统框。
- 像高级视频包装组件，而不是后台数据面板。
- 卡片有材质、阴影、边框和层级，但结构必须能用 React `div`、SVG、CSS 渐变和 GSAP 复刻。
- 每句话只围绕 1 个字幕关键词或短语触发包装，不把整句字幕做成大字卡。
- 人物视频优先保护脸部、嘴部、手势和中下方字幕安全区。

- Non-dashboard, non-scanning-system, non-fullscreen-frame feel.
- Premium video packaging components rather than backend analytics panels.
- Cards have material, shadows, borders, and hierarchy, while remaining reproducible with React `div`, SVG, CSS gradients, and GSAP.
- Each spoken sentence triggers packaging around one subtitle keyword or short phrase, not full sentence text.
- Speaker footage must protect the face, mouth, gestures, and lower-center subtitle safe zone.

## 默认组件 / Default Components

### 1. Remotion Modular Cards / 模块卡片

适用：

- 工具名、步骤、状态、能力点、短概念解释。
- 需要清晰但不抢脸的侧边信息。

结构：

- 1-2 张半哑光深色卡片，放在左侧、右侧或上半区。
- 圆角 14-24px，透明渐变边框，轻微颗粒，软阴影。
- 卡片内包含线性图标、英文短标签、中文关键词。
- 单张主卡宽度建议为画面宽度 18%-32%，不横跨人物脸部。

动效：

- `cueFrame - 6` 卡片从 `y: 24, opacity: 0, scale: .96` 入场。
- `cueFrame` 关键词文字完成入场并轻微 pop。
- `cueFrame + 4` 边框或关键词区域弱发光一次。
- SVG 连接线可用 `strokeDashoffset` 绘制，必要时带循环光晕点。

### 2. Kinetic Type Cards / 动态关键词排版卡

适用：

- 强调观点词、反问词、结论词、教程关键动作。
- 需要比普通字幕更有节奏的关键词强化。

结构：

- 大号关键词 + pill 高亮 + 下划线/短色条 + 小贴纸标签。
- 主词使用 44-68px 横屏字号或 38-58px 竖屏字号，`font-weight: 850-950`。
- 关键词卡放在左右留白，不放到嘴部或字幕安全区。

动效：

- 关键词用 `clip-path` 或遮罩横向揭示。
- pill 从词中心扩张，跟随关键词落点出现。
- 下划线用 `scaleX` 从左到右绘制。
- 小标签带 4-8 度旋转回正，落点轻微弹跳。

### 3. Infographic Orbit Cards / 信息图环绕卡

适用：

- 一个来源分出多个方向、三种方案、流程路径、概念关系。
- 需要展示关联和传输，而不是单张静态卡片。

结构：

- 左侧或右侧源节点卡 + 2-3 条 SVG 曲线 + 目标 pill 标签。
- 曲线从源节点边缘连接到目标卡边缘，长度根据卡片实际位置计算。
- 目标卡包含图标、英文短标签、中文短词。

动效：

- 源节点先出现。
- 曲线按关键词逐条绘制。
- 每条连线上必须有柔和光晕点沿线循环移动，显性表现关联与传输。
- 光点接近目标卡时，目标卡点亮或 pop 入场。

## 字体 / Typography

```text
主标题：Arial Black / HarmonyOS Sans SC Black / Source Han Sans Heavy / PingFang SC Heavy。
关键词：HarmonyOS Sans SC Bold / Source Han Sans Heavy / PingFang SC Heavy / Inter ExtraBold。
辅助标签：PingFang SC Semibold / Inter SemiBold / IBM Plex Sans SemiBold。
代码或技术标签：JetBrains Mono / SF Mono / Menlo。
```

规则：

- Remotion 组件必须显式设置 `fontFamily`。
- 中文关键词最多 2 行，每行 1-8 个字。
- 英文标签只做短识别符，例如 `PROMPT`、`OUTPUT`、`KEY`、`WEB`。
- 字距不能为负值；技术标签可用 `letter-spacing: 0.06em-0.14em`。

## 颜色 / Color

```text
background overlay: rgba(3,5,10,0.20-0.62)
card fill: rgba(8,12,20,0.60-0.84)
primary blue: #37A6FF / #2F7CFF
green: #35E982
coral: #FF6B7A
yellow: #FFCC4A
white: #FFFFFF / #F4F8FF
border: rgba(255,255,255,0.14-0.24) or semantic accent 45%-65%
```

整条视频最多使用 1 个主色 + 2 个语义强调色。不要做单一蓝色系统面板，也不要让发光铺满全屏。

Use at most one primary color plus two semantic accent colors in one video. Do not make a single-blue system-panel look, and do not let glow cover the whole screen.

## 材质 / Material

- 默认卡片：半哑光深色卡片，透明渐变边框，轻微颗粒，内高光和外阴影。
- 关键词卡：半透明深色底 + 彩色 pill + 手绘感下划线或短色条。
- 信息图线条：SVG 曲线，圆头线帽，柔和 `drop-shadow`，移动光晕点。
- 不使用旧式科技角框、扫描网格、全局进度条、雷达仪表盘外壳。

## 布局与安全区 / Layout and Safe Zones

- 人脸、嘴部、眼睛、胸前麦克风和手势优先级最高。
- 卡片优先放左侧、右侧或上半区。
- 中下方字幕安全区默认不放大卡片。
- 单句最多 1 张主卡 + 2 个标签；多元素必须错峰出现。
- 如果需要在人物中间做图形，先做人像遮罩，让包装位于人物下方或后方。

## 禁用 / Do Not

- 不使用旧式科技仪表盘包装。
- 不生成全屏系统框、扫描线、网格、雷达图、仪表盘外壳。
- 不生成顶部或底部整条视频全局进度条。
- 不使用大面积扫光、闪白或强转场光效。
- 不让卡片遮挡脸、嘴、字幕和主要手势。
- 不把完整口播句子作为大字卡片。
- 不把所有元素一次性全部出现；必须按字幕关键词落点触发。

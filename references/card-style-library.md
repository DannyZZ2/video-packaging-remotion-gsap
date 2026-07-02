# 默认卡片风格库 / Default Card Style Library

> 用途：`$video-auto-edit` 的内置风格库。默认只使用一个主风格；用户明确指定时，可切换到其它内置风格。  
> Purpose: built-in style library for `$video-auto-edit`. Use one default primary style unless the user explicitly selects another built-in style.

## Built-In Style Policy / 内置风格策略

Built-in styles:

内置风格：

```text
Default: Remotion Native Material Cards / Remotion 原生材质卡片
Optional: Holographic Glass HUD / 全息玻璃 HUD
Optional: Frosted Glass Packaging / 毛玻璃包装
Optional: Reference HUD Pattern / 参考 HUD 信息图
```

Use `Remotion Native Material Cards` by default. Use optional built-in styles only when the user explicitly asks for them, provides a matching reference, or the approved packaging plan names that style. Do not generate full-screen system frames, radar dashboards, global progress bars, or analytics-panel shells unless they are part of the explicitly selected style.

默认使用 `Remotion Native Material Cards / Remotion 原生材质卡片`。只有用户明确要求、提供匹配参考，或已确认包装方案指定时，才使用可选内置风格。不要生成全屏系统框、雷达仪表盘、全局进度条或数据分析面板外壳，除非它们属于已明确选择的风格。

If the user provides a custom Markdown style or reference image, treat it as an external style brief for that task only. It is not added to the built-in style library unless the user explicitly asks to update the skill.

如果用户提供自定义风格 Markdown 或参考图片，只把它作为该任务的外部风格 brief；除非用户明确要求更新 skill，否则不加入内置风格库。

## Default Style / 默认风格

### Remotion Native Material Cards / Remotion 原生材质卡片

适合：

- AI 工具讲解、剪辑包装、教程步骤、关键词强化、流程说明、观点解释。
- 需要轻量、清晰、可按字幕关键词触发的包装。
- 需要用 Remotion + React + GSAP 直接复刻，而不是依赖复杂位图或不可控生成图。

视觉结构：

- 实拍画面上叠加材质化包装卡片。
- 组件以模块卡片、动态关键词排版卡、信息图环绕卡为主。
- 卡片放左右侧、上半区或空白墙面，避开脸、嘴、麦克风、手势和字幕安全区。
- 卡片有半透明深色底、渐变边框、轻微颗粒、内高光、外阴影和明确字体层级。
- 连线类动效必须使用 SVG 曲线和循环移动的柔和光晕点。

## Optional Built-In Style / 可选内置风格

### Holographic Glass HUD / 全息玻璃 HUD

适合：

- prompt、命令、AI task、生成状态、工具面板。
- 需要蓝紫全息、命令面板、状态 chip、短任务提示的片段。
- 用户明确要求“全息玻璃 HUD”或提供同类参考图时。

视觉结构：

- 黑色/深蓝黑玻璃主卡，青色和紫色双层描边。
- 小型状态 chip、命令行短文本、角标线框。
- 背景可有极淡网格和点阵，但不做全局进度条。
- 图标使用简单线性 SVG，不使用复杂图片图标。
- 单屏优先 1 张主面板 + 1-2 个状态 chip，避免压住人物脸部和嘴部。

Remotion 实现：

```text
container: backdrop-filter blur(10px-16px), dark blue-black glass gradient
border: cyan/violet double stroke
shadow: weak outer glow + inset highlight
text: Inter Black / JetBrains Mono / Source Han Sans Heavy
motion: corner frame draw -> panel slide -> chip pop -> mono text type-in
```

动效建议：

- 关键词 cue 前 4-6 帧绘制角标线框。
- cue 点主卡轻微透视滑入，`scale 0.96 -> 1`。
- 命令文字可用 6-12 帧打字机，但不要打完整长句。
- 状态 chip 在关键词后 8-14 帧 pop。
- 待机只做边框弱呼吸和光标闪烁。

禁用：

- 不做整屏扫描光。
- 不做顶部/底部全局进度条。
- 不把卡片做成纯色扁平矩形。
- 不遮挡脸、嘴、字幕安全区。

### Frosted Glass Packaging / 毛玻璃包装

适合：

- 口播、教程、访谈、AI 工具讲解中需要高级但不强科技感的轻包装。
- 左侧竖向信息卡、右侧小提示卡、底部身份条、能力点摘要。
- 用户明确要求“毛玻璃”“透明玻璃”“明亮玻璃卡片”或提供同类参考图时。

视觉结构：

- 清透毛玻璃卡片叠在实拍画面上，透明但文字必须清楚。
- 左侧可用一张竖向主卡，右侧放 1-2 张小卡，底部可放短身份条或主题条。
- 使用白色、浅蓝、淡粉边缘光，避免过重雾面、脏噪点和大片灰蒙蒙遮罩。
- 不使用卡片里的白点颗粒；扫光只允许入场后一次，不循环。
- 卡片避开脸、嘴、麦克风和字幕区，优先贴左右空白区域。

Remotion 实现：

```text
container: backdrop-filter blur(8px-14px) saturate(1.35-1.55) contrast(1.04-1.08)
fill: rgba(255,255,255,0.035-0.15) + subtle blue/rose transparent gradient
border: rgba(255,255,255,0.38-0.55) + cyan/rose rim glow
shadow: soft outer shadow + subtle inset highlight
icon: translucent rounded square with semantic glow
motion: card slide/pop -> one-shot shine sweep -> icon/text stagger -> subtle idle float
```

动效建议：

- 卡片从画面边缘滑入并轻微 `scale 0.96 -> 1`。
- 入场完成后做一次 12-24 帧斜向扫光，之后停止。
- 图标、标题、说明文字错峰 3-6 帧进入。
- 待机只做 1-2px 轻微漂浮和弱边缘呼吸。
- 退场淡出或侧滑，不做闪白和强扫描。

禁用：

- 不做旧式 HUD 雷达、命令行扫描或全屏系统框。
- 不做循环扫光。
- 不在卡片上生成白点颗粒。
- 不把玻璃层做得灰雾厚重导致人物和文字都发糊。

### Reference HUD Pattern / 参考 HUD 信息图

适合：

- 信息图式解释：体检报告、评分、合规检查、最小改动、分支流向、终端步骤、自动报告。
- 需要把一句话拆成“报告/图表/表格/路径/流程”的讲解段。
- 用户明确要求参考 `ReferenceHudPatternDemo`、参考 HUD 信息图、流量评分、合规表格、路径分流等效果时。

视觉结构：

- 深蓝黑背景或暗色玻璃面板，左上角使用竖向发光条 + 英文标题 + 中文副标题。
- 面板以 SVG 图表、表格、终端、分支路径为核心，不用整屏扫描光。
- 语义色固定：蓝=信息/主路径，绿=通过/正确，黄=警告/评分，红=风险/错误。
- 连线必须使用 SVG path，线条中间有循环移动的柔和光晕点，用来显性表现关联与传输。
- 比默认风格信息密度更高，只在用户明确选择时使用。

Remotion 实现：

```text
header: vertical glow bar + uppercase label + Chinese subtitle
panel: dark glass gradient + semantic stroke + weak glow
chart: SVG radar/gauge/table/branch/terminal primitives
connector: strokeDashoffset draw + frame-driven moving glow dot
text: Inter Black / JetBrains Mono / Source Han Sans Heavy
motion: header reveal -> panel draw -> rows/cards/charts by keyword cue -> connector glow dot loop
```

动效建议：

- 标题先出现，主面板 6-10 帧后描边绘制。
- 表格行、雷达角标、终端步骤按字幕关键词逐项点亮。
- 连线先绘制，再让光晕点沿路径循环运动；光点不能停在终点。
- 仪表盘数值从 0 增长时，0 帧状态不能提前显示右侧进度。
- 卡片间距、连线长度和目标卡宽度必须由内容尺寸决定，避免视觉间距不一致。

禁用：

- 不做整条视频顶部/底部全局进度条。
- 不把标签放在雷达图内部；雷达标签应贴近最外侧多边形角点。
- 不让大面板遮挡人物脸、嘴或字幕区。

## Typography / 字体

```text
主标题：Arial Black / HarmonyOS Sans SC Black / Source Han Sans Heavy / PingFang SC Heavy，44-68px。
关键词：HarmonyOS Sans SC Bold / Source Han Sans Heavy / PingFang SC Heavy / Inter ExtraBold，38-68px。
辅助说明：PingFang SC Semibold / Inter SemiBold，18-30px。
技术标签：JetBrains Mono / SF Mono / Menlo，16-24px。
```

规则：

- 必须显式设置 `fontFamily`，不要依赖浏览器默认字体。
- 中文关键词必须短，每行优先 1-8 个字。
- 英文标签只做识别符，不写完整句子。
- 不使用负字距。

## Component Variants / 组件变体

| Component | 用途 | 结构 | 动效 |
|---|---|---|---|
| `RemotionModularCard` | 工具名、步骤、状态、能力点 | 图标容器 + 英文标签 + 中文关键词 + 半哑光卡片 | y 位移淡入，轻微 scale pop，边框弱发光 |
| `KineticTypeCard` | 观点词、结论词、动作词 | 大关键词 + pill 高亮 + 下划线 + 小贴纸 | clip-path 揭示，pill 扩张，下划线 scaleX 绘制 |
| `InfographicOrbitCard` | 多方向路径、流程关系 | 源节点 + SVG 曲线 + 目标 pill 标签 | 路径绘制，光晕点沿线循环，目标卡点亮 |
| `KeywordChip` | 工具名、概念词、分类 | 小胶囊标签，语义色描边 | 弹出、吸附、短暂 glow pulse |
| `IconInfoCard` | 功能、模块、能力点 | 线性图标 + 双语短标签 | 图标先入，文字后入，边框点亮 |
| `ComparisonSwapCard` | 旧词到新词、错误到正确 | 左红词 + 箭头 + 右绿词 | 红词轻震，箭头绘制，新词弹入 |
| `MouseClickCallout` | 操作演示、点击动作 | 鼠标指针 + 光圈 + 小标签 | 指针移动，点击光圈 6-10 帧扩散 |
| `MiniTerminalTag` | prompt、命令、工具状态 | 短命令条，不做大终端面板 | 短打字机，光标闪烁，局部高亮 |
| `HolographicGlassPanel` | prompt、命令、AI task、生成状态 | 深色玻璃主卡 + 青紫双层描边 + 状态 chip + mono 短命令 | 角标绘制，面板滑入，chip pop，mono 打字 |
| `FrostedGlassPanel` | focus、身份条、功能摘要、侧边提示 | 清透毛玻璃卡 + 浅色渐变边缘光 + 图标容器 + 短标题 | 侧滑/弹入，一次扫光，图标文字错峰，轻微待机漂浮 |
| `ReferenceHudInfoPanel` | 报告、评分、合规、流程、终端步骤 | 左上标题系统 + 深色玻璃面板 + SVG 图表/表格/路径 | 面板描边绘制，行/图表错峰，连线光晕点循环 |

## Remotion Implementation / Remotion 实现

```text
card: React div + CSS gradient border + backdrop-filter or translucent fill
icon: inline SVG or lucide icon
connector: SVG path with strokeDashoffset draw
moving dot: frame-driven point along SVG/cubic path, rendered as soft glow circle
keyword reveal: clip-path / mask / scaleX
motion timing: cueFrame - 6 entry, cueFrame highlight, cueFrame + 8 settle
```

## Motion Rules / 动效规则

- 每个有意义句子按字幕关键词选择一个匹配组件。
- 所有包装动效必须由关键词 cue 触发，不按均分时间随机出现。
- 入场 180-360ms，使用 `power2.out` 或 `back.out(1.2)`。
- 强调 80-160ms，关键词 pop、描边点亮或下划线绘制。
- 待机只允许轻微呼吸、弱边框脉冲、光点循环或小标签漂浮。
- 退场 120-240ms，淡出或轻微位移，不做强闪光转场。
- 同组元素错峰 3-8 帧。

## Do Not / 禁用

- 不使用旧式科技仪表盘包装。
- 不生成顶部/底部整条视频全局进度条。
- 不生成全屏系统框、雷达仪表盘或分析报告大面板。
- 不把卡片做成纯色扁平矩形。
- 不把完整口播句子作为大字卡片。
- 不让连线光点停在终点，必须沿路径循环运动。
- 不遮挡脸部、嘴部、手势和字幕安全区。

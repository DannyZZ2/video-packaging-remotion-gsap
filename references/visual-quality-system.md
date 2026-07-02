# 视觉质量系统 / Visual Quality System

> 用途：约束 `$video-auto-edit` 的视频包装质感，重点解决字体普通、卡片廉价、层级不清、遮挡主体和动效过散的问题。  
> Purpose: constrain `$video-auto-edit` packaging quality, especially typography, card polish, hierarchy, subject safety, and motion consistency.

## 核心原则 / Core Principles

1. 先做“设计系统”，再做单个动效。所有包装都必须从字体、组件、颜色、层级和安全区出发。
2. 包装元素服务口播，不抢人物主体。短视频包装应该是“信息增强”，不是把画面做成海报。
3. 一条视频只允许 1 个主视觉方向、1 套字体组合、1-2 个强调色和 3-5 个组件类型。
4. 任何包装方案都必须写明：字体、字重、字号区间、组件类型、颜色、层级、安全区和退场方式。
5. Remotion 实现后必须做截图验收；发现字体 fallback、遮挡脸嘴、文字过大或卡片过密，要先修正再让用户看。
6. 如果用户没有提供更强的自定义风格，默认使用 `card-style-library.md` 中的 `Remotion Native Material Cards`，再按字幕语义选择模块卡片、关键词排版卡或信息图环绕卡。只有用户明确指定时才使用可选内置风格：`Holographic Glass HUD`、`Frosted Glass Packaging`、`Reference HUD Pattern`。

## 字体系统 / Typography System

| 角色 | 中文优先 | 英文/数字优先 | 用途 |
|---|---|---|---|
| 主标题 | HarmonyOS Sans SC Black, Source Han Sans Heavy, PingFang SC Heavy | Arial Black, Inter Black, Anton | 章节标题、强观点、封面式大字 |
| 关键词 | Source Han Sans Heavy, PingFang SC Heavy, HarmonyOS Sans SC Bold | Inter ExtraBold, DIN Alternate Bold | 关键词弹出、替换、高亮、标签 |
| 正文小字 | PingFang SC Semibold, Source Han Sans Medium | Inter SemiBold, IBM Plex Sans SemiBold | 卡片说明、步骤短句 |
| 技术标签 | SF Mono, JetBrains Mono, Menlo | JetBrains Mono, SF Mono | prompt、命令、状态标签 |

### 字体硬规则 / Typography Rules

- 不要裸用浏览器默认字体；Remotion 里必须显式设置 `fontFamily`。
- 中文标题必须使用 Heavy/Black 级别字重；普通 `font-weight: 400` 禁止用于包装主视觉。
- 关键词最多 2 行，每行不超过 8 个中文字符或 4 个英文词。
- 小字说明每行不超过 12 个中文字符或 5 个英文词。
- 字距不要用负值；技术短标签可以使用 `letter-spacing: 0.06em-0.14em`。
- 标题行高控制在 `0.9-1.08`；卡片正文行高控制在 `1.15-1.35`。
- 如果本机没有推荐字体，使用已安装的同类字体，但必须在设计稿和实现说明里写出 fallback。

## 字号与层级 / Size and Hierarchy

| 层级 | 1080x1920 竖屏建议 | 1920x1080 横屏建议 | 使用限制 |
|---|---:|---:|---|
| Hero 标题 | 56-92px | 72-128px | 只用于开头钩子或章节，不常驻 |
| 关键词 | 38-68px | 44-84px | 1-4 个字最佳，出现后快速收束 |
| 卡片标题 | 28-42px | 30-48px | 必须比正文明显粗 |
| 卡片正文 | 22-30px | 22-34px | 不写长段落 |
| 技术标签 | 16-24px | 18-28px | 全大写或短中文标签 |

包装视觉权重从高到低：人物脸部/主体 > 当前口播关键词 > 说明卡片 > 标签 > 装饰线条。任何装饰线条不能比关键词更抢眼。

## 组件库 / Component Library

动效方案和 Remotion 实现优先从这些组件中选，不要每段重新发明样式。

| 组件 | 结构 | 适用 | 动效建议 |
|---|---|---|---|
| `RemotionModularCard` | 图标容器 + 英文短标签 + 中文关键词 + 半哑光深色卡片 | 工具名、步骤、状态、能力点 | 侧滑/上浮入场，关键词 pop，边框弱发光 |
| `KineticTypeCard` | 大关键词 + pill 高亮 + 下划线 + 小贴纸 | 观点词、结论词、教程动作 | clip-path 揭示，pill 扩张，下划线 scaleX 绘制 |
| `InfographicOrbitCard` | 源节点 + SVG 曲线 + 目标标签卡 | 多方向路径、流程关系、方案分支 | 路径绘制，光晕点沿线循环，目标卡点亮 |
| `KeywordChip` | 胶囊标签 + 语义色描边 | 工具名、概念词、分类 | 弹出、变色、贴纸、吸附 |
| `IconInfoCard` | 线性图标 + 英文标签 + 中文短说明 | 功能、模块、能力点 | 图标先入，文字后入，边框脉冲 |
| `StepListCard` | 1/2/3 或 checkbox 列表 | 教程步骤、执行过程 | 勾选、行内高亮、短进度 |
| `ComparisonSwapCard` | 左右两卡或旧词到新词 | 纠错、反转、认知升级 | 旧卡暗下去，箭头绘制，新卡点亮 |
| `MouseClickCallout` | 鼠标指针 + 点击光圈 + 高亮 | 操作演示、按钮、prompt | 移动、点击、光圈 6-10 帧 |
| `MagnetFlow` | 标签吸附 + 连线节点 | 工作流、Agent、自动化链路 | 错峰飞入、磁吸、连线生长 |
| `MiniTerminalTag` | 短命令条 + 状态 chip | prompt、命令、工具状态 | 短打字机、光标闪烁、局部加载 |
| `HolographicGlassPanel` | 深色玻璃主卡 + 青紫双层描边 + 状态 chip + mono 短命令 | prompt、命令、AI task、生成状态、工具面板 | 角标绘制，面板滑入，chip pop，mono 打字 |
| `FrostedGlassPanel` | 清透毛玻璃卡 + 浅色边缘光 + 图标容器 + 短标题 | 口播侧卡、focus、身份条、功能摘要 | 侧滑/弹入，一次扫光，图标文字错峰，轻微待机漂浮 |
| `ReferenceHudInfoPanel` | 左上标题系统 + 深色玻璃面板 + SVG 图表/表格/路径 | 报告、评分、合规、流程、终端步骤 | 面板描边绘制，行/图表错峰，连线光晕点循环 |

组件半径优先 10-24px。除非参考风格明确要求，不要把所有卡片都做成大圆角胶囊。

## 颜色与材质 / Color and Material

- 背景遮罩：`rgba(0,0,0,0.20-0.62)`，不要把人物压成死黑。
- 主文字：白色或接近白色，阴影克制。
- 主强调：蓝色或珊瑚色二选一；辅助强调可用绿色或黄色，但整条视频最多 2 个强调色。
- 卡片底：深蓝黑或黑色半透明，透明度 60%-84%。
- 描边：1-2px，使用透明渐变边框或语义色描边。
- 发光只服务边框、图标、连线光点或关键词落点，不要大面积糊光。
- 禁止全屏扫光、顶部/底部整条视频进度条、全屏系统框和扫描网格。
- 使用 `Holographic Glass HUD` 时，允许局部青紫双层描边、弱点阵和短命令面板，但仍禁止全屏扫描光和全局进度条。
- 使用 `Frosted Glass Packaging` 时，允许清透明亮玻璃、浅蓝/淡粉边缘光和一次性扫光；禁止白点颗粒、循环扫光和灰雾厚重的玻璃层。
- 使用 `Reference HUD Pattern` 时，允许深色信息图面板、雷达/仪表/表格/路径 SVG 组件和语义色连线；必须显式选择该风格，且仍禁止全局进度条和挡脸大面板。

### 高级卡片材质 / Premium Card Material

默认卡片不要做成单层扁平矩形。参考 `card-style-library.md`，明确卡片材质、描边、内外光影、字体和内容层。卡片至少应包含：

- 半透明或半哑光深色底。
- 1px 透明渐变边框或语义色描边。
- 内侧高光或暗角。
- 软外阴影。
- 图标容器和文字层级。

By default, cards must not be single-layer flat rectangles. Follow `card-style-library.md` and specify material, border, inner/outer lighting, typography, and content layers.

## 布局与安全区 / Layout and Safe Zones

- 口播人物视频默认保留脸部、嘴部、手势和下方中间字幕安全区。
- 卡片优先放左侧、右侧或上三分区；底部只放短标签或字幕，不放大卡片。
- 竖屏人物居中时，左右边缘可放窄卡；人物偏右时，左侧可放主卡。
- 单屏最多 1 张主卡 + 2 个标签；超过时要分批出现。
- 包装元素应在当前句子结束后退场，避免堆积。
- 用户提供的图片、logo、UI 截图、透明 PNG、图标、贴纸或元素图，若名称/标签/可见文字匹配字幕关键词，优先作为该关键词的包装元素展示。
- 素材展示必须保留原始比例，并按画面安全区限制最大尺寸；不拉伸、不裁掉关键信息，不把小图强行放成大海报。
- 画面中有指、拖、划、圈选或画线手势时，素材或动画优先贴近指尖、路径终点或划线路径出现，但必须偏移 16-48px 避免盖住手部。
- 手势锚点和脸部/嘴部/字幕安全区冲突时，优先保护脸、嘴和字幕，把素材移动到最近的安全邻近区域，并在方案中说明偏移。
- 没有可靠手势位置时，不要猜测手势锚点；回到左侧、右侧或上半区的常规布局。

## 动效质感 / Motion Quality

- 入场：180-360ms，`power2.out` 或 `back.out(1.2)`，弹跳幅度小。
- 强调：80-160ms，关键词 pop、描边点亮、局部高亮。
- 待机：只允许轻微呼吸、边框脉冲、光点循环或光标闪烁，不要所有元素一直动。
- 退场：120-240ms，淡出或轻微位移，不做强转场闪烁。
- 错峰：同组元素间隔 3-8 帧，帮助观众分辨顺序。
- 任何抖动最多 2-3 次；不能让整条视频长期抖动。
- 卡片入场拆成主体、图标、文字、边框高光 4 个错峰步骤，不要整卡一次性淡入。

## 包装方案必须包含 / Required In Packaging Plans

每个动效段落必须额外写清：

- 字体：具体 `fontFamily`、字重、字号区间。
- 组件：从组件库选择，如 `RemotionModularCard`、`KineticTypeCard`。
- 层级：在人物、字幕、卡片、装饰之间的前后关系。
- 质量风险：是否可能挡脸、挡嘴、挡字幕、字太小或卡片太密。

## Remotion 实现验收 / Remotion QA

打开 Studio 前必须至少检查一个桌面尺寸截图；如果是竖屏视频，也检查竖屏构图。验收项：

1. 字体加载成功，没有 fallback 成普通系统默认字。
2. 没有遮挡脸部、嘴部和字幕安全区。
3. 关键词不是整句字幕；只显示短词或短语。
4. 卡片数量、发光、描边和文字密度可读。
5. 没有整条视频的顶部/底部全局进度条。
6. 动效段落覆盖每个有意义句子或节奏点，而不是只做第一句。
7. Studio 预览不渲染导出，等用户确认后再导出。
8. 卡片不是扁平纯色矩形；至少能看到材质底、内外光影、语义色描边和明确字体层级。

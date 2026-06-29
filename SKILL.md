---
name: video-auto-edit
description: Use when the user wants a reusable workflow for video editing decisions, subtitle-aligned visual packaging, keyword HUD/card animations, Remotion Studio preview, and final export using Remotion plus GSAP.
---

# video-auto-edit

# 视频自动剪辑包装工作流

## Core Rule / 核心规则

Run this as a confirmation-gated workflow. Do not edit, package, render, or export until the matching user confirmation is reached. If a required tool is missing, install that tool and keep the requested stack; do not switch to another animation or video framework.

这是一个分阶段确认的工作流。未到对应确认节点前，不剪辑、不包装、不渲染、不导出。如果缺少必要工具，直接安装对应工具并保持用户指定技术栈；不要换成其它动画或视频方案。

Required companion skills:

- Use the installed `$video-use` skill for video analysis, cutting, SRT handling, and packaging-plan design. If `$video-use` is not installed or cannot be loaded, bootstrap it automatically before continuing.
- Ask the user whether to use ElevenLabs for transcription. Use ElevenLabs only after the user chooses it and provides an API key; otherwise use Whisper-compatible local transcription.
- Use Remotion + GSAP for the animation implementation after the user approves the packaging design.

必要关联技能：

- 使用已安装的 `$video-use` skill 做视频分析、剪辑、SRT 处理和包装方案设计。如果 `$video-use` 未安装或无法加载，先自动自举安装后再继续。
- 先询问用户是否使用 ElevenLabs 进行转写。只有用户选择 ElevenLabs 并提供 API key 后才使用；否则使用 Whisper 兼容的本地转写方案。
- 用户确认包装设计后，用 Remotion + GSAP 实现动画包装。

## Dependency Bootstrap / 依赖自举

At the start of the workflow, check whether `$video-use` is available. If it is missing, reduce user setup friction by installing it automatically from its upstream repository:

流程开始时先检查 `$video-use` 是否可用。如果缺失，为降低用户使用成本，自动从上游仓库安装：

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py --repo browser-use/video-use --path . --name video-use
```

After installation:

安装后：

- Read the installed `video-use/install.md` only for general setup and the transcription option selected by the user.
- Install or request only hard requirements that cannot be skipped, such as `ffmpeg`, `ffprobe`, Python dependencies, a Whisper-compatible transcription tool, or an ElevenLabs API key when the user chooses ElevenLabs.
- Do not echo or log ElevenLabs API keys. Never commit `.env`.
- If the current Codex session cannot dynamically load the newly installed skill, read the installed `video-use/SKILL.md` directly for this session and tell the user to restart Codex after the current workflow to pick up the skill normally.
- If GitHub access, network, package manager permissions, Whisper setup, or ElevenLabs key validation fails, explain the exact missing requirement and ask the user only for that one unblocker.

- 读取已安装的 `video-use/install.md` 时，只参考通用安装步骤和用户选择的转写方案。
- 只安装或索取不能跳过的硬依赖，例如 `ffmpeg`、`ffprobe`、Python 依赖、Whisper 兼容转写工具，或用户选择 ElevenLabs 时需要的 API key。
- 不回显、不记录 ElevenLabs API key；不要提交 `.env`。
- 如果当前 Codex 会话不能动态加载新安装的 skill，本轮直接读取已安装的 `video-use/SKILL.md` 使用，并提醒用户当前流程结束后重启 Codex，以便后续正常识别。
- 如果 GitHub 访问、网络、包管理器权限、Whisper 安装或 ElevenLabs key 校验失败，只说明当前缺少的具体条件，并只向用户索取这一项阻塞信息。

## Transcription Choice / 转写选择

Before any transcription, editing analysis, SRT generation, or packaging timing work, ask the user whether to use ElevenLabs.

在任何转写、剪辑分析、SRT 生成或包装时间轴设计之前，先询问用户是否使用 ElevenLabs。

If the user chooses ElevenLabs:

如果用户选择 ElevenLabs：

1. Tell the user that ElevenLabs transcription uploads the video's audio to an external ElevenLabs service, then ask for explicit consent to upload the audio.
2. Ask the user to provide an ElevenLabs API key or confirm that `ELEVENLABS_API_KEY` is already available in the environment.
3. Use ElevenLabs/Scribe transcription with word-level timestamps only after both consent and API-key availability are confirmed.
4. Use word-level timestamps as the preferred source for cut boundaries, SRT timing, keyword animation timing, and subtitle-aligned packaging.
5. Keep the key out of logs and code. If it must be persisted for helper compatibility, store it only in the local tool's ignored `.env` file with restrictive permissions.

1. 告知用户 ElevenLabs 转写会把视频音频上传到外部 ElevenLabs 服务，然后明确询问是否同意上传音频。
2. 让用户提供 ElevenLabs API key，或确认环境变量 `ELEVENLABS_API_KEY` 已经可用。
3. 只有在用户同意上传且 API key 可用后，才使用 ElevenLabs/Scribe 转写和词级时间戳。
4. 优先用词级时间戳作为剪辑边界、SRT 时间、关键词动画时间和字幕对齐包装的依据。
5. 不把 key 写入日志或代码。如果为了 helper 兼容必须持久化，只能写入本地工具被忽略的 `.env`，并设置严格权限。

If the user does not choose ElevenLabs:

如果用户不选择 ElevenLabs：

1. Use an existing `whisper`, `faster-whisper`, `mlx-whisper`, or equivalent local Whisper command if available.
2. If no Whisper tool is available, install a Whisper-compatible option locally for the project environment, then transcribe.
3. Output separate SRT by default. Do not burn subtitles into the video unless the user explicitly asks.
4. For packaging design, use Whisper transcript text and timestamps as the timing source. If word-level timestamps are unavailable, use segment timestamps plus silence detection to avoid obvious cut or animation timing errors.

1. 如果环境里已有 `whisper`、`faster-whisper`、`mlx-whisper` 或等价本地 Whisper 命令，优先使用。
2. 如果没有 Whisper 工具，在当前项目环境中安装 Whisper 兼容方案后再转写。
3. 默认输出独立 SRT；除非用户明确要求，否则不烧录字幕。
4. 包装设计以 Whisper 转写文本和时间戳作为时间依据。如果没有词级时间戳，用分段时间戳配合静默检测，避免明显的剪辑或动效对齐错误。

## Workflow / 流程

### 1. Ask Transcription Method / 询问转写方式

Start by asking whether the user wants to use ElevenLabs for transcription.

首先询问用户是否使用 ElevenLabs 进行转写。

- If yes: tell the user that the video's audio will be uploaded to the external ElevenLabs service, ask for explicit upload consent, then ask the user to provide an ElevenLabs API key or confirm that `ELEVENLABS_API_KEY` is already available in the environment. Use ElevenLabs/Scribe word-level timestamps only after consent and key availability are confirmed.
- If no: use Whisper-compatible local transcription.

- 如果使用：告知用户视频音频会上传到外部 ElevenLabs 服务，明确询问是否同意上传，然后让用户提供 ElevenLabs API key，或确认环境变量 `ELEVENLABS_API_KEY` 已可用。只有同意上传且 key 可用后，才使用 ElevenLabs/Scribe 的词级时间戳。
- 如果不使用：使用 Whisper 兼容的本地转写方案。

Keep this choice for the full workflow, including editing analysis, SRT generation, packaging timing, and keyword animation timing.

这个选择贯穿完整流程，包括剪辑分析、SRT 生成、包装时间轴和关键词动画时间。

### 2. Ask Whether Editing Is Needed / 询问是否需要剪辑

Start by asking whether the user needs the raw footage edited.

先问用户是否需要对原始素材进行剪辑。

- If yes: ask for the source video folder and ask whether to use the normal edit plan or the fine-cut plan below. Then invoke `$video-use` to inspect, transcribe, propose an edit strategy, wait for confirmation, and produce the edited video or edit preview according to `video-use` rules.
- If no: ask the user to provide the already-edited video file.

- 如果需要：让用户提供需要剪辑的视频文件夹，并询问使用“默认剪辑方案”还是下面的“精剪方案”。然后调用 `$video-use` 检查素材、转写、提出剪辑策略、等待确认，并按 `video-use` 规则产出剪辑版本或预览。
- 如果不需要：让用户提供已经剪辑好的视频文件。

Do not proceed without a concrete video asset from one of these branches.

没有拿到明确的视频文件前，不进入后续步骤。

#### Fine-Cut Plan / 精剪方案

When the user selects fine cut, give `$video-use` this intent:

用户选择精剪时，把下面意图交给 `$video-use` 执行：

Goal: remove poorly delivered parts and keep the smoothest, most final-version-like expression.

目标：把讲得不好的地方去掉，保留表达最顺畅、最像最终版本的内容。

Preferences:

偏好：

1. For repeated takes, stumbles, trial runs, and restarts, delete earlier attempts by default and keep the last smooth version.
2. Remove obvious fillers and broken starts such as "嗯", "呃", "就是", half-sentences, restarted openings, and corrected mistakes when possible.
3. Do not only cut interruptions. Also inspect retained paragraphs for silence, breath gaps, waiting feel, and dead air.
4. If two sentences have obvious blank space, breath, or pause between them, shorten the gap.
5. Do not over-tighten. Preserve enough breathing room so the result does not feel mechanical.

1. 重复、卡壳、试讲、说到一半重来的地方，默认删掉前面几遍，保留最后一次讲顺的版本。
2. 明显的“嗯、呃、就是”、半句话、重新起头、说错重来，尽量删掉。
3. 不要只剪打断；保留下来的每个段落内部也要检查静默、气口、等待感。
4. 两句话之间如有明显空白、呼吸、停顿，就压短。
5. 不要剪得太紧，不要变成没有呼吸的机器感。

Fine-cut output requirements:

精剪输出要求：

1. Never overwrite the original video.
2. Keep `video-use` working artifacts and edit decisions in the normal `<videos_dir>/edit/` workspace.
3. Export or copy the fine-cut deliverable to the source video's directory as `<素材名称>_video-use精剪.<ext>`.
4. Preserve the edit decision file, such as `edl.json`, and any project notes generated by `$video-use`.
5. After rendering, run silence detection once to check for obvious mistakes, long blanks, or unexpected dead air. Fix clear issues before moving on.

1. 不覆盖原始视频。
2. `video-use` 工作文件和剪辑决策文件仍保留在标准 `<videos_dir>/edit/` 工作区。
3. 精剪成片导出或复制到素材同目录，命名为 `<素材名称>_video-use精剪.<ext>`。
4. 保留剪辑决策文件，例如 `edl.json`，以及 `$video-use` 生成的项目记录。
5. 渲染后跑一次静默检测，检查有没有明显错误、长空白或异常等待；发现明确问题先修正再进入后续步骤。

### 3. Ask About Custom Style / 询问是否自定义风格

After receiving the edited video, ask whether the user wants a custom visual style.

拿到剪辑好视频后，询问用户是否需要自定义视觉风格。

- If yes: ask the user to upload or point to a style Markdown file.
- If no: use the project/default `DESIGN.md`. If no usable `DESIGN.md` exists, create a default style brief before packaging design and either save it as `DESIGN.md` in the packaging workspace or include it at the top of the packaging plan.

- 如果需要：让用户上传或指定风格 Markdown 文件。
- 如果不需要：使用项目或默认的 `DESIGN.md`。如果没有可用的 `DESIGN.md`，先创建默认风格简报，再进入包装设计；可以保存为包装工作区里的 `DESIGN.md`，也可以写在包装方案开头。

Read the selected style file before designing packaging. Carry forward hard constraints such as safe zones, colors, typography, and forbidden transitions.

设计包装前必须先读取所选风格文件。要继承安全区、颜色、字体、禁用转场等硬约束。

Default style brief when `DESIGN.md` is missing:

缺少 `DESIGN.md` 时的默认风格简报：

- Layout: keep the middle lower subtitle area clear; place cards, keywords, terminals, and labels mainly on the left, right, or upper third; never cover the speaker's face or mouth.
- Visual language: compact tech HUD, cyber-blue/cyan accents, restrained glow, translucent dark cards, thin borders, visible depth layers, and distinctive bold display typography rather than plain system-text styling.
- Motion: small-scale card bounce, keyword pop, mouse click, drag/snap, checklist completion, collision rebound, and content-specific loading indicators; avoid transition flashes, scan wipes, and whole-video progress bars.
- Density: keep overlays smaller than the main subject, use short text, stagger elements, and remove or fade packaging as soon as the sentence beat is complete.

- 布局：预留下方中间字幕安全区；卡片、关键词、终端框和标签优先放左侧、右侧或上三分区；不得遮挡人物脸部和嘴部。
- 视觉语言：紧凑科技 HUD，蓝色/青色点缀，克制发光，半透明深色卡片，细描边，明确层级，使用有辨识度的粗体标题字，不使用普通系统字的平铺效果。
- 动效：小幅卡片弹跳、关键词弹出、鼠标点击、拖拽吸附、清单勾选、卡片碰撞回弹、内容内部加载状态；避免转场闪烁、扫描光效和整条视频进度条。
- 密度：包装元素要小于主体视觉权重，文字尽量短，元素错峰出现，当前句子节奏结束后及时退场或淡出。

### 4. Design The Packaging Plan Only / 只设计包装方案

Use `$video-use` to analyze the edited video and align visual packaging to the transcript/subtitle content. This step must not render, modify, or overwrite the source video.

使用 `$video-use` 分析剪辑后的视频，并把视觉包装与字幕/文案内容对齐。此步骤不得渲染、不得修改、不得覆盖原视频。

Before drafting the packaging plan, read `references/keyword-animation-effects.md` and choose or combine suitable keyword, card, mouse, collision, drag, snapping, checklist, and loading effects according to subtitle meaning.

设计包装方案前，先读取 `references/keyword-animation-effects.md`，并根据字幕语义选择或组合合适的关键词、卡片、鼠标、碰撞、拖拽、吸附、勾选、加载等动效。

The design draft must include, for every animation segment:

每一段动效设计稿都必须包含：

- Time node: start and end time.
- On-screen text: exact visible words, keywords, cards, labels, or captions.
- Animation effect: entrance, hold/idle, emphasis, exit or handoff.
- Motion details: direction, speed feel, easing, stagger, highlight timing.
- Layout: safe zone, face/mouth avoidance, subtitle-safe area, layer order.
- Visual style: colors, font/weight, card style, glow, borders, hierarchy.

- 时间节点：开始和结束时间。
- 画面文字：屏幕上实际出现的文字、关键词、卡片、标签或字幕。
- 动画效果：入场、待机、重点强调、退场或交接。
- 运动细节：方向、速度感、缓动、错峰、重点高亮时间。
- 布局：安全区、不挡脸不挡嘴、字幕安全区、层级顺序。
- 视觉风格：颜色、字体/字重、卡片样式、发光、边框、层级。

Present the draft to the user and wait for approval. Do not implement Remotion before approval.

把设计稿发给用户确认。用户未确认前，不进入 Remotion 实现。

### 5. Implement Remotion + GSAP Preview / 实现 Remotion + GSAP 预览

After approval, create or update a Remotion project for the video packaging.

用户确认后，创建或更新 Remotion 项目来实现视频包装。

Implementation requirements:

实现要求：

- Use Remotion for composition structure, video placement, timeline, Studio, and export.
- Use GSAP for animation easing/timeline calculations or element motion logic.
- Use the approved packaging plan as the source of truth.
- Keep video and audio aligned to the edited video.
- Keep overlays outside face/mouth and subtitle-safe zones unless the approved plan explicitly says otherwise.
- Do not add transition flashes or scan wipes if the selected style forbids them.
- Do not add global top/bottom video progress bars. Progress bars are allowed only inside task cards or loading panels when they represent content-specific progress.
- Do not render or export at this stage.

- 用 Remotion 负责合成结构、视频放置、时间线、Studio 和导出。
- 用 GSAP 负责动画缓动、时间线计算或元素运动逻辑。
- 以用户确认的包装方案为唯一实现依据。
- 保持视频与音频和剪辑后视频对齐。
- 除非方案明确允许，否则包装元素必须避开脸部、嘴部和字幕安全区。
- 如果风格文件禁止转场闪烁或扫描光效，就不得添加。
- 不要添加顶部或底部的整条视频全局进度条。只有表示内容内部状态的任务卡片进度条或加载面板进度条可以使用。
- 此阶段不渲染、不导出。

If dependencies are missing, install only what is needed for this stack, such as `remotion`, `@remotion/cli`, `react`, `react-dom`, `typescript`, and `gsap`. Do not replace Remotion + GSAP with HyperFrames, plain HTML, PIL, Manim, or another animation system unless the user explicitly changes the requirement.

如果依赖缺失，只安装这个技术栈必需的依赖，例如 `remotion`、`@remotion/cli`、`react`、`react-dom`、`typescript`、`gsap`。不要把 Remotion + GSAP 替换成 HyperFrames、普通 HTML、PIL、Manim 或其它动画系统，除非用户明确改需求。

Run a TypeScript or build check when available. Then open Remotion Studio and give the user the local Studio URL.

如果项目支持，运行 TypeScript 或构建检查。然后打开 Remotion Studio，并把本地 Studio 地址给用户。

### 6. Export Only After Final Approval / 最终确认后才导出

Wait for the user to review Studio and explicitly confirm export.

等待用户在 Studio 中检查并明确确认导出。

Only then render/export the final video. Preserve the edited input video and style/design artifacts. If subtitles are requested, keep SRT as a separate file unless the user explicitly asks to burn subtitles into the video.

只有在用户确认后才渲染/导出最终视频。保留剪辑后输入视频和风格/设计稿文件。如果用户需要字幕，默认输出独立 SRT；除非用户明确要求，否则不烧录字幕。

## Output Naming / 输出命名

Prefer explicit artifacts:

推荐产出命名清晰的文件：

- `packaging-plan-<video-name>.md` for the approved motion design.
- `subtitles/<video-name>.srt` for optional SRT.
- `remotion-packaging/` or `<video-name>-remotion-packaging/` for the Remotion project.
- `final/` or `exports/` only after the final export confirmation.

## Confirmation Gates / 确认节点

Never skip these gates:

不要跳过这些确认点：

1. Confirm whether to use ElevenLabs or Whisper for transcription.
2. Confirm whether editing is needed.
3. If editing is needed, confirm the edit strategy before touching cuts.
4. Confirm whether custom style is needed.
5. Confirm the packaging motion design before Remotion implementation.
6. Confirm Studio preview before final export.

## Common Mistakes / 常见错误

- Do not generate only one scene when the transcript has multiple sentences; every meaningful sentence or beat needs packaging consideration.
- Do not let cards, terminals, titles, or transition effects cover the speaker's face or mouth.
- Do not render during the planning or Studio-preview stage.
- Do not burn subtitles by default.
- Do not generate whole-video playback progress bars as decorative HUD.
- Do not silently switch from Remotion + GSAP to another tool when setup is inconvenient.
- Do not overwrite the original or edited source video.

- 不要在文案有多句话时只生成一个场景；每个有意义的句子或节奏点都要考虑包装。
- 不要让卡片、终端框、标题或转场效果挡住人物脸部和嘴部。
- 不要在方案阶段或 Studio 预览阶段渲染。
- 默认不要烧录字幕。
- 不要把整条视频播放进度条当作装饰性 HUD 生成。
- 不要因为环境麻烦就偷偷换掉 Remotion + GSAP。
- 不要覆盖原始视频或剪辑后源视频。

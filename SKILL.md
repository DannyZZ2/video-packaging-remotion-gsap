---
name: video-auto-edit
description: Use when the user wants a reusable workflow for video editing decisions, subtitle-aligned visual packaging, keyword card animations, Remotion Studio preview, and final export using Remotion plus GSAP.
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

## Packaging Timing Bundle / 包装时间包

Before any packaging design, normalize the final edited video, subtitles, transcript, timestamps, and edit decisions into one packaging timing bundle. This rule applies whether the video came from a `$video-use` fine cut or from an already-edited video provided by the user.

进入任何包装设计前，必须把最终剪辑视频、字幕、转写文本、时间戳和剪辑决策统一整理成一个包装时间包。无论视频来自 `$video-use` 精剪，还是用户直接提供的剪辑成片，都适用这条规则。

The bundle must include:

时间包必须包含：

- `edited_video_path`: the final edited video used for packaging.
- `srt_path`: independent SRT path. Generate one by default and do not burn subtitles into the video.
- `transcript_text` or `transcript_path`: transcript text matched to the final edited video.
- `transcription_provider`: `elevenlabs` or `whisper`.
- `word_timestamps_path`: required when ElevenLabs is selected, unless the user provided a verified equivalent word-level timestamp file.
- `segment_timestamps_path`: required when Whisper is selected. If the local Whisper tool can produce word-level timestamps, include that file too, but still mark the provider as `whisper`.
- `keyword_cues_path`: required for packaging. It must list each selected subtitle keyword, its subtitle line, trigger time, timing source, and confidence.
- `edl_path`: edit decision file when available. If the user provided an already-edited video and no EDL exists, set this to `none`.

- `edited_video_path`：用于包装的最终剪辑视频。
- `srt_path`：独立 SRT 路径。默认生成，不烧录进视频。
- `transcript_text` 或 `transcript_path`：与最终剪辑视频匹配的转写文本。
- `transcription_provider`：`elevenlabs` 或 `whisper`。
- `word_timestamps_path`：选择 ElevenLabs 时必须提供，除非用户已经提供可验证的等效词级时间戳文件。
- `segment_timestamps_path`：选择 Whisper 时必须提供。如果本地 Whisper 工具能产出词级时间戳，也一并保留，但 provider 仍标记为 `whisper`。
- `keyword_cues_path`：包装阶段必须提供。它要列出每个选中的字幕关键词、所属字幕行、触发时间、时间来源和置信度。
- `edl_path`：有剪辑决策文件时填写；如果用户提供的是已经剪辑好的视频且没有 EDL，写 `none`。

If `$video-use` produced a fine cut, reuse cached transcript/SRT/timestamp artifacts only when they match the final edited video. If they were generated from the raw source or from an earlier preview, regenerate or normalize timing artifacts from the final edited video before packaging design.

如果 `$video-use` 产出了精剪成片，只有在缓存的转写、SRT、时间戳产物与最终剪辑视频一致时才复用。若这些产物来自原始素材或早期预览版本，进入包装设计前必须基于最终剪辑视频重新生成或归一化时间产物。

If the user provides an already-edited video, still run the selected transcription path before packaging design unless the user provides both a usable SRT and verified word-level timestamps. A user-provided SRT can be used as subtitle text and base timing, but it is not a replacement for word-level keyword timing. When ElevenLabs is selected and upload consent is granted, run ElevenLabs on the edited video to get word-level timestamps for keyword animation. If the user refuses external upload, use Whisper and mark the bundle as segment-level timing unless local word-level timing is available.

如果用户提供的是已经剪辑好的视频，进入包装设计前仍要按已选择的转写方案生成时间产物，除非用户同时提供可用 SRT 和已验证的词级时间戳。用户上传的 SRT 可以作为字幕文本和基础时间，但不能替代关键词动画所需的词级时间。若用户选择 ElevenLabs 且同意上传音频，应对剪辑成片运行 ElevenLabs，拿到关键词动效用的词级时间戳。若用户拒绝外部上传，则使用 Whisper，并在时间包中标记为分段级时间；本地工具能产出词级时间时再补充词级文件。

Save the normalized bundle as `timing/<video-name>-packaging-timing.json` or `packaging-timing-<video-name>.json`, then pass this bundle to `$video-use`. `$video-use` must design packaging from the bundle instead of ad hoc transcript, SRT, or EDL paths.

将归一化后的时间包保存为 `timing/<video-name>-packaging-timing.json` 或 `packaging-timing-<video-name>.json`，然后交给 `$video-use`。`$video-use` 必须基于这个时间包做包装方案，不要临时拼接零散的转写、SRT 或 EDL 路径。

All packaging animations must be triggered by subtitle keyword cue points. Prefer exact word-level keyword start times from `word_timestamps_path`. If only segment-level Whisper timing is available, estimate the keyword trigger inside the matching subtitle segment by text position and nearby silence/speech energy, then mark the cue confidence as estimated. Do not trigger effects only from evenly divided scene times or generic segment start times unless the segment has no usable keyword; in that case write `triggerKeyword: none` and explain why.

所有包装动效都必须按字幕关键词落点触发。优先使用 `word_timestamps_path` 中精确的词级关键词开始时间。如果只有 Whisper 分段时间，则根据关键词在字幕文本中的位置和附近静默/语音能量，在对应字幕段内估算触发点，并把 cue 置信度标记为 estimated。不要只按均分场景时间或泛泛的段落开始时间触发动效；除非该段没有可用关键词，此时必须写 `triggerKeyword: none` 并说明原因。

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

#### Post-Edit Handoff / 精剪后交接

After a fine-cut render passes verification, do not stop at "fine cut complete." Reply with the edited file path, the key verification results, and the next workflow action.

精剪渲染并通过检查后，不要停在“精剪完成”。必须回复剪辑后文件路径、关键检查结果，以及下一步流程动作。

Use this handoff shape:

使用下面这种交接格式：

```text
精剪完成，未覆盖原片。
输出文件：<edited-video-path>
检查结果：<duration / resolution / audio / silence check summary>

下一步进入视觉包装流程：
1. 我会基于这条精剪视频生成独立 SRT，默认不烧录字幕。
2. 接下来需要确认包装风格：使用自定义风格 Markdown，还是使用默认 DESIGN.md / 默认风格？
3. 风格确认后，我会用 video-use 生成包装动效设计稿，只出方案，不渲染。

请确认：继续做视觉包装吗？如果继续，请选择“自定义风格”或“默认风格”。
```

If the user already selected a style earlier, skip the style question and ask for approval to generate the packaging motion design draft from the edited video, transcript/SRT, EDL, selected style, and keyword-animation reference.

如果用户前面已经确认过风格，不要重复询问风格；直接询问是否基于剪辑后视频、转写/SRT、EDL、已选风格和关键词动效参考生成包装动效设计稿。

Before asking for packaging approval, create or update the packaging timing bundle from the final fine-cut video. The next-step handoff should refer to this bundle rather than separate transcript, SRT, and EDL files.

询问是否进入包装方案前，先基于最终精剪视频创建或更新时间包。下一步交接时应引用这个时间包，而不是零散引用转写、SRT 和 EDL 文件。

### 3. Ask About Custom Style / 询问是否自定义风格

After receiving the edited video, ask whether the user wants a custom visual style.

拿到剪辑好视频后，询问用户是否需要自定义视觉风格。

- If yes: ask the user to upload or point to either a style Markdown file or one or more reference images.
- If no: use the project/default `DESIGN.md`. If no usable `DESIGN.md` exists, read `references/default-design.md` from this skill and use `Remotion Native Material Cards` as the default visual style brief.

- 如果需要：让用户上传或指定风格 Markdown 文件，或上传一张/多张参考图片。
- 如果不需要：使用项目或默认的 `DESIGN.md`。如果没有可用的 `DESIGN.md`，读取本 skill 的 `references/default-design.md`，并使用 `Remotion Native Material Cards / Remotion 原生材质卡片` 作为默认视觉风格简报。

Read the selected style source before designing packaging. Carry forward hard constraints such as safe zones, colors, typography, and forbidden transitions.

设计包装前必须先读取所选风格来源。要继承安全区、颜色、字体、禁用转场等硬约束。

If the user provides reference image(s), inspect the image(s) first and extract a concise style brief before packaging design. The extracted brief must cover: layout pattern, card shape and border style, typography feel, color palette, glow/shadow treatment, icon elements, density, safe-zone implications, motion assumptions, and what not to copy if it would block faces, mouths, or subtitles.

如果用户提供参考图片，必须先观察图片并提取精简风格 brief，再进入包装设计。提取出的 brief 必须包含：布局模式、卡片形状和描边样式、字体气质、配色、发光/阴影处理、图标元素、信息密度、安全区影响、动效假设，以及哪些元素如果会挡脸、挡嘴或挡字幕就不要照搬。

After reading a style Markdown or extracting a brief from reference image(s), pass the hard constraints and visual direction into `$video-use` as part of the packaging-design brief. If no stronger custom style is provided, pass `references/card-style-library.md` as the unified default card-polish and material-quality reference. The packaging plan must be designed by `$video-use` from the edited video, transcript text, EDL, SRT/timestamps, selected style Markdown or extracted image-style brief, card style library, and keyword-animation reference together. The main agent must not bypass `$video-use` and draft the packaging plan by itself.

读取风格 Markdown 或从参考图片提取 brief 后，必须把其中的硬约束和视觉方向作为包装设计 brief 交给 `$video-use`。如果用户没有提供更强的自定义风格，把 `references/card-style-library.md` 作为统一的默认卡片质感和材质质量参考。包装方案必须由 `$video-use` 基于剪辑后视频、转写文本、EDL、SRT/时间戳、所选风格 Markdown 或图片风格提取 brief、卡片风格库，以及关键词动效参考一起设计。主 Agent 不得绕过 `$video-use` 自行产出包装方案。

Default style brief when `DESIGN.md` is missing: read `references/default-design.md` completely before packaging design.

缺少 `DESIGN.md` 时的默认风格简报：进入包装设计前，完整读取 `references/default-design.md`。

### 4. Design The Packaging Plan Only / 只设计包装方案

Use `$video-use` to analyze the edited video and align visual packaging to the transcript/subtitle content. This step must not render, modify, or overwrite the source video.

使用 `$video-use` 分析剪辑后的视频，并把视觉包装与字幕/文案内容对齐。此步骤不得渲染、不得修改、不得覆盖原视频。

The main agent's role in this step is to prepare inputs for `$video-use`, including the packaging timing bundle, selected style Markdown or extracted image-style brief, `references/visual-quality-system.md`, and the keyword-animation reference. Verify that the bundle exists and points to the final edited video before asking `$video-use` for a plan. `$video-use` must return the packaging motion design draft. The main agent may summarize or relay that draft to the user, but must not replace it with a self-authored packaging plan.

此步骤中，主 Agent 的职责是为 `$video-use` 准备输入，包括包装时间包、已选择的风格 Markdown 或图片风格提取 brief、`references/visual-quality-system.md`，以及关键词动效参考。请求 `$video-use` 出方案前，必须确认时间包存在，并且指向最终剪辑视频。包装动效设计稿必须由 `$video-use` 返回。主 Agent 可以整理或转述该设计稿给用户，但不得用自己另写的包装方案替代它。

Before drafting the packaging plan, read `references/visual-quality-system.md`, `references/card-style-library.md`, and `references/keyword-animation-effects.md`. The default built-in style is `Remotion Native Material Cards`. Optional built-in styles include `Holographic Glass HUD`, `Frosted Glass Packaging`, and `Reference HUD Pattern`; use them only when the user explicitly requests one, provides a matching reference, or the approved design draft names that style. A user-provided Markdown style or reference image may still be used as an explicit external custom style for that task.

设计包装方案前，先读取 `references/visual-quality-system.md`、`references/card-style-library.md` 和 `references/keyword-animation-effects.md`。默认内置风格是 `Remotion Native Material Cards / Remotion 原生材质卡片`。可选内置风格包括 `Holographic Glass HUD / 全息玻璃 HUD`、`Frosted Glass Packaging / 毛玻璃包装`、`Reference HUD Pattern / 参考 HUD 信息图`；只有在用户明确要求、提供匹配参考，或已确认设计稿指定时才使用。用户提供的风格 Markdown 或参考图片仍可作为该任务的外部自定义风格使用。

The design draft must include, for every animation segment:

每一段动效设计稿都必须包含：

- Time node: start and end time.
- Trigger keyword cue: exact subtitle keyword, cue time, timing source, and confidence.
- On-screen text: exact visible words, keywords, cards, labels, or captions.
- Animation effect: entrance, hold/idle, emphasis, exit or handoff.
- Motion details: direction, speed feel, easing, stagger, highlight timing.
- Layout: safe zone, face/mouth avoidance, subtitle-safe area, layer order.
- Visual style: colors, font/weight, card style, glow, borders, hierarchy.
- Card material: glass layer, dark gradient, semantic border, inner/outer shadow, icon container, and highlight treatment.
- Applied style constraints: cite the specific constraints from the selected style Markdown or extracted image-style brief that shaped this segment.
- Typography: exact `fontFamily`, weight, size range, line height, and fallback.
- Component: one component from `references/visual-quality-system.md`, such as `KeywordChip`, `TerminalCard`, or `StepListCard`.
- Quality risk: state whether this segment may block face, mouth, subtitles, become too dense, or suffer font fallback.

- 时间节点：开始和结束时间。
- 触发关键词落点：准确的字幕关键词、cue 时间、时间来源和置信度。
- 画面文字：屏幕上实际出现的文字、关键词、卡片、标签或字幕。
- 动画效果：入场、待机、重点强调、退场或交接。
- 运动细节：方向、速度感、缓动、错峰、重点高亮时间。
- 布局：安全区、不挡脸不挡嘴、字幕安全区、层级顺序。
- 视觉风格：颜色、字体/字重、卡片样式、发光、边框、层级。
- 卡片材质：玻璃层、暗色渐变、语义描边、内外阴影、图标容器和高光处理。
- 已应用的风格约束：说明该段具体采用了所选风格 Markdown 或图片风格提取 brief 中的哪些约束。
- 字体：具体 `fontFamily`、字重、字号区间、行高和 fallback。
- 组件：从 `references/visual-quality-system.md` 选择一个组件，例如 `KeywordChip`、`TerminalCard` 或 `StepListCard`。
- 质量风险：说明该段是否可能挡脸、挡嘴、挡字幕、信息过密或字体 fallback。

The packaging motion design draft must use this exact Markdown block format. Do not replace it with a table, a loose paragraph, or a general style summary:

包装动效设计稿必须使用下面这种 Markdown 分段格式。不得用表格、散文段落或泛泛的风格总结替代：

```text
包装动效方案

0.00-1.55s
触发关键词：GPT 5.6 @ 0.34s，source=word_timestamps，confidence=exact
画面文字：GPT 5.6、发布
动效：左上角模式标题卡从左滑入，GPT 5.6 关键词轻微 pop，发布 标签短促点亮后收起。
运动：ease-out，关键元素错峰进入；如无独立运动细节，写“同动效描述”。
布局：左上三分区，不碰脸部；层级在人物前但低视觉权重。
卡片材质：黑色半透明玻璃底 + 深色渐变 + 蓝色语义描边 + 弱外发光 + 内阴影 + 左侧图标容器。
已应用的风格约束：引用所选风格 Markdown 或图片风格提取 brief 中实际采用的颜色、字体、卡片、安全区或禁用项。
字体：Source Han Sans Heavy / Inter Black，56-72px，line-height 0.96，fallback PingFang SC Heavy。
组件：PremiumKeywordPanel + KeywordChip。
质量风险：注意不要变成整句大字；标题卡不得压到脸部或字幕区。

1.55-3.20s
触发关键词：推理模式 @ 1.82s，source=word_timestamps，confidence=exact
画面文字：2 种推理模式
动效：两枚模式芯片从标题卡下方 stagger 弹出，先显示空芯片，再填入 Max / Ultra 轮廓。
运动：ease-out，芯片间 4 帧错峰，边框青色脉冲一次。
布局：左侧中上，保留下方字幕区。
卡片材质：每枚芯片使用半哑光深色底、1px 语义色渐变描边、内侧暗角和图标弱发光，禁止纯色扁平矩形。
已应用的风格约束：引用所选风格 Markdown 或图片风格提取 brief 中实际采用的约束。
字体：Inter ExtraBold / PingFang SC Semibold，28-36px，line-height 1.18。
组件：RemotionModularCard / KeywordChip。
质量风险：两枚芯片不要同时遮挡人物手势；边框发光不能过曝。
```

Each segment must start with a precise `start-end` time range and must include `触发关键词`、`画面文字`、`动效`、`运动`、`布局`、`卡片材质`、`已应用的风格约束`、`字体`、`组件`、`质量风险`. If a segment has no visible overlay, write `画面文字：无` and explain why no overlay should appear. The visible text must be short and concrete; do not use full transcript sentences as on-screen overlay text unless the user explicitly requests it.

每个段落必须以精确的 `开始-结束` 时间范围开头，并且必须包含 `触发关键词`、`画面文字`、`动效`、`运动`、`布局`、`卡片材质`、`已应用的风格约束`、`字体`、`组件`、`质量风险`。如果某段不应出现包装元素，写 `画面文字：无` 并说明不出现动效的原因。画面文字必须短而具体；除非用户明确要求，不要把完整口播句子直接作为包装大字。

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
- Drive overlay entrances, highlights, bounces, clicks, card collisions, and exits from the approved subtitle keyword cue times. Convert cue seconds to Remotion frames and use those frames as animation anchors.
- Use `references/visual-quality-system.md` as the implementation quality bar for typography, components, hierarchy, spacing, and motion polish.
- Use `references/card-style-library.md` as the unified implementation reference for the selected built-in or custom style. Default to `Remotion Native Material Cards`; use optional built-in styles only when explicitly selected in the approved plan.
- Keep video and audio aligned to the edited video.
- Keep overlays outside face/mouth and subtitle-safe zones unless the approved plan explicitly says otherwise.
- Explicitly set `fontFamily`, weight, size, line height, and fallback for every text component; do not rely on browser default fonts.
- Do not add transition flashes, scan wipes, full-screen frames, or dashboard shells if the selected style forbids them.
- Do not add global top/bottom video progress bars. Progress bars are allowed only inside task cards or loading panels when they represent content-specific progress.
- Do not render or export at this stage.

- 用 Remotion 负责合成结构、视频放置、时间线、Studio 和导出。
- 用 GSAP 负责动画缓动、时间线计算或元素运动逻辑。
- 以用户确认的包装方案为唯一实现依据。
- 按已确认方案中的字幕关键词落点驱动包装元素入场、高亮、弹跳、点击、卡片碰撞和退场。将 cue 秒数转换成 Remotion 帧，并以这些帧作为动画锚点。
- 使用 `references/visual-quality-system.md` 作为字体、组件、层级、间距和动效质感的实现质量标准。
- 使用 `references/card-style-library.md` 作为已选内置或自定义风格的统一实现参考。默认使用 `Remotion Native Material Cards / Remotion 原生材质卡片`；只有在确认方案明确选择时才使用可选内置风格。
- 保持视频与音频和剪辑后视频对齐。
- 除非方案明确允许，否则包装元素必须避开脸部、嘴部和字幕安全区。
- 每个文字组件都必须显式设置 `fontFamily`、字重、字号、行高和 fallback；不要依赖浏览器默认字体。
- 如果风格文件禁止转场闪烁、扫描光效、全屏框或仪表盘外壳，就不得添加。
- 不要添加顶部或底部的整条视频全局进度条。只有表示内容内部状态的任务卡片进度条或加载面板进度条可以使用。
- 此阶段不渲染、不导出。

If dependencies are missing, install only what is needed for this stack, such as `remotion`, `@remotion/cli`, `react`, `react-dom`, `typescript`, and `gsap`. Do not replace Remotion + GSAP with HyperFrames, plain HTML, PIL, Manim, or another animation system unless the user explicitly changes the requirement.

如果依赖缺失，只安装这个技术栈必需的依赖，例如 `remotion`、`@remotion/cli`、`react`、`react-dom`、`typescript`、`gsap`。不要把 Remotion + GSAP 替换成 HyperFrames、普通 HTML、PIL、Manim 或其它动画系统，除非用户明确改需求。

Run a TypeScript or build check when available.

如果项目支持，运行 TypeScript 或构建检查。

#### Silent Remotion Studio QA / Remotion Studio 静默自检

Before opening Remotion Studio, run this silent QA even when the user says "do not render" or "open Studio only." This QA renders still frames only; it must not export the full video.

打开 Remotion Studio 前，即使用户说“不渲染”或“只打开 Studio”，也必须先做下面的静默自检。此自检只渲染静帧，不得导出完整视频。

Required checks:

必须检查：

1. Run the available project checks, such as `npm run typecheck`, `npm run build`, or `npm run compositions`.
2. Capture still frames with `remotion still` at a minimum of 3 key moments: one early overlay moment, one middle keyword/card moment, and one late/risk or payoff moment. Prefer 5 frames when the composition has many timed keyword cues.
3. Inspect the stills before showing Studio. Confirm that the frame is not blank, the base video is visible, the packaging layer is above the video, and at least one expected overlay is visible at each sampled cue time.
4. Verify subtitle keyword timing: an overlay tied to a keyword must be absent before its cue and visible at or just after its cue. If exact pre/post frame checks are too expensive, check at least the cue frame and one nearby later frame.
5. Check typography, face/mouth safety, subtitle safety, card density, glow intensity, material layering, semantic border color, and global-progress-bar violations.
6. If any sampled frame is blank, missing overlays, hidden behind video, off-canvas, blocking the face/mouth, or showing the wrong keyword timing, fix the issue and rerun the relevant still checks before opening Studio.
7. Only after silent QA passes, open Remotion Studio and give the user the local Studio URL. Summarize only pass/fail and key fixes; do not require the user to manually discover basic visibility bugs.

1. 运行项目可用检查，例如 `npm run typecheck`、`npm run build` 或 `npm run compositions`。
2. 用 `remotion still` 抽取至少 3 个关键静帧：一个早期包装出现点、一个中段关键词/卡片点、一个后段风险或 payoff 点。若合成里有很多关键词 cue，优先抽 5 帧。
3. 打开 Studio 前先检查这些静帧。确认画面不是空白、底层视频可见、包装层在视频上方，并且每个抽样 cue 时间点至少有一个预期包装元素可见。
4. 检查字幕关键词时间：绑定关键词的包装元素在 cue 前应不可见，在 cue 当帧或稍后应可见。如果逐个前后帧成本太高，至少检查 cue 帧和一个稍后的邻近帧。
5. 检查字体、脸部/嘴部安全、字幕安全、卡片密度、发光强度、材质层次、语义描边颜色和全局进度条违规。
6. 如果任何抽样帧空白、包装缺失、被视频盖住、跑到画面外、挡脸/挡嘴，或关键词时间错误，先修复并重新跑相关静帧检查，再打开 Studio。
7. 静默自检通过后，才打开 Remotion Studio 并给用户本地 Studio URL。只向用户总结通过/失败和关键修复点；不要让用户手动发现基础可见性问题。

### 6. Export Only After Final Approval / 最终确认后才导出

Wait for the user to review Studio and explicitly confirm export.

等待用户在 Studio 中检查并明确确认导出。

Only then render/export the final video. Preserve the edited input video and style/design artifacts. If subtitles are requested, keep SRT as a separate file unless the user explicitly asks to burn subtitles into the video.

只有在用户确认后才渲染/导出最终视频。保留剪辑后输入视频和风格/设计稿文件。如果用户需要字幕，默认输出独立 SRT；除非用户明确要求，否则不烧录字幕。

## Output Naming / 输出命名

Prefer explicit artifacts:

推荐产出命名清晰的文件：

- `packaging-plan-<video-name>.md` for the approved motion design.
- `timing/<video-name>-packaging-timing.json` or `packaging-timing-<video-name>.json` for the normalized packaging timing bundle.
- `timing/<video-name>-keyword-cues.json` for subtitle keyword cue points used by packaging animations.
- `references/card-style-library.md` as the unified active card style library.
- `subtitles/<video-name>.srt` for optional SRT.
- `remotion-packaging/` or `<video-name>-remotion-packaging/` for the Remotion project.
- `final/` or `exports/` only after the final export confirmation.

## Confirmation Gates / 确认节点

Never skip these gates:

不要跳过这些确认点：

1. Confirm whether to use ElevenLabs or Whisper for transcription.
2. Confirm whether editing is needed.
3. If editing is needed, confirm the edit strategy before touching cuts.
4. Confirm whether custom style is needed, and if yes whether the source is a style Markdown file or reference image(s).
5. Confirm the packaging motion design before Remotion implementation.
6. Confirm Studio preview before final export.

## Common Mistakes / 常见错误

- Do not generate only one scene when the transcript has multiple sentences; every meaningful sentence or beat needs packaging consideration.
- Do not let cards, terminals, titles, or transition effects cover the speaker's face or mouth.
- Do not render during the planning or Studio-preview stage.
- Do not burn subtitles by default.
- Do not generate whole-video playback progress bars as decorative elements.
- Do not stop after fine-cut verification without telling the user the next packaging action.
- Do not skip the packaging timing bundle after fine-cut or user-provided edited-video handoff.
- Do not treat user-provided SRT as word-level keyword timing unless it is accompanied by verified word-level timestamps.
- Do not trigger every animation from generic scene starts, equal time slices, or hand-picked decorative timings; use subtitle keyword cue points.
- Do not generate flat single-layer cards. Cards need glass material, gradient, semantic border, inner/outer shadows, icon container, and explicit typography hierarchy.
- Do not use unselected optional styles. Default to `Remotion Native Material Cards`; use `Holographic Glass HUD`, `Frosted Glass Packaging`, or `Reference HUD Pattern` only when explicitly selected.
- Do not ignore reference image(s) when the user provides them as the custom style source; extract a style brief first.
- Do not silently switch from Remotion + GSAP to another tool when setup is inconvenient.
- Do not overwrite the original or edited source video.

- 不要在文案有多句话时只生成一个场景；每个有意义的句子或节奏点都要考虑包装。
- 不要让卡片、终端框、标题或转场效果挡住人物脸部和嘴部。
- 不要在方案阶段或 Studio 预览阶段渲染。
- 默认不要烧录字幕。
- 不要把整条视频播放进度条当作装饰元素生成。
- 不要在精剪检查完成后停住而不告诉用户下一步包装动作。
- 精剪或用户提供成片交接后，不要跳过包装时间包。
- 不要把用户提供的 SRT 当作词级关键词时间，除非同时有已验证的词级时间戳。
- 不要用泛泛的场景开始时间、均分时间片或手选装饰时间触发所有动效；必须使用字幕关键词落点。
- 不要生成单层扁平卡片。卡片需要玻璃材质、渐变、语义描边、内外阴影、图标容器和明确字体层级。
- 不要混用未被选择的可选内置风格。默认使用 `Remotion Native Material Cards / Remotion 原生材质卡片`；只有明确选择时才使用 `Holographic Glass HUD / 全息玻璃 HUD`、`Frosted Glass Packaging / 毛玻璃包装` 或 `Reference HUD Pattern / 参考 HUD 信息图`。
- 用户提供参考图片作为自定义风格来源时，不要忽略图片；必须先提取风格 brief。
- 不要因为环境麻烦就偷偷换掉 Remotion + GSAP。
- 不要覆盖原始视频或剪辑后源视频。

# video-auto-edit

`video-auto-edit` is a Codex skill for a confirmation-gated video workflow: optional fine-cut editing, SRT/timing generation, subtitle-keyword-driven visual packaging, Remotion + GSAP animation preview, and final export after user approval.

`video-auto-edit` 是一个 Codex skill，用于把视频流程固定成：可选精剪、生成 SRT/时间包、按语音/字幕关键词触发视觉包装、用 Remotion + GSAP 生成动效预览，并在用户确认后再导出。

## Install / 安装

Install with the Codex skill installer:

使用 Codex skill 安装器：

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py --repo DannyZZ2/video-auto-edit --path . --name video-auto-edit
```

Manual install is also possible:

也可以手动安装：

```bash
git clone https://github.com/DannyZZ2/video-auto-edit.git ~/.codex/skills/video-auto-edit
```

## Requirements / 依赖

- Codex with skill support.
- `ffmpeg` and `ffprobe` for video/audio inspection.
- `$video-use` skill. This skill is configured to bootstrap `$video-use` automatically when missing.
- Local Whisper-compatible transcription by default, or ElevenLabs/Scribe only after explicit user choice, upload consent, and API-key availability.
- Remotion + GSAP for approved animation implementation and Studio preview.

- 支持 skill 的 Codex。
- 用于视频/音频检查的 `ffmpeg` 和 `ffprobe`。
- `$video-use` skill。本 skill 已配置为缺失时自动安装 `$video-use`。
- 默认使用本地 Whisper 兼容转写；只有用户明确选择 ElevenLabs、同意上传音频并提供 API key 后，才使用 ElevenLabs/Scribe。
- 用户确认动效方案后，使用 Remotion + GSAP 实现动画并打开 Studio 预览。

## Workflow / 工作流

1. Ask whether to use ElevenLabs or local Whisper transcription.
2. Ask whether the user needs editing.
3. If editing is needed, ask for the source video folder and choose normal edit or fine-cut.
4. If editing is not needed, ask for the already-edited video.
5. Generate or normalize SRT, transcript, timestamp, edit-decision, keyword-cue, current-project asset, and gesture-cue data into one packaging timing bundle.
6. Ask whether the user wants a custom style Markdown or reference image. Use current Codex project/workspace assets by default; ask only whether the user wants to add extra asset paths or disable asset matching.
7. Match current-project asset filenames, path segment names, and filename-token aliases to subtitle keywords. Do not inspect image contents, OCR visible text, or classify the image subject for matching. When a subtitle keyword matches an asset name, show that asset at the keyword cue unless safety or style constraints prevent it. Do not scan the uploaded video's source folder by default.
8. If a reference image file name contains `github`, or the explicitly provided reference image contains a GitHub repository pattern, parse it into a `GitHubRepoCard`. For ordinary content assets, create GitHub cards only from filename tokens or user-provided metadata, not from image-content understanding.
9. If pointing, dragging, swiping, circling, or line-drawing gestures are visible near a cue, use the gesture position as the preferred image/animation anchor.
10. If no custom style is provided, use the default `Remotion Native Material Cards` style. Optional built-in styles are available when explicitly selected: `Holographic Glass HUD`, `Frosted Glass Packaging`, and `Reference HUD Pattern`.
11. Use `$video-use` to generate a packaging motion design draft from the final video, timing bundle, selected style, asset manifest, gesture cues, card style library, and keyword animation library.
12. Wait for user confirmation.
13. Implement the approved design with Remotion + GSAP.
14. Open Remotion Studio for preview only.
15. Export only after the user confirms the Studio preview.

1. 先问用户是否使用 ElevenLabs，还是使用本地 Whisper 转写。
2. 再问用户是否需要剪辑。
3. 如果需要剪辑，让用户提供源视频文件夹，并选择默认剪辑或精剪。
4. 如果不需要剪辑，让用户提供已经剪辑好的视频。
5. 将 SRT、转写文本、时间戳、剪辑决策、关键词 cue、当前项目素材和手势 cue 统一整理为包装时间包。
6. 询问是否使用自定义风格 Markdown 或参考图片。默认使用当前 Codex 项目/工作区素材；只询问用户是否要额外补充素材路径，或是否关闭素材匹配。
7. 用当前项目素材文件名、路径片段名称和文件名分词别名匹配字幕关键词。不要理解图片内容，不要 OCR 可见文字，不要根据图片主体分类做匹配。字幕关键词匹配到素材名称时，在该关键词 cue 展示素材，除非安全区或风格约束不允许。默认不要扫描用户上传视频所在的源文件夹。
8. 如果参考图文件名包含 `github`，或用户明确提供的参考图中有 GitHub 仓库结构，解析为 `GitHubRepoCard`。普通内容素材只能从文件名分词或用户提供的元数据生成 GitHub 卡片，不根据图片内容理解生成。
9. 如果画面中在 cue 附近有指、拖、划、圈选或画线手势，优先使用手势位置作为图片/动画锚点。
10. 如果没有自定义风格，默认使用 `Remotion Native Material Cards / Remotion 原生材质卡片`。明确选择时，也可以使用可选内置风格：`Holographic Glass HUD / 全息玻璃 HUD`、`Frosted Glass Packaging / 毛玻璃包装`、`Reference HUD Pattern / 参考 HUD 信息图`。
11. 调用 `$video-use`，基于最终视频、时间包、风格、素材清单、手势 cue、卡片风格库和关键词动效库生成包装动效设计稿。
12. 等用户确认。
13. 用 Remotion + GSAP 实现确认后的动效。
14. 只打开 Remotion Studio 预览。
15. 用户确认 Studio 效果后再导出。

## Core Rules / 核心规则

- Do not edit, package, render, or export before the matching confirmation step.
- Generate independent SRT by default; do not burn subtitles unless explicitly requested.
- Trigger all packaging animations from subtitle/voice keyword cue points.
- Use matching current Codex project assets by default when asset filenames, path segment names, or filename-token aliases match subtitle keywords.
- Do not match assets by understanding image content, OCR, inferred labels, or subject classification.
- Do not search the uploaded video's source folder for packaging assets unless the user explicitly designates it as an asset source.
- Parse GitHub-like reference images, or filename/metadata-based GitHub assets, into editable `GitHubRepoCard` overlays with owner, repo, function, and languages.
- Use visible pointing, dragging, swiping, circling, or line-drawing gestures as preferred placement anchors.
- Avoid blocking faces, mouths, gestures, and the subtitle safe zone.
- Do not generate global top or bottom video progress bars.
- Do not switch away from Remotion + GSAP for animation implementation.
- Never commit `.env`, API keys, tokens, or local secrets.

- 未到对应确认节点前，不剪辑、不包装、不渲染、不导出。
- 默认生成独立 SRT；除非用户明确要求，否则不烧录字幕。
- 所有包装动效必须按语音/字幕关键词落点触发。
- 默认使用当前 Codex 项目素材；当素材文件名、路径片段名称或文件名分词别名匹配字幕关键词时，优先使用该素材。
- 不要通过理解图片内容、OCR、推断标签或主体分类来匹配素材。
- 除非用户明确指定，否则不要去用户上传视频所在源文件夹里找包装素材。
- 把 GitHub 类参考图，或基于文件名/元数据的 GitHub 素材，解析成可编辑的 `GitHubRepoCard`，包含用户名、项目名、项目功能和语言。
- 当画面中有指、拖、划、圈选或画线手势时，优先把它作为包装元素的放置锚点。
- 避免遮挡脸、嘴、手势和字幕安全区。
- 不生成顶部/底部整条视频进度条。
- 动画实现不切换到 Remotion + GSAP 以外的方案。
- 不提交 `.env`、API key、token 或任何本地密钥。

## Included Files / 包含文件

- `SKILL.md`
- `agents/openai.yaml`
- `references/default-design.md`
- `references/card-style-library.md`
- `references/keyword-animation-effects.md`
- `references/visual-quality-system.md`

The repository intentionally does not include local videos, Remotion projects, generated renders, reference images, or API keys.

仓库有意不包含本地视频、Remotion 工程、生成成片、参考图片或 API key。

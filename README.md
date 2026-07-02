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
5. Generate or normalize SRT, transcript, timestamp, edit-decision, and keyword-cue data into one packaging timing bundle.
6. Ask whether the user wants a custom style Markdown or reference image.
7. If no custom style is provided, use the default `Remotion Native Material Cards` style. Optional built-in styles are available when explicitly selected: `Holographic Glass HUD`, `Frosted Glass Packaging`, and `Reference HUD Pattern`.
8. Use `$video-use` to generate a packaging motion design draft from the final video, timing bundle, selected style, card style library, and keyword animation library.
9. Wait for user confirmation.
10. Implement the approved design with Remotion + GSAP.
11. Open Remotion Studio for preview only.
12. Export only after the user confirms the Studio preview.

1. 先问用户是否使用 ElevenLabs，还是使用本地 Whisper 转写。
2. 再问用户是否需要剪辑。
3. 如果需要剪辑，让用户提供源视频文件夹，并选择默认剪辑或精剪。
4. 如果不需要剪辑，让用户提供已经剪辑好的视频。
5. 将 SRT、转写文本、时间戳、剪辑决策和关键词 cue 统一整理为包装时间包。
6. 询问是否使用自定义风格 Markdown 或参考图片。
7. 如果没有自定义风格，默认使用 `Remotion Native Material Cards / Remotion 原生材质卡片`。明确选择时，也可以使用可选内置风格：`Holographic Glass HUD / 全息玻璃 HUD`、`Frosted Glass Packaging / 毛玻璃包装`、`Reference HUD Pattern / 参考 HUD 信息图`。
8. 调用 `$video-use`，基于最终视频、时间包、风格、卡片风格库和关键词动效库生成包装动效设计稿。
9. 等用户确认。
10. 用 Remotion + GSAP 实现确认后的动效。
11. 只打开 Remotion Studio 预览。
12. 用户确认 Studio 效果后再导出。

## Core Rules / 核心规则

- Do not edit, package, render, or export before the matching confirmation step.
- Generate independent SRT by default; do not burn subtitles unless explicitly requested.
- Trigger all packaging animations from subtitle/voice keyword cue points.
- Avoid blocking faces, mouths, gestures, and the subtitle safe zone.
- Do not generate global top or bottom video progress bars.
- Do not switch away from Remotion + GSAP for animation implementation.
- Never commit `.env`, API keys, tokens, or local secrets.

- 未到对应确认节点前，不剪辑、不包装、不渲染、不导出。
- 默认生成独立 SRT；除非用户明确要求，否则不烧录字幕。
- 所有包装动效必须按语音/字幕关键词落点触发。
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

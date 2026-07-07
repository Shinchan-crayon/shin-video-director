# Shin-video 总导演

Shin-video 总导演 是 Shin-video 口播视频工作流中的一个 Skill。

## 定位

- 中文名：Shin-video 总导演
- 英文名：`shin-video-director`
- 所属场景：总控
- 工作流目标：给文章，确认口播，放入口播音频，Agent 自动生成视频。

## 安装

这个仓库是 **总控入口包**，负责判断材料状态和调度工作流。它不是完整工作流包，单独安装它只能得到入口调度能力。

如果你要完整运行 Shin-video，需要同时安装下面 10 个子 Skill：

```bash
npx skills add https://github.com/Shinchan-crayon/web-article-cleaner
npx skills add https://github.com/Shinchan-crayon/voiceover-writer
npx skills add https://github.com/Shinchan-crayon/audio-checker
npx skills add https://github.com/Shinchan-crayon/whisperx-transcriber
npx skills add https://github.com/Shinchan-crayon/scene-generator
npx skills add https://github.com/Shinchan-crayon/subtitle-rhythm-builder
npx skills add https://github.com/Shinchan-crayon/remotion-video-director
npx skills add https://github.com/Shinchan-crayon/style-consultant
npx skills add https://github.com/Shinchan-crayon/animation-preset-builder
npx skills add https://github.com/Shinchan-crayon/remotion-renderer
```

最后再安装总控入口：

```bash
npx skills add https://github.com/Shinchan-crayon/shin-video-director
```

如果你拿到的是完整工作流包，优先按 `workflow-manifest.json` 和 `AGENT-先读我.md` 安装，不要只装总控入口。

## 使用方式

安装后，向 Agent 说明你要使用「Shin-video 总导演」即可。

示例：

```text
请使用 Shin-video 总导演，继续处理当前 Shin-video 项目。
```

## 输入与输出

请以 `SKILL.md` 为准。Shin-video v1 的统一输出规范是：

```text
文章正文.md
口播文案.md
口播音频.mp3
runtime/asr.json
runtime/scenes.json
runtime/director-plan.json
runtime/style-profile.json
runtime/preview.mp4
成品/成品.mp4
```

## 外部依赖

本 Skill 属于 Shin-video 工作流封装。根据具体阶段，它可能调用文本模型、音频模型、WhisperX、faster-whisper、Remotion、Node.js、Python 或 FFmpeg。

音频默认使用 MiniMax `speech-2.8-hd` 生成。如果 MiniMax 暂时不可用，或用户已经有音频，允许用户手动提供 `口播音频.mp3`，然后继续执行音频检查、WhisperX 时间戳、导演方案和 Remotion 渲染，不要让整条管线卡死在 TTS。

## 能力边界

- v1 不把图片链路作为必要步骤。
- 口播文案只能包含要念出来的话。
- 不允许在口播文案里写画面提示、分镜说明或 Remotion 指令。
- 不能跳过 WhisperX 时间戳步骤。
- 不能跳过 `runtime/director-plan.json` 和 `runtime/style-profile.json`。
- 最终成片固定输出到 `成品/成品.mp4`。

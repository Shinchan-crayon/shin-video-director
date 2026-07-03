# Shin-video 总导演

Shin-video 总导演 是 Shin-video 口播视频工作流中的一个 Skill。

## 定位

- 中文名：Shin-video 总导演
- 英文名：`shin-video-director`
- 所属场景：总控
- 工作流目标：给文章，确认口播，放入口播音频，Agent 自动生成视频。

## 安装

```bash
npx skills add https://github.com/Shinchan-crayon/shin-video-director
```

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
runtime/style-profile.json
runtime/preview.mp4
成品/成品.mp4
```

## 外部依赖

本 Skill 属于 Shin-video 工作流封装。根据具体阶段，它可能调用文本模型、音频模型、WhisperX、faster-whisper、Remotion、Node.js、Python 或 FFmpeg。

WhisperX、Remotion、faster-whisper 不是 ThinkAI 自研，也不是本仓库自研。

## 能力边界

- v1 不把图片链路作为必要步骤。
- 口播文案只能包含要念出来的话。
- 不允许在口播文案里写画面提示、分镜说明或 Remotion 指令。
- 不能跳过 WhisperX 时间戳步骤。
- 最终成片固定输出到 `成品/成品.mp4`。

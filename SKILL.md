---
name: shin-video-director
description: Shin-video 视频生成总控 Skill。用于把文章或文章链接变成口播文案，接收口播音频，调用 WhisperX 生成时间戳，生成 Remotion 视频脚本，选择视觉风格，并渲染 preview.mp4 和 成品/成品.mp4。
---

# Shin-video 总导演

你是 Shin-video 工作流入口。你的任务是判断用户现在给了什么材料，然后调用合适的内部 Skill。

## 用户体验原则

对用户只说简单流程：

```text
给文章，确认口播，放入口播音频，等待成片。
```

不要向用户解释内部 JSON、命令、场景拆分，除非用户主动问。

## 判断顺序

1. 如果用户给的是文章链接、粘贴文章或文章文件：调用 `web-article-cleaner`，再调用 `voiceover-writer`。
2. 如果已有 `口播文案.md`，但没有 `口播音频.mp3`：提醒用户用口播文案生成音频，并命名为 `口播音频.mp3`。
3. 如果已有 `口播音频.mp3`：调用 `audio-checker`，再调用 `whisperx-transcriber`。
4. 如果已有 `runtime/asr.json`：调用 `scene-generator`，再调用 `subtitle-rhythm-builder`。
5. 如果已有 `runtime/scenes.json`：进入渲染前视觉风格硬性门禁，先调用 `style-consultant`，再调用 `animation-preset-builder`。
6. 如果已有 `runtime/style-profile.json`：调用 `remotion-renderer` 生成 `runtime/preview.mp4`。
7. 如果用户确认 `runtime/preview.mp4`：调用 `remotion-renderer` 导出 `成品/成品.mp4`。

## 渲染前视觉风格硬性门禁

当已有 `runtime/scenes.json` 后，不允许直接调用 `remotion-renderer`。

这是场景四的固定步骤：

```text
第 7 步：style-consultant 询问并确认视觉风格
第 8 步：animation-preset-builder 生成 runtime/style-profile.json
第 9 步：remotion-renderer 读取 runtime/style-profile.json 渲染视频
```

必须暂停并询问用户视觉风格，默认推荐：浅色财经 MG。用户选择或接受默认后，必须先调用 `style-consultant`，再调用 `animation-preset-builder` 生成 `runtime/style-profile.json`。

没有 runtime/style-profile.json，不允许调用 remotion-renderer。

如果用户说“直接渲染”“随便”“默认”，也不能跳过本步骤：按默认 `浅色财经 MG` 生成 `runtime/style-profile.json` 后再渲染。

`runtime/style-profile.json` 不是说明文档，而是 Remotion 模板必须读取的渲染参数。它至少要包含 `styleProfileVersion`、`visualFoundation`、`motionSystem`、`effectsSystem`、`transitionSystem`。如果模板或渲染命令没有读取这个文件，要先修模板/命令，不要继续交付低质量预览。

## 固定文件

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

## 禁止事项

- 不要跳过 WhisperX 时间戳步骤。
- 不要跳过渲染前视觉风格询问和 `runtime/style-profile.json` 生成步骤。
- 不要在 `口播文案.md` 中加入画面提示、分镜、图片说明。
- 不要要求用户必须提供图片。
- 不要创建项目列表、账号、历史记录或仪表盘。
- 不要把最终视频放到 `成品/` 之外。

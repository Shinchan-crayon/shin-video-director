# 使用指南

## 适用场景

当 Shin-video 工作流进入「总控」阶段，并需要执行「Shin-video 总导演」时使用。

## 推荐提示词

```text
请使用「Shin-video 总导演」处理当前 Shin-video 项目。
```

## 项目文件结构

```text
视频项目/
├── 文章正文.md
├── 口播文案.md
├── 口播音频.mp3
├── runtime/
│   ├── asr.json
│   ├── scenes.json
│   ├── style-profile.json
│   └── preview.mp4
└── 成品/
    └── 成品.mp4
```

## 前期准备

开始完整工作流前，Agent 需要确认：

- Node.js 和 npm 可用；
- Python 可用；
- FFmpeg 可用；
- WhisperX 和 faster-whisper 模型可用；
- Remotion 和 Shin-video 视频模板可用；
- 用户已经提供文章或文章链接；
- 用户确认后的口播音频命名为 `口播音频.mp3`。

## 失败处理

- 缺文章：请用户提供文章或链接。
- 缺音频：提醒用户把口播音频放入项目并命名为 `口播音频.mp3`。
- 缺 WhisperX：先部署本地 WhisperX 环境，不要手工编时间戳。
- 缺 Remotion：先部署 Remotion 环境，不要跳过预览。

## 输出原则

- 中间文件放 `runtime/`。
- 预览视频放 `runtime/preview.mp4`。
- 最终成片放 `成品/成品.mp4`。

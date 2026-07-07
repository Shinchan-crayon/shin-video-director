# 来源与能力边界

## Skill

- 中文名：Shin-video 总导演
- 英文名：`shin-video-director`
- 所属场景：总控

## 来源

本 Skill 属于 Shin-video 口播视频工作流，由 ThinkAI / Shin-video 进行中文化、流程整理和上架封装。

## 工作流基础

Shin-video 基于：

- 文本模型：整理文章并生成自然口播文案；
- 音频模型：由用户根据口播文案生成 `口播音频.mp3`；
- WhisperX：读取音频并生成精准时间戳；
- faster-whisper：作为本地 ASR 模型能力；
- Remotion：按时间轴和动画配置渲染 MP4；
- FFmpeg / 编码环境：支撑视频编码和基础检查。

## 边界

- 本仓库不是 WhisperX 官方仓库。
- 本仓库不是 Remotion 官方仓库。
- v1 不做图片素材链路。
- v1 不做网站、账号、项目列表、历史记录或仪表盘。

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

如果用户是第一次使用，或明确问“怎么安装、怎么配置、怎么开始”，先引导用户完成新手准备，不要直接进入视频制作。

需要读取并遵守：

```text
references/新手小白安装与制作教程.md
references/Agent执行指南.md
references/前期准备说明.md
references/WhisperX和Remotion部署说明.md
```

首次运行必须检查 Node.js、npm、Python、FFmpeg、Remotion、WhisperX、faster-whisper 模型和 `.env.local`。MiniMax 音频模型默认使用 `speech-2.8-hd`，默认 `voice_id` 为 `Chinese (Mandarin)_Reliable_Executive`。如果缺 `MINIMAX_API_KEY`，只提醒用户写入 `.env.local`，不要要求用户把 API Key 发到对话里，也不要把真实 API Key 写入 GitHub、Skill 或说明文档。

本 Skill 是总控入口，不是完整工作流包。首次安装或排查时，必须确认 10 个子 Skill 是否都已安装：`web-article-cleaner`、`voiceover-writer`、`audio-checker`、`whisperx-transcriber`、`scene-generator`、`subtitle-rhythm-builder`、`remotion-video-director`、`style-consultant`、`animation-preset-builder`、`remotion-renderer`。缺少任一子 Skill 时，先提示安装完整工作流，不要假装总控入口能独立完成全部视频。

渲染前视觉风格选择由 `style-consultant` 负责（已升级为中文名 + 英译的四预设体系：暗海军蓝数据风（Data-Rich / Bloomberg）、黑底发布极简风（Minimal / Jobs）、暖色叙事风（Warm / Narrative）、终端技术风（Terminal / Hacker））。首次运行需读取 `references/video-archetypes.md` 了解完整的创意方向预设。这里不设置默认风格，必须采用一问一答确认用户的画面需求和视频比例。

MiniMax 是默认自动 TTS 路径，不是唯一入口。如果 MiniMax 请求失败、缺少 Key、额度不足，或用户已经有音频，允许用户手动提供 `口播音频.mp3` 后继续执行 `audio-checker`、`whisperx-transcriber`、`scene-generator`、`remotion-video-director`、`animation-preset-builder` 和 `remotion-renderer`。不要因为 TTS 失败中断后续所有步骤。

## 判断顺序

1. 如果用户给的是文章链接、粘贴文章或文章文件：调用 `web-article-cleaner`，再调用 `voiceover-writer`。
2. 如果已有 `口播文案.md`，但没有 `口播音频.mp3`：默认用 MiniMax 生成音频；MiniMax 不可用时，提醒用户手动生成或上传音频，并命名为 `口播音频.mp3`。
3. 如果已有 `口播音频.mp3`：调用 `audio-checker`，再调用 `whisperx-transcriber`。
4. 如果已有 `runtime/asr.json`：调用 `scene-generator`，再调用 `subtitle-rhythm-builder`。
5. 如果已有 `runtime/scenes.json`，但没有 `runtime/director-plan.json`：必须调用 `remotion-video-director`，先完成 `Think -> Design -> Build -> Review` 导演方案。
6. 如果已有 `runtime/director-plan.json`：确认用户已经看过并认可导演稿；未确认时停下让用户审，不进入风格和渲染。
7. 如果用户已确认导演稿：进入渲染前视觉风格硬性门禁，先调用 `style-consultant`，再调用 `animation-preset-builder`。
8. 如果已有 `runtime/style-profile.json`：调用 `remotion-renderer` 生成 `runtime/preview.mp4`。
9. 如果用户确认 `runtime/preview.mp4`：调用 `remotion-renderer` 导出 `成品/成品.mp4`。

## 渲染前视觉风格硬性门禁

当已有 `runtime/scenes.json` 后，不允许直接调用 `style-consultant`、`animation-preset-builder` 或 `remotion-renderer`。

这是场景四的固定步骤：

```text
对外执行流程第 6 步：必须暂停并询问用户视觉风格和视频比例
内部第 7 步：remotion-video-director 生成 runtime/director-plan.json
内部第 8 步：style-consultant 询问并确认视觉风格
内部第 9 步：animation-preset-builder 读取 runtime/director-plan.json 并生成 runtime/style-profile.json
内部第 10 步：remotion-renderer 检查 Remotion / zod 依赖，读取 runtime/director-plan.json 和 runtime/style-profile.json 渲染视频
```

必须先调用 `remotion-video-director` 生成导演方案，再暂停并询问用户视觉风格和视频比例。用户明确选择或描述需求后，必须先调用 `style-consultant`，再调用 `animation-preset-builder` 生成 `runtime/style-profile.json`。

`remotion-video-director` 必须先给用户输出可审导演稿，至少包含 Creative Brief、Narrative Arc、Scene Sequence、Audio Strategy、Visual Risks 和 Review Checklist。用户未确认导演稿前，不允许进入 `style-consultant`、`animation-preset-builder` 或 `remotion-renderer`。

没有 runtime/director-plan.json，不允许调用 animation-preset-builder 或 remotion-renderer。
没有 runtime/style-profile.json，不允许调用 remotion-renderer。

如果用户说“直接渲染”“随便”“默认”“你看着办”，也不能跳过本步骤，更不能套默认配置；必须继续追问，直到用户明确选择风格或描述画面需求。

`runtime/director-plan.json` 是 Remotion 视频生成架构设计器和导演系统的输出。它必须包含 `directorPlanVersion`、`videoDirectorWorkflow`、`remotionExecutionContract`、`creativeBrief`、`narrativeArc`、`attentionCurve`、`peakMoments`、`globalRules` 和按 scene 拆分的 `directorType`、`importance`、`role`、`shotStyle`、`cameraMotion`、`entranceAnimation`、`emphasisAnimation`、`transitionAnimation`、`background`、`components`、`content`、`safeArea`。它的作用是防止视频只套用固定模板，要求每条视频都根据当前文章重新设计镜头语言。

`runtime/director-plan.json` 还必须包含 `audioStrategy` 和 `reviewScorecard`。`audioStrategy` 要明确口播优先、是否使用音乐、是否需要 ducking；`reviewScorecard` 要给出 hook、节奏、可读性、字幕安全区、音画关系和受众匹配的审片结果。

`videoDirectorWorkflow` 必须标明已适配 `bayramannakov/remotion-video-director` 的方法论：`Think -> Design -> Build -> Review`、`one-scene-one-idea`、archetype 映射和专家审片协议。这里学习的是导演流程，不是照搬对方的英文产品视频模板。

`remotionExecutionContract` 必须约束 renderer：角色是 `execute-director-plan`，字幕系统是 `audio-timeline-independent`，主画面策略是 `explain-not-repeat-caption`，并遵守 frame-driven animation、clamped interpolation、Sequence 时间轴、组件复用和文本测量。

`runtime/style-profile.json` 不是说明文档，而是 Remotion 模板必须读取的渲染参数。它至少要包含 `styleProfileVersion`、`visualFoundation`、`motionSystem`、`effectsSystem`、`transitionSystem`、`recipeSystem`、`motionModules`、`transitionModules`、`effectModules`、`antiRepetitionRules`。

`runtime/scenes.json` 也不能只写字幕。每个 scene 必须尽量包含 `sceneType`、`visualIntent`、`layoutRecipe`、`motionVariant`、`transition`、`visualModules`、`pace`。Remotion 模板必须按 `sceneType` 分发到不同场景组件，并按 `motionVariant` / `visualModules` 组合动画。这样同一条视频可以保持统一主题，但每 5-15 秒呈现方式不同，避免整条片子像同一个 PPT 模板循环。

如果模板或渲染命令没有读取 `runtime/director-plan.json` 和 `runtime/style-profile.json`，或没有使用 scene 里的 `sceneType` / `motionVariant` / `visualModules` 以及导演方案里的 `directorType` / `shotStyle` / `cameraMotion` / `remotionExecutionContract`，要先修模板/命令，不要继续交付低质量预览。

进入 `remotion-renderer` 前，必须让它执行依赖检查：`npm ls zod @remotion/bundler @remotion/cli @remotion/renderer remotion --depth=0`。当前 Shin-video 模板要求 Remotion 4.0.484 与 `zod` 4.3.6；如果出现 `Remotion version mismatch` 或版本不一致，先修依赖再渲染。

## 固定文件

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

## 禁止事项

- 不要跳过 WhisperX 时间戳步骤。
- 不要跳过 `remotion-video-director` 和 `runtime/director-plan.json`。
- 不要跳过渲染前视觉风格询问和 `runtime/style-profile.json` 生成步骤。
- 不要在 `口播文案.md` 中加入画面提示、分镜、图片说明。
- 不要要求用户必须提供图片。
- 不要创建项目列表、账号、历史记录或仪表盘。
- 不要把最终视频放到 `成品/` 之外。
- 不要复用上一条视频的背景、排版和动效模板来糊弄当前文章。

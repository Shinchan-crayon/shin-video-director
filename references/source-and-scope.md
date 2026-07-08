# 来源、职责与边界：Shin-video 总导演

## 基本信息

- 中文名：Shin-video 总导演
- 英文名：`shin-video-director`
- 工作流角色：总控编排
- 所属阶段：入口与全流程调度

## 要解决的问题

用户不知道从文章、音频、时间轴、导演方案、风格配置到 Remotion 成片应该先做哪一步。这个 Skill 负责识别当前材料状态，把任务交给正确的下游 Skill，并阻止跳过关键步骤。

## 输入

- 文章链接、文章正文、已有口播文案或已有口播音频
- 当前项目目录中的固定文件
- 用户对风格、比例、素材、质量的确认

## 输出

- 下一步执行指令
- 完整工作流状态判断
- 最终引导到 runtime/preview.mp4 和 成品/成品.mp4

## 上下游关系

上游：
- 用户输入

下游：
- web-article-cleaner
- voiceover-writer
- audio-checker
- whisperx-transcriber
- scene-generator
- subtitle-rhythm-builder
- remotion-video-director
- style-consultant
- animation-preset-builder
- remotion-renderer

## 具体职责

- 新手不知道怎么开始
- Agent 容易跳步骤
- 生成视频前缺少导演方案或视觉风格确认
- TTS 失败时整条链路被错误中断

## 不负责什么

- 单独替代全部子 Skill
- 直接生成 MP4
- 绕过 WhisperX 或 Remotion

## 在 AI 员工体系中的位置

这个 Skill 是一个员工能力模块，不是完整员工页面。完整员工页面会说明：

- 这个 AI 员工能解决什么问题；
- 背后由哪些 Skill 组成；
- 适合谁使用；
- 如何安装；
- 有哪些 Demo；
- 免费版、付费版和定制服务如何区分。

## 质量要求

- 必须遵守上游输入，不编造缺失文件。
- 必须生成明确输出，不能只写建议。
- 必须在边界内工作，不能抢下游 Skill 的职责。
- 失败时要说明缺哪个输入、哪个环境或哪个用户确认。

## 来源说明

本 Skill 属于 Shin-video 工作流封装，用于把文章、口播、音频、时间轴、导演方案、视觉风格和 Remotion 渲染串成稳定流程。它调用或依赖的外部工具以各自官方能力为准；本仓库只负责工作流编排、中文化说明和执行约束。

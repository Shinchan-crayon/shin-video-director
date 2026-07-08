# Shin-video 总导演

> Shin-video AI 员工体系中的「总控编排」Skill。它不是孤立提示词，而是整条文章转口播视频流水线的一环。

## 它能解决什么问题

用户不知道从文章、音频、时间轴、导演方案、风格配置到 Remotion 成片应该先做哪一步。这个 Skill 负责识别当前材料状态，把任务交给正确的下游 Skill，并阻止跳过关键步骤。

## 它在工作流里的位置

**阶段：** 入口与全流程调度

**上游：**
- 用户输入

**下游：**
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

## 输入什么

- 文章链接、文章正文、已有口播文案或已有口播音频
- 当前项目目录中的固定文件
- 用户对风格、比例、素材、质量的确认

## 输出什么

- 下一步执行指令
- 完整工作流状态判断
- 最终引导到 runtime/preview.mp4 和 成品/成品.mp4

## 背后由哪些能力组成

这个 Skill 自身负责：

- 新手不知道怎么开始
- Agent 容易跳步骤
- 生成视频前缺少导演方案或视觉风格确认
- TTS 失败时整条链路被错误中断

它通常由 `shin-video-director` 总控调用，也可以在当前项目材料已经准备好时单独调用。

## 每个 Skill 的作用

在完整 AI 员工体系里，本 Skill 的职责是：**总控编排**。

它不负责：

- 单独替代全部子 Skill
- 直接生成 MP4
- 绕过 WhisperX 或 Remotion

## 演示截图 / 视频

![Shin-video Demo](docs/demo/style-comparison.png)

演示重点：完整流程 Demo：文章输入 -> 口播文案 -> 音频 -> 时间轴 -> 导演方案 -> 风格确认 -> MP4。

完整视频 Demo 建议放在总控仓库或 ThinkAI Skill 页面中展示；单个 Skill 仓库保留截图和阶段说明，避免每个子仓库重复放大视频文件。

## 适合谁买

想把文章稳定做成本地口播视频的内容团队、AI 工具玩家、工作流搭建者。

## 下载 / 安装方式

```bash
npx skills add https://github.com/Shinchan-crayon/shin-video-director
```

安装后，在支持 Skill 的 Agent 中说：

```text
请使用 Shin-video 总导演，继续处理当前 Shin-video 项目。
```

## 付费版本和定制服务入口

- 免费版：安装本 Skill，按本地 Shin-video 工作流手动配置运行。
- 付费版：可提供整套工作流安装包、环境部署协助、示例项目和远程排障。
- 定制服务：可按行业定制口播风格、导演规则、Remotion 模板、品牌视觉系统和 ThinkAI 上架页面。

咨询入口：ThinkAI Skill 广场 / GitHub Issues / 私信定制咨询

## 验收标准

使用本 Skill 后，至少要能确认：

- 已正确生成或推进到：下一步执行指令
- 已正确生成或推进到：完整工作流状态判断
- 已正确生成或推进到：最终引导到 runtime/preview.mp4 和 成品/成品.mp4
- 没有绕过它的上游依赖。
- 没有把本 Skill 明确不负责的事情混进来。
- 出错时能说清楚卡在哪个输入或环境条件上。

## 能力边界

- 单独替代全部子 Skill
- 直接生成 MP4
- 绕过 WhisperX 或 Remotion

Shin-video 追求的是「可维护的本地视频生成工作流」，不是把所有能力塞进一个万能提示词。这个 Skill 只负责自己这一段，完整成片需要配合其它 AI 员工和本地 Shin-video 项目。

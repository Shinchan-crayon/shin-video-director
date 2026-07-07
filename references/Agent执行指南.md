# Shin-video Agent 执行指南

这份指南给 Agent 看。目标是让新手用户不用懂环境、命令和内部 JSON，也能完成一次完整的视频制作。

## 一、首次运行先做准备检查

当用户是第一次使用 Shin-video，或者项目目录没有明确运行记录时，先读取：

```text
references/新手小白安装与制作教程.md
references/前期准备说明.md
references/WhisperX和Remotion部署说明.md
workflow-manifest.json
skills/shin-video-director/SKILL.md
```

不要直接进入生成视频。先检查：

```bash
node -v
npm -v
python3 --version
ffmpeg -version
npx remotion --version
```

再检查 `.env.local` 是否存在。

还要确认这不是只安装了总控入口。完整 Shin-video 至少需要：

```text
shin-video-director
web-article-cleaner
voiceover-writer
audio-checker
whisperx-transcriber
scene-generator
subtitle-rhythm-builder
remotion-video-director
style-consultant
animation-preset-builder
remotion-renderer
```

缺少子 Skill 时，先补安装，不要把总控入口当成完整工作流。

## 二、环境变量检查

必须检查 `.env.local`，但不要把真实密钥打印给用户。

必需项：

```text
MINIMAX_API_KEY
MINIMAX_TTS_ENDPOINT
MINIMAX_TTS_MODEL
MINIMAX_VOICE_ID
WHISPERX_PYTHON
WHISPERX_MODEL
DEFAULT_OUTPUT_ROOT
```

推荐默认：

```text
MINIMAX_TTS_ENDPOINT=https://api.minimax.chat/v1/t2a_v2
MINIMAX_TTS_MODEL=speech-2.8-hd
MINIMAX_VOICE_ID=Chinese (Mandarin)_Reliable_Executive
```

如果缺 MiniMax Key，只提示：

```text
请把 MiniMax API Key 写入 .env.local 的 MINIMAX_API_KEY。
```

不要要求用户把 API Key 发到对话里。

如果已经出现在对话里，不要写进 GitHub、README、Skill 或上传指令。

MiniMax 是默认自动生成音频的路径。MiniMax 不可用时，不要终止整个工作流；如果用户能提供 `口播音频.mp3`，就从音频检查继续往下跑。

## 三、完整执行链路

固定链路：

```text
文章或文章链接
→ web-article-cleaner
→ 文章正文.md
→ voiceover-writer
→ 口播文案.md
→ MiniMax speech-2.8-hd 或用户手动提供音频
→ 口播音频.mp3
→ audio-checker
→ whisperx-transcriber
→ runtime/asr.json
→ scene-generator
→ subtitle-rhythm-builder
→ runtime/scenes.json
→ remotion-video-director
→ runtime/director-plan.json
→ style-consultant
→ animation-preset-builder
→ runtime/style-profile.json
→ remotion-renderer
→ runtime/preview.mp4
→ 用户确认
→ 成品/成品.mp4
```

禁止跳过：

```text
MiniMax 音频确认
WhisperX 时间戳
Remotion 视频导演
视觉风格和比例确认
预览确认
```

## 四、和新手用户的对话方式

只暴露用户需要操作的步骤。

推荐说法：

```text
我先帮你检查环境，然后你给我文章，我会生成一版口播文案给你确认。
```

口播文案完成后：

```text
这是口播文案。你确认后，我会用 MiniMax 生成口播音频，并给你试听确认。
```

音频完成后：

```text
音频已经生成。你确认后，我会生成时间戳、视频脚本和预览视频。
```

渲染前：

```text
渲染前需要确认视频风格和比例。默认推荐浅色财经 MG，比例默认 16:9。
```

预览完成后：

```text
预览视频已生成。你确认没问题后，我再导出最终 MP4。
```

不要让用户学习：

```text
WhisperX 命令
Remotion 命令
scenes.json
director-plan.json
style-profile.json
```

除非用户主动问。

## 五、口播文案规则

`口播文案.md` 只能写要念出来的话。

不能包含：

```text
【画面】
分镜说明
图片提示
Remotion 指令
字幕编号
时间码
```

口播文案确认后，再生成音频。

## 六、MiniMax 生成音频规则

默认使用：

```text
model: speech-2.8-hd
voice_id: Chinese (Mandarin)_Reliable_Executive
```

输出文件必须统一为：

```text
口播音频.mp3
```

生成音频后必须让用户试听确认。用户不满意时，先改口播文案、音色或参数，再重新生成，不要直接进入 WhisperX。

## 七、WhisperX 和字幕规则

WhisperX 用来提供音频时间轴。

字幕文本应优先使用用户确认过的 `口播文案.md`，再结合 WhisperX 时间戳切句。不要直接把 WhisperX 识别错的文本当最终字幕。

字幕原则：

```text
一整句话尽量完整显示
不要一句话只显示半截就切走
字幕固定在底部安全区
字幕跟音频进度走，不要被主画面卡片绑死
```

## 八、Remotion 视频导演规则

`runtime/scenes.json` 生成后，必须进入：

```text
remotion-video-director
```

输出：

```text
runtime/director-plan.json
```

没有 `runtime/director-plan.json`，禁止进入动画预设和渲染。

导演方案必须解决：

```text
镜头不能单一
节奏不能平均
主画面不能复读完整字幕
背景和动效不能复用旧模板
字幕不能和画面重叠
默认不加噪点
默认不做无意义抖动
```

## 九、视觉风格和比例确认

渲染前必须询问：

```text
视频风格
视频比例
```

默认推荐：

```text
浅色财经 MG
16:9
```

如果用户选择 `9:16`，v1 可以采用 16:9 主画面居中，上下留白的方式，保证内容完整可读。

## 十、渲染前检查

调用 Remotion 前，检查：

```text
runtime/scenes.json
runtime/director-plan.json
runtime/style-profile.json
口播音频.mp3
```

检查 Remotion 依赖：

```bash
npm ls zod @remotion/bundler @remotion/cli @remotion/renderer remotion --depth=0
```

当前模板要求：

```text
Remotion 4.0.484
zod 4.3.6
```

如果版本不一致，先修依赖，不要硬渲染。

## 十一、交付前检查

预览生成后必须检查：

```text
视频是否生成
是否能播放
是否有声音
是否黑屏
时长是否接近音频时长
字幕是否完整
字幕是否遮挡主画面
主画面是否复读完整字幕
画面是否像固定 PPT 模板循环
```

通过后再让用户确认。用户确认后才能导出：

```text
成品/成品.mp4
```

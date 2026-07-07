# WhisperX 和 Remotion 本地部署说明

这份说明给 Agent 看，用来在一台新机器上部署 Shin-video v1 需要的本地环境。

普通用户不需要学习这些命令。Agent 只在发现环境缺失时执行或提示。

## 一、Shin-video 依赖什么

Shin-video v1 的完整链路是：

```text
文章正文.md
→ 口播文案.md
→ 口播音频.mp3
→ WhisperX 生成 runtime/asr.json
→ 生成 runtime/scenes.json
→ 生成 runtime/style-profile.json
→ Remotion 渲染 runtime/preview.mp4
→ 用户确认
→ Remotion 导出 成品/成品.mp4
```

必须具备：

```text
Node.js
npm
Remotion
Python
WhisperX
faster-whisper 模型
FFmpeg
```

推荐在 macOS 上用 Homebrew 安装基础工具。

## 二、先做环境检查

在终端执行：

```bash
node -v
npm -v
python3 --version
ffmpeg -version
```

判断：

- `node -v` 能输出版本，说明 Node.js 可用。
- `npm -v` 能输出版本，说明 npm 可用。
- `python3 --version` 建议为 Python 3.10 或 3.11。
- `ffmpeg -version` 能输出版本，说明 FFmpeg 可用。

如果缺 Homebrew：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

如果缺 Node.js：

```bash
brew install node
```

如果缺 Python：

```bash
brew install python@3.11
```

如果缺 FFmpeg：

```bash
brew install ffmpeg
```

## 三、部署 Remotion

### 方式 A：在 Shin-video 项目中安装依赖

如果用户拿到的是完整 Shin-video 项目目录，进入项目根目录：

```bash
cd "/path/to/Shin-video"
npm install
```

验证 Remotion CLI：

```bash
npx remotion --version
```

Shin-video 项目内建议脚本：

```bash
npm run remotion:studio
npm run render:preview
npm run render:final
```

输出约定：

```text
runtime/preview.mp4
成品/成品.mp4
```

### 方式 B：新建一个 Remotion 模板项目

如果机器上完全没有 Remotion 项目，可用官方脚手架创建：

```bash
npx create-video@latest
```

然后进入新项目：

```bash
npm install
npx remotion studio
```

Shin-video 正式运行时，仍然要使用 Shin-video 自己的模板、`runtime/scenes.json`、`runtime/style-profile.json` 和 `口播音频.mp3`。

### Remotion 常见问题

如果 `npx remotion --version` 失败：

```bash
npm install remotion @remotion/cli @remotion/renderer @remotion/bundler
```

如果渲染报 FFmpeg 或编码问题：

```bash
ffmpeg -version
```

如果缺 FFmpeg，执行：

```bash
brew install ffmpeg
```

## 四、部署 WhisperX

### 1. 创建独立 Python 环境

推荐不要把 WhisperX 装进系统 Python，单独建一个环境：

```bash
mkdir -p "$HOME/Documents/Ai剪辑/asr_runtime"
cd "$HOME/Documents/Ai剪辑/asr_runtime"
python3 -m venv whisperx-env
source whisperx-env/bin/activate
python -m pip install --upgrade pip setuptools wheel
```

### 2. 安装 WhisperX

```bash
pip install whisperx
```

验证：

```bash
python -m whisperx --help
```

如果能看到参数说明，表示 WhisperX 可以被调用。

### 3. 准备 faster-whisper 模型

Shin-video 不强制用户理解模型细节，但 Agent 必须知道模型路径。

推荐模型目录：

```text
$HOME/Documents/Ai剪辑/asr_runtime/models/faster-whisper-medium
```

如果用户已经有模型，直接记录已有路径。

如果没有模型，可让 WhisperX 首次运行时自动下载，或者用 Hugging Face 工具下载模型。示例：

```bash
source "$HOME/Documents/Ai剪辑/asr_runtime/whisperx-env/bin/activate"
pip install -U huggingface_hub
mkdir -p "$HOME/Documents/Ai剪辑/asr_runtime/models"
huggingface-cli download Systran/faster-whisper-medium \
  --local-dir "$HOME/Documents/Ai剪辑/asr_runtime/models/faster-whisper-medium"
```

如果下载需要登录 Hugging Face：

```bash
huggingface-cli login
```

### 4. WhisperX 转写命令模板

在视频项目目录中执行：

```bash
mkdir -p runtime
"$HOME/Documents/Ai剪辑/asr_runtime/whisperx-env/bin/python" \
  -m whisperx \
  "口播音频.mp3" \
  --model "$HOME/Documents/Ai剪辑/asr_runtime/models/faster-whisper-medium" \
  --language zh \
  --output_format json \
  --output_dir "runtime"
```

WhisperX 默认会输出一个以音频文件名命名的 JSON。Agent 要把它统一整理为：

```text
runtime/asr.json
```

示例：

```bash
cp runtime/口播音频.json runtime/asr.json
```

如果实际输出文件名不同，先用下面命令查找：

```bash
find runtime -maxdepth 1 -name "*.json" -print
```

再复制为 `runtime/asr.json`。

## 五、项目目录标准

每个视频项目保持这个结构：

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

不要把中间文件散落在项目根目录。

## 六、环境配置文件建议

如果项目需要固定路径，可在项目根目录建立 `.env.local`：

```bash
WHISPERX_PYTHON="$HOME/Documents/Ai剪辑/asr_runtime/whisperx-env/bin/python"
WHISPERX_MODEL="$HOME/Documents/Ai剪辑/asr_runtime/models/faster-whisper-medium"
WHISPERX_LANGUAGE="zh"
REMOTION_ENTRY="remotion/index.ts"
REMOTION_COMPOSITION_ID="ShinVideo"
```

Agent 读取配置时遵循：

1. 优先读取项目 `.env.local`。
2. 没有配置时使用上面的默认路径。
3. 默认路径不存在时，先检查本机已有路径。
4. 仍然找不到时，向用户说明缺哪一项，不要假装已部署。

## 七、完整自检

### WhisperX 自检

在视频项目目录中放一个短音频 `口播音频.mp3`，执行：

```bash
mkdir -p runtime
"$HOME/Documents/Ai剪辑/asr_runtime/whisperx-env/bin/python" \
  -m whisperx \
  "口播音频.mp3" \
  --model "$HOME/Documents/Ai剪辑/asr_runtime/models/faster-whisper-medium" \
  --language zh \
  --output_format json \
  --output_dir "runtime"
find runtime -maxdepth 1 -name "*.json" -print
```

通过标准：

```text
runtime/ 下出现 json 文件
json 里有 text/start/end 或 segments 等时间信息
```

### Remotion 自检

在 Shin-video 项目根目录执行：

```bash
npm install
npm run remotion:studio
```

如果需要渲染预览：

```bash
npm run render:preview
```

通过标准：

```text
runtime/preview.mp4 存在
视频可播放
有声音
不是黑屏
时长接近 口播音频.mp3
```

## 八、失败时怎么提示用户

缺音频：

```text
请把根据口播文案生成的音频放进当前项目，并命名为：口播音频.mp3
```

缺 WhisperX：

```text
当前机器还没有可用的 WhisperX 环境。我需要先部署 Python 虚拟环境、安装 whisperx，并配置 faster-whisper 模型路径。
```

缺模型：

```text
WhisperX 已安装，但没有找到 faster-whisper 模型目录。请提供已有模型路径，或允许我下载 Systran/faster-whisper-medium。
```

缺 Remotion：

```text
当前项目还没有可用的 Remotion 环境。我需要先执行 npm install，并确认 npx remotion --version 可以正常输出。
```

渲染失败：

```text
Remotion 渲染失败。我会先检查 scenes.json、style-profile.json、口播音频.mp3、FFmpeg 和输出目录，再重新渲染。
```

## 九、硬性原则

- 不要跳过 WhisperX。
- 不要手工编时间戳冒充 ASR。
- 不要让用户学习 WhisperX 或 Remotion 命令。
- 不要把最终成片输出到 `成品/` 之外。
- 不要把 `runtime/` 中间文件暴露成用户必须理解的概念。

## 十、参考来源

- WhisperX 官方仓库：https://github.com/m-bain/whisperX
- Remotion 官方文档：https://www.remotion.dev/docs
- Remotion 安装文档：https://www.remotion.dev/docs/install

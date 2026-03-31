# AI 播客剪辑工具

受 Descript 启发的 AI 播客编辑器，支持转录、文本编辑映射音频、口癖检测、音频增强。

## 功能规划

### 核心功能（MVP）
- [ ] 音频上传（MP3、WAV、M4A 等格式）
- [ ] AI 转录（阿里云 NLS，支持中文 + 中英混合）
- [ ] 波形可视化 + 词级时间戳文本同步显示
- [ ] 文本选择 → 删除对应音频片段
- [ ] 自动检测口癖 / 废话（规则 + LLM 双引擎）
- [ ] 导出处理后的音频

### 增强功能（后续）
- [ ] 音频降噪增强（Dolby.io API）
- [ ] 说话人分离（多人播客）
- [ ] 导出字幕文件（SRT / VTT）

## 技术栈

### 前端
| 技术 | 用途 |
|---|---|
| React + TypeScript + Vite | 主框架 |
| WaveSurfer.js | 音频波形可视化 |
| Slate.js | 带时间戳的可编辑转录文本 |
| TailwindCSS | 样式 |

### 后端
| 技术 | 用途 |
|---|---|
| Python FastAPI | API 服务 |
| FFmpeg / pydub | 音频剪辑与导出 |
| Celery + Redis | 异步转录任务队列 |

### 第三方 API
| 服务 | 用途 |
|---|---|
| 阿里云 NLS（录音文件识别） | 转录，支持词级时间戳 |
| 通义 / Claude API | 智能口癖 / 废话检测 |
| Dolby.io Enhance API | 音频降噪增强（可选） |

## 项目结构（规划）

```
podcast-editor/
├── frontend/          # React 前端
│   ├── src/
│   │   ├── components/
│   │   │   ├── Waveform/       # WaveSurfer.js 波形组件
│   │   │   ├── TranscriptEditor/  # Slate.js 转录编辑器
│   │   │   ├── AudioUpload/    # 音频上传
│   │   │   └── FillerPanel/    # 口癖检测面板
│   │   ├── hooks/
│   │   └── api/
│   └── package.json
├── backend/           # Python FastAPI 后端
│   ├── app/
│   │   ├── api/
│   │   │   ├── transcribe.py   # 转录接口
│   │   │   ├── edit.py         # 音频编辑接口
│   │   │   └── enhance.py      # 音频增强接口
│   │   ├── services/
│   │   │   ├── aliyun_asr.py   # 阿里云 NLS 封装
│   │   │   ├── audio_editor.py # FFmpeg 音频处理
│   │   │   └── filler_detect.py # 口癖检测
│   │   └── main.py
│   └── requirements.txt
├── docs/              # 文档
└── README.md
```

## 本地运行

### 环境变量配置

复制 `.env.example` 为 `.env` 并填写：

```bash
cp .env.example .env
```

```env
# 阿里云 NLS
ALIYUN_ACCESS_KEY_ID=your_access_key_id
ALIYUN_ACCESS_KEY_SECRET=your_access_key_secret
ALIYUN_NLS_APPKEY=M8NRtnUHJcq7l9ZZ

# LLM（口癖检测）
DASHSCOPE_API_KEY=your_dashscope_key   # 通义
# 或
OPENAI_API_KEY=your_openai_key

# Dolby.io（可选，音频增强）
DOLBY_API_KEY=your_dolby_key
```

### 启动

```bash
# 后端
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload

# 前端
cd frontend
npm install
npm run dev
```

## 关键技术细节

### 词级时间戳与音频剪辑

阿里云 NLS 返回格式示例：
```json
{
  "words": [
    { "word": "今天", "begin_time": 1200, "end_time": 1800 },
    { "word": "嗯", "begin_time": 1900, "end_time": 2100 },
    { "word": "我们", "begin_time": 2200, "end_time": 2600 }
  ]
}
```

用户删除"嗯"→ 生成剪切列表 → FFmpeg 拼接剩余片段：
```bash
ffmpeg -i input.mp3 -filter_complex \
  "[0:a]atrim=0:1.9[a1];[0:a]atrim=2.1[a2];[a1][a2]concat=n=2:v=0:a=1" \
  output.mp3
```

### 口癖自动检测

**第一层（规则匹配）：** 嗯、啊、那个、就是、对对对、然后然后、um、uh、like

**第二层（LLM语义）：** 将转录文本发给 LLM，识别整句冗余（如"我想说的是……我的意思就是……"）

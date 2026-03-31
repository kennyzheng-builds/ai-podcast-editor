# 项目讨论记录

## 2026-03-31 — 项目启动

### 需求确认

**目标产品**：AI 播客剪辑工具，参考 Descript
- 上传音频 → 自动转录
- 基于文本选择来删除音频片段
- 自动检测并删除口癖、废话、脏话
- 音频增强（录音棚音质效果，参考 Adobe Podcast）

**使用场景**：个人日常使用

**语言**：中文为主，夹杂英文词汇（如"Manus"、"Claude Code"等科技词汇）

**用户技术背景**：非技术背景

### 技术选型决策

**转录 API**：阿里云 NLS 录音文件识别
- 原因：中文 + 中英混合支持最好，已有账号
- AppKey：M8NRtnUHJcq7l9ZZ（语音识别项目）
- 需要补充：AccessKey ID + Secret

**前端框架**：React + TypeScript + Vite
- 波形：WaveSurfer.js
- 文本编辑器：Slate.js（支持词级时间戳挂载）
- 样式：TailwindCSS

**后端**：Python FastAPI + FFmpeg

**LLM（口癖检测）**：通义千问（待配置 DashScope API Key）

**音频增强**：Dolby.io API（后续添加，个人用免费额度 50 分钟/月）

### 待确认事项

- [ ] 阿里云 AccessKey ID + Secret（用于 NLS 鉴权）
- [ ] 通义 DashScope API Key（用于智能口癖检测）
- [ ] Dolby.io API Key（可选，音频增强）

### 核心架构设计

```
音频上传
  ↓
阿里云 NLS 转录（返回词级时间戳）
  ↓
前端展示：波形（WaveSurfer）+ 文本（Slate.js）双向同步
  ↓
用户操作：手动选词删除 / 一键删口癖
  ↓
生成剪切列表 → FFmpeg 拼接音频
  ↓
导出清洁音频
```

**最难的技术点**：文本编辑器与音频时间轴的双向同步（文本选区 ↔ 音频时间段）

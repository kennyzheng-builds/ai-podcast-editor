# Capy — Working Memory

## 当前主项目

**AI 播客剪辑工具**（参考 Descript）
- GitHub: https://github.com/kennyzheng-builds/ai-podcast-editor
- 状态：架构设计完成，等待 API Keys 后开始写代码

## 用户信息

- GitHub: `kennyzheng-builds`
- 语言：中文为主，偶尔英文
- 技术背景：非技术背景，不懂代码
- 沟通风格：简洁直接，不要 emoji

## 已确认的技术决策

| 模块 | 选型 |
|---|---|
| 转录 | 阿里云 NLS 录音文件识别 |
| 前端 | React + TypeScript + Vite |
| 波形 | WaveSurfer.js |
| 文本编辑器 | Slate.js |
| 后端 | Python FastAPI + FFmpeg |
| 口癖检测 | 规则 + LLM（通义） |
| 音频增强 | Dolby.io API（可选） |

## 已有的 API Keys

- 阿里云 NLS AppKey: `M8NRtnUHJcq7l9ZZ`（项目名：语音识别）

## 待用户提供

- 阿里云 AccessKey ID + Secret（NLS 鉴权必须）
- 通义 DashScope API Key（口癖检测）
- Dolby.io API Key（可选，音频增强）

## 重要文件位置

- 工作区: `/home/node/a0/workspace/dcfc044c-5f80-4b2f-b9af-cf5fdc2519c6/workspace/`
- 项目文档: `docs/conversation-log.md`
- 环境变量模板: `.env.example`

## 关键架构

音频上传 → 阿里云 NLS（词级时间戳）→ 波形+文本双向同步 → 文本选择删除 → FFmpeg 拼接 → 导出

## Recent Activity

- 2026-03-31: 项目启动，完成架构设计，GitHub 仓库创建

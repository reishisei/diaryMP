# Implementation Plan: AI 语音日记

**Branch**: `001-diary-basic` | **Date**: 2026-05-07 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `/specs/001-ai-voice-diary/spec.md`

## Summary

微信小程序日记应用，核心功能是通过 AI 语音对话来创建日记。用户通过语音与 AI 聊天，聊天结束后 AI 自动将对话内容整理成一篇日记保存在本地。辅助功能包括日记列表浏览和日记管理（编辑/删除）。

## Technical Context

**Language/Version**: JavaScript (ES6) + WXML + WXSS — 微信小程序原生框架
**Primary Dependencies**: 微信同声传译插件 (ASR+TTS), DeepSeek API (LLM)
**Storage**: 微信本地存储 (wx.setStorage / wx.getStorage)，结构化 JSON
**Testing**: 微信开发者工具 + 手动真机测试
**Target Platform**: 微信小程序 (iOS + Android)，开发版/体验版
**Project Type**: 微信小程序 (移动端应用)
**Performance Goals**: 首屏加载 < 1.5s，AI 语音回复 < 5s，包体积 < 2MB
**Constraints**: 小程序包体积限制 2MB；AI 对话功能必须联网；开发版可跳过域名校验
**Scale/Scope**: 个人单用户，不上线发布

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| 原则 | 状态 | 说明 |
|------|------|------|
| I. 隐私优先 (NON-NEGOTIABLE) | ⚠️ 部分例外 | 日记数据本地存储 ✅；但 DeepSeek API 调用会传输对话内容到外部服务，需用户知情 |
| II. 简洁至上 | ✅ | 仅写日记和看日记两个核心功能 |
| III. 离线可用 | ⚠️ 例外 | AI 对话功能必须联网（合理例外），但日记阅读和列表可离线 ✅ |
| IV. 数据主权 | ✅ | 本地存储，用户完全控制数据 |
| V. 微信原生体验 | ✅ | 原生框架 + 同声传译插件 |

**例外说明**:
1. **隐私例外**: DeepSeek API 处理对话内容时数据经过外部服务器。由于用户知情选择该方案且为个人使用，此例外可接受。
2. **离线例外**: AI 语音对话本质依赖云端 AI 服务，无法离线运行。但已保存的日记阅读不受影响。

## Project Structure

### Documentation (this feature)

```text
specs/001-ai-voice-diary/
├── plan.md              # This file
├── research.md          # Phase 0 — 技术调研
├── data-model.md        # Phase 1 — 数据模型
├── quickstart.md        # Phase 1 — 快速开始
├── contracts/           # Phase 1 — 接口约定
└── tasks.md             # (由 /speckit-tasks 生成)
```

### Source Code (repository root)

```text
miniprogram/
├── app.js               # 小程序入口
├── app.json             # 小程序配置
├── app.wxss             # 全局样式
├── pages/
│   ├── index/           # 日记列表页
│   │   ├── index.js
│   │   ├── index.wxml
│   │   ├── index.wxss
│   │   └── index.json
│   ├── diary/           # 日记详情页
│   │   ├── diary.js
│   │   ├── diary.wxml
│   │   ├── diary.wxss
│   │   └── diary.json
│   └── chat/            # AI 对话写日记页
│       ├── chat.js
│       ├── chat.wxml
│       ├── chat.wxss
│       └── chat.json
├── services/
│   ├── storage.js       # 本地存储服务
│   ├── voice.js         # 语音录制 + 识别 + 合成
│   └── ai.js            # DeepSeek API 调用
├── models/
│   └── diary.js         # 日记数据模型
├── utils/
│   └── util.js          # 工具函数
└── components/          # 可复用组件
    └── diary-card/      # 日记卡片组件
```

**Structure Decision**: 标准的微信小程序原生项目结构，按页面分包。`pages/` 存放三个页面，`services/` 封装核心能力层，`models/` 定义数据模型。

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| DeepSeek API 外部数据传输 | AI 对话功能的核心依赖 | 无可替代——对话式 AI 必须调用云端 LLM |

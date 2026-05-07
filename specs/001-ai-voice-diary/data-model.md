# 数据模型：AI 语音日记

## 概述

本应用包含三个核心数据实体：日记条目 (DiaryEntry)、对话会话 (ConversationSession)、对话轮次 (ConversationTurn)。

## 实体关系

```
ConversationSession 1 ──── * ConversationTurn
        │
        │ 生成
        ▼
   DiaryEntry
```

一次对话会话包含多轮交互，结束后生成一篇日记条目。

---

## DiaryEntry (日记条目)

日记是最终保存的内容实体，由 AI 对话整理生成。

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | string | ✅ | UUID，唯一标识 |
| `date` | string | ✅ | 日记日期，格式 `YYYY-MM-DD` |
| `title` | string | ✅ | AI 根据对话内容生成的标题 |
| `content` | string | ✅ | AI 整理后的日记正文（自由叙述摘要） |
| `topic` | string | ❌ | 对话原始话题 |
| `createdAt` | number | ✅ | 创建时间戳 (ms) |
| `updatedAt` | number | ✅ | 最后修改时间戳 (ms) |

**示例**：
```json
{
  "id": "a1b2c3d4-...",
  "date": "2026-05-07",
  "title": "公园散步的午后",
  "content": "今天下午我去公园散步了，天气很好...",
  "topic": "今天做了什么",
  "createdAt": 1715000000000,
  "updatedAt": 1715000000000
}
```

**验证规则**：
- `title` 长度 1-50 字
- `content` 最小 1 字
- `date` 必须有效日期格式

---

## ConversationSession (对话会话)

一次完整的写日记对话过程。

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | string | ✅ | UUID |
| `topic` | string | ❌ | 用户发起的话题 |
| `status` | enum | ✅ | `active` / `ended` |
| `turns` | ConversationTurn[] | ✅ | 对话轮次列表 |
| `startedAt` | number | ✅ | 开始时间戳 |
| `endedAt` | number | ❌ | 结束时间戳 |

**状态流转**：
```
创建会话 → active → 用户说"结束" → ended → 生成日记
```

---

## ConversationTurn (对话轮次)

单次用户-AI 交换。

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `userText` | string | ✅ | 用户语音转文字结果 |
| `aiText` | string | ✅ | AI 回复文本 |
| `userAudio` | string | ❌ | 用户录音临时文件路径 |
| `aiAudio` | string | ❌ | AI 语音合成文件路径 |
| `timestamp` | number | ✅ | 该轮次时间戳 |

---

## 本地存储结构

```
KEY: "diary_entries"
VALUE: DiaryEntry[]  // 全部日记，按 createdAt 降序

KEY: "diary_entry_{id}"
VALUE: DiaryEntry     // 单篇日记（用于快速读取）
```

**读取策略**：
- 列表页：读取 `diary_entries` 键获取所有日记摘要
- 详情页：读取 `diary_entry_{id}` 获取完整内容
- 编辑/删除：操作后同步更新两处

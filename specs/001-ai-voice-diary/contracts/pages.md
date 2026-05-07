# UI 页面接口约定

## 页面路由

| 路径 | 页面 | 说明 |
|------|------|------|
| `pages/index/index` | 日记列表 | 首页 |
| `pages/chat/chat` | AI 对话写日记 | 核心功能页 |
| `pages/diary/diary?id={id}` | 日记详情 | 查看/编辑单篇日记 |

---

## 页面间通信

### 首页 → 对话页 (wx.navigateTo)

```javascript
// 无参数传递，直接跳转
wx.navigateTo({ url: '/pages/chat/chat' })
```

### 对话页 → 首页 (保存后返回)

对话保存后通过 `wx.navigateBack` 返回首页，首页在 `onShow` 中刷新列表。

### 首页 → 详情页

```javascript
wx.navigateTo({ url: '/pages/diary/diary?id=a1b2c3d4-...' })
```

---

## 全局数据 (app.globalData)

```javascript
// app.js
globalData: {
  currentSession: null  // 当前对话会话对象
}
```

---

## 服务层接口 (services/)

### storage.js

```javascript
// 获取所有日记列表
getAllDiaries(): Promise<DiaryEntry[]>

// 获取单篇日记
getDiary(id: string): Promise<DiaryEntry | null>

// 保存日记
saveDiary(entry: DiaryEntry): Promise<void>

// 更新日记
updateDiary(entry: DiaryEntry): Promise<void>

// 删除日记
deleteDiary(id: string): Promise<void>
```

### voice.js

```javascript
// 开始录音并识别（ASR）
startRecordingAndRecognize(): Promise<string>  // 返回识别文字

// 停止录音
stopRecording(): void

// 文字转语音并播放（TTS）
speakText(text: string): Promise<void>

// 停止播放
stopSpeaking(): void
```

### ai.js

```javascript
// 发送消息到 DeepSeek 并获取回复（流式/非流式）
sendMessage(messages: Message[]): Promise<string>

// 生成日记摘要（传入完整对话历史）
generateDiary(messages: Message[]): Promise<string>
```

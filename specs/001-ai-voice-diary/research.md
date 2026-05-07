# 技术调研报告：AI 语音日记

## DeepSeek API 集成方案

### 决策
在小程序中直接通过 `wx.request` 调用 DeepSeek API（开发版跳过域名校验，体验版需配置合法域名）。

### 技术方案
- **API 地址**: `https://api.deepseek.com/v1/chat/completions`
- **鉴权方式**: API Key（配置在代码中，个人使用可接受）
- **模型选择**: `deepseek-chat` (性价比最高)
- **请求格式**: 标准 OpenAI-compatible API
- **上下文管理**: 每次请求携带完整对话历史数组（由前端维护）

### 注意事项
- 开发版：在详情中勾选"不校验合法域名、web-view（业务域名）、TLS 版本及 HTTPS 证书"
- 体验版/正式版：需在微信小程序管理后台配置 `api.deepseek.com` 到合法域名白名单
- API Key 直接写在代码中（个人使用可接受，如需分享代码需注意不要泄露）

## 微信同声传译插件集成

### 决策
使用微信官方的同声传译插件（iSpeech）实现语音识别和语音合成。

### 集成步骤
1. **app.json** 中引入插件：
   ```json
   {
     "plugins": {
       "WechatSI": {
         "version": "0.3.5",
         "provider": "wx069ba97219f66d99"
       }
     }
   }
   ```
2. **语音识别 (ASR)**:
   - 使用 `plugin.login` 获取鉴权
   - 调用 `plugin.recordRecognition` 录制并识别语音
   - 支持普通话，返回识别文本
3. **语音合成 (TTS)**:
   - 调用 `plugin.textToSpeech` 将 AI 回复文字转为语音
   - 返回音频文件 ID，通过 `wx.createInnerAudioContext` 播放

### 限制
- 需要网络连接
- 每天免费额度有限（个人使用完全足够）
- 不支持自定义语音角色

## 对话上下文管理

### 决策
在页面内存中维护对话上下文数组，通过 DeepSeek API 的 `messages` 参数保持多轮对话连贯性。

### 消息结构
```javascript
// messages 数组结构
[
  { role: "system", content: "你是一个日记助手..." },
  { role: "user", content: "今天我去公园散步了" },
  { role: "assistant", content: "听起来很不错！..." },
  { role: "user", content: "还遇到了..." }
]
```

### 对话结束与日记生成
1. 用户说出"结束"语音指令，或内置命令词
2. 系统将完整对话历史发送给 DeepSeek，附带日记整理指令
3. DeepSeek 返回整理后的日记叙述文本
4. 用户确认后保存到本地存储

### System Prompt 设计
```
你是一个温暖的日记助手。用户会和你语音聊天分享日常。
你的任务是：
1. 友好地回应用户，像朋友一样聊天
2. 在对话中自然地引导用户分享更多细节（时间、地点、感受等）
3. 当用户说"结束"时，将本次对话整理成一篇流畅的第一人称日记
回复简洁自然，每次不超过 100 字（便于语音朗读）。
```

## 数据存储方案

### 决策
使用微信本地存储 `wx.setStorageSync` / `wx.getStorageSync`，数据格式为 JSON。

### 存储结构
```
KEY: "diary_list"
VALUE: [
  {
    "id": "uuid",
    "date": "2026-05-07",
    "title": "今日话题",
    "content": "AI 整理的日记正文...",
    "createdAt": 1715000000000,
    "updatedAt": 1715000000000
  }
]
```

### 存储限制
- 微信本地存储上限约 10MB
- 纯文本日记条目，每条约 1-2KB，可存 5000+ 篇
- 超出时提示用户导出或清理

## 项目初始化方案

### 决策
使用微信开发者工具创建标准小程序项目，不使用第三方框架。

### 步骤
1. 在微信开发者工具中创建新项目（AppID 使用测试号或个人的）
2. 选择 JavaScript 基础模板
3. 在 `app.json` 中注册页面和插件
4. 按上述目录结构创建代码文件

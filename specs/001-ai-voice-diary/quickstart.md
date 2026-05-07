# 快速开始：AI 语音日记

## 前置要求

1. 安装 [微信开发者工具](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)
2. 注册微信小程序账号（可使用测试号）
3. 获取 [DeepSeek API Key](https://platform.deepseek.com/)

## 初始化项目

在微信开发者工具中创建新项目：

1. **项目名称**: `diary-mp`
2. **目录**: 本项目根目录 `/miniprogram`
3. **AppID**: 使用测试号或你的小程序 AppID
4. **后端服务**: 选择"不使用云服务"
5. **模板**: JavaScript 基础模板

## 配置步骤

### 1. 配置 app.json

注册页面和引入同声传译插件：

```json
{
  "pages": [
    "pages/index/index",
    "pages/chat/chat",
    "pages/diary/diary"
  ],
  "plugins": {
    "WechatSI": {
      "version": "0.3.5",
      "provider": "wx069ba97219f66d99"
    }
  },
  "window": {
    "navigationBarTitleText": "语音日记"
  }
}
```

### 2. 配置 DeepSeek API Key

在 `services/ai.js` 中配置：

```javascript
const DEEPSEEK_API_KEY = 'sk-your-key-here'
const DEEPSEEK_API_URL = 'https://api.deepseek.com/v1/chat/completions'
```

### 3. 开启开发调试

在微信开发者工具中：
- 详情 → 本地设置 → 勾选"不校验合法域名..."
- 以保证开发阶段可以直接调用 DeepSeek API

## 运行

在微信开发者工具中点击"编译"即可预览。使用真机调试可测试语音功能。

## 项目结构

```
miniprogram/
├── app.js / app.json / app.wxss     # 小程序入口
├── pages/
│   ├── index/    # 日记列表（首页）
│   ├── chat/     # AI 对话写日记
│   └── diary/    # 日记详情/编辑
├── services/
│   ├── storage.js  # 本地存储
│   ├── voice.js    # 语音识别 + 合成
│   └── ai.js       # DeepSeek API
├── models/
│   └── diary.js    # 数据模型
└── utils/
    └── util.js     # 工具函数
```

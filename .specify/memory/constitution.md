<!--
  Sync Impact Report:
  - Version: 0.1.0 (initial)
  - Added: 5 core principles, 3 additional sections, governance
  - Templates: plan-template.md ✅ no changes needed, spec-template.md ✅ no changes needed, tasks-template.md ✅ no changes needed
  - No deferred items
-->
# 日记本 Constitution

## Core Principles

### I. 隐私优先（NON-NEGOTIABLE）
日记内容属于用户隐私。所有日记数据 MUST 在本地加密存储，上传到云端的数据 MUST 使用端到端加密。不得以任何形式将用户日记内容用于训练、分析或分享给第三方。用户认证 MUST 使用微信登录，不额外收集手机号、邮箱等个人信息。

### II. 简洁至上
应用的核心价值是"快速记录想法"。每次打开应用 SHOULD 在 3 秒内进入可书写状态。功能不堆砌——只做写日记、看日记、搜日记三件事。新增任何功能 MUST 先质疑"是否偏离了记录想法的核心场景"。

### III. 离线可用
用户 MUST 能在无网络环境下正常写日记。本地数据为主要存储，云端仅为同步备份。写入操作 MUST 先写入本地缓存再异步同步到云端，确保用户不会因为网络问题丢失内容。

### IV. 数据主权
用户对自己的日记数据拥有完全控制权。应用 MUST 提供一键导出所有日记的功能（支持 Markdown / JSON 格式）。用户注销时 MUST 提供清除所有云端数据的选项。

### V. 微信原生体验
遵循《微信小程序设计指南》，使用 WeUI 组件库保持一致体验。小程序包体积 MUST 控制在 2MB 以内，首屏加载时间不超过 1.5 秒。充分利用微信原生能力（登录、分享、订阅消息），避免引入不必要的第三方依赖。

## 技术约束

**前端框架**：微信小程序原生框架（避免引入 UniApp / Taro 等跨端框架，保持最小包体积）  
**后端服务**：微信云开发（云数据库 + 云函数 + 云存储）  
**数据存储**：本地使用 wx.getStorage / wx.setStorage 缓存，云数据库作为远程存储  
**加密方案**：本地存储使用 AES 加密，传输使用 HTTPS  
**工具链**：使用微信开发者工具 + ESLint 做代码规范检查

## 开发流程

1. 每个功能开发前 MUST 先编写 spec 文档
2. 遵循 TDD：测试先行（或手动测试用例先行）→ 实现 → 验证
3. 每个 MR / commit MUST 包含对应的测试或验证步骤
4. 所有代码提交前 MUST 通过 ESLint 检查
5. 云函数部署 MUST 经过本地测试后再发布

## Governance

本文档是项目的最高准则。任何原则的增删改 MUST 提交 Constitution 变更说明并在团队内达成一致。版本号遵循语义化版本规范。

**非功能性需求（性能、隐私、安全）的例外申请 MUST 在技术方案中明确说明理由和替代方案。**

**Version**: 0.1.0 | **Ratified**: 2026-05-07 | **Last Amended**: 2026-05-07

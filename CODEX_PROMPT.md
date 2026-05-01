# Prompt for Codex — Phase 1a Build

> 以下是发给 Codex 的 prompt。复制全文发送即可。

---

你好，请帮我构建这个项目的 Phase 1a（核心对话 MVP）。

## 在开始写代码之前，请先读这三份文档：

1. **PRODUCT_SPEC.md** — 完整的产品规格，包含用户画像、核心流程、象长老的对话规则、安全边界、建议的文件结构、Phase 1a 开发清单
2. **LEARNING_STACK.md** — 我要用的技术栈清单（Next.js、Tailwind CSS 等），请严格按照这里列的技术来构建
3. **README.md** — 产品概述和开发阶段规划

## Phase 1a 要做的事（按优先级）：

### 1. 首页
- 产品介绍页面，展示象长老形象
- 一个主按钮："跟象长老说说话"
- 手机优先设计（移动端布局）
- 使用 PRODUCT_SPEC.md 第七节的色彩和字体

### 2. 对话页面（核心）
- 聊天气泡式 UI，孩子在右边，象长老在左边
- 孩子可以打字输入
- 孩子可以语音输入（浏览器 Web Speech API / SpeechRecognition，如果浏览器不支持就只显示文字输入）
- 调用 Claude API 作为象长老的对话引擎：
  - 通过 `/api/chat` 路由代理（API Key 不暴露给前端）
  - 使用流式响应（Streaming），让象长老的话逐字出现
  - System Prompt 请严格按照 PRODUCT_SPEC.md 第四节"象长老的角色设定"来写，包括必须做的、绝对不能做的、安全检测触发词
- 3-5 轮对话后，象长老主动提议："要不要让象长老帮你把刚才说的话，整理成一张话卡给妈妈看？"
- 基础安全检测：检测到自伤/暴力关键词时切换到安全回应模式（见 PRODUCT_SPEC.md 第四节）

### 3. 话卡生成
- 对话结束后，用 LLM 生成话卡内容，固定三段结构：
  - 象长老听到的（孩子的感受）
  - 孩子真正想说的（核心诉求）
  - 给爸爸/妈妈的话（一个回应台阶）
- 话卡渲染为好看的卡片组件
- 孩子可以预览话卡
- 把话卡渲染为可分享的图片（用 html2canvas 或类似方案）

### 4. 分享
- 使用 Web Share API（navigator.share），让孩子可以把话卡图片分享到微信、AirDrop 等
- 如果浏览器不支持 Web Share API，提供"保存图片"按钮作为降级方案

### 5. 象长老语音（TTS）
- 象长老的文字同时用 TTS 朗读出来
- 通过 `/api/tts` 路由代理调用火山引擎 TTS
- TTS 失败时不阻塞流程，降级到浏览器 speechSynthesis
- TTS 的逻辑可以参考旧项目 https://github.com/AmyX2025/elephant-elder-court 的 `server.js`

### 6. 部署
- 确保项目能在 Vercel 上正常部署
- 环境变量参考 `.env.example`

## 从旧项目迁移的资产

旧项目：https://github.com/AmyX2025/elephant-elder-court

请从旧项目迁移以下内容（详见 PRODUCT_SPEC.md 第五节"从旧项目迁移的资产"表格）：
- 象长老的 SVG 形象 → 转为 React 组件 `ElephantAvatar.tsx`
- CSS 色彩变量 → `theme.css` 或 Tailwind 配置
- TTS 火山引擎/MiniMax 逻辑 → `lib/tts.ts`
- 语音输入逻辑 → `VoiceInput.tsx` 组件

## Phase 1a 不要做的（留给后续）：
- ❌ 不做用户注册登录
- ❌ 不做数据库
- ❌ 不做家长端
- ❌ 不做音乐生成（Phase 1b）
- ❌ 不做邮件通知
- ❌ 不做人机验证
- ❌ 不做 UI 美化（21st.dev）
- ❌ 不做管理后台

## 技术要求：
- Next.js App Router + TypeScript + Tailwind CSS
- 所有 API 密钥通过环境变量管理，绝不硬编码
- 移动端优先的响应式设计
- 代码结构清晰，按 PRODUCT_SPEC.md 建议的文件结构组织

请开始构建，先从项目结构和核心对话功能开始。

# 象长老 — Elephant Elder Listener

> 帮孩子把说不出口的话，用大人听得进去的方式说出来。

## 这是什么产品？

象长老是一个面向儿童的 AI 倾听工具。当孩子在家里觉得委屈、不被理解、想找人说话时，可以打开这个 App，跟象长老倾诉。

象长老会耐心听孩子说完，引导孩子理清情绪，然后帮孩子把想说的话整理成一张"话卡"——用大人听得进去的方式，替孩子传达心声。

### 核心流程

```
孩子打开 App
  → 跟象长老说话（语音或打字）
  → 象长老引导提问，帮孩子理清感受
  → 生成"话卡"（去情绪化的表达）
  → 孩子预览、编辑
  → 选择是否分享给爸妈
  → 可选：生成一首表达心情的音乐
```

### 为什么做这个？

- 亲子冲突中孩子是弱势方，没有"公正的第三方"帮他们说话
- 市面上的儿童情绪 App 都是"教孩子认识情绪"，没有一个是"听孩子说话、帮孩子表达"
- 孩子长期"没人听"会压抑，沟通桥梁断掉时可能陷入孤立
- 象长老不是法官，不裁定谁对谁错——只是帮孩子把委屈翻译成大人能听进去的话

### 产品演进

本项目从 [elephant-elder-court](https://github.com/AmyX2025/elephant-elder-court)（亲子调解庭 MVP）pivot 而来。第一版验证了仪式感和交互流程，但发现"双方同时参与调解"的假设不成立。新方向聚焦于孩子单方可用的倾听体验。

## 开发阶段

### Phase 1a — 核心对话 MVP
- [ ] 孩子和象长老的 AI 对话（Claude API）
- [ ] 象长老引导式提问（帮孩子从情绪变成表达）
- [ ] 生成话卡（去情绪化、保留核心诉求）
- [ ] 话卡预览和分享（生成图片 → 系统分享）
- [ ] 语音合成（象长老的声音，火山引擎 TTS）
- [ ] 部署到 Vercel

### Phase 1b — 音乐表达
- [ ] MiniMax Music 2.6 集成
- [ ] 孩子可选"把心情变成一首音乐"
- [ ] 音乐播放和分享

### Phase 2 — 用户体系 + 数据持久化
- [ ] 邮箱注册登录（密码哈希 bcrypt）
- [ ] Neon PostgreSQL 数据库
- [ ] 对话记录和话卡存储
- [ ] 家长端查看孩子的话卡历史
- [ ] Cloudflare R2 云存储（图片 + 音乐）

### Phase 3 — 通知 + 安全
- [ ] Resend + React Email 邮件通知家长
- [ ] Cloudflare Turnstile 人机验证
- [ ] 儿童安全检测（自伤/暴力内容拦截）

### Phase 4 — 打磨
- [ ] 21st.dev UI 美化
- [ ] 自定义域名
- [ ] 管理后台

## 技术栈

```
前端：      Next.js (React) + Tailwind CSS
后端：      Next.js API Routes
数据库：    Neon (PostgreSQL)
认证：      NextAuth.js (Auth.js) + bcrypt
云存储：    Cloudflare R2
人机验证：  Cloudflare Turnstile
邮件：      Resend + React Email
AI 对话：   Claude API (Anthropic)
语音合成：  火山引擎 / MiniMax TTS
音乐生成：  MiniMax Music 2.6
UI 组件：   21st.dev
部署：      Vercel
数据库管理：TablePlus
```

## 本地开发

```bash
npm install
cp .env.example .env.local
# 填入 API keys
npm run dev
```

打开 http://localhost:3000

## 安全原则（儿童产品红线）

- 不做心理诊断，不说"你妈妈错了"
- 不鼓励孩子对抗/隐瞒家长
- 不承诺保密——涉及自伤、家暴时必须引导找真人
- 不收集不必要的个人信息
- 鼓励孩子连接现实中可信赖的成年人
- 家长可查看对话记录（透明性是信任基础）

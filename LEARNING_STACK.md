# 学习技术栈清单

> 本文档是我（Amy）在 AI 编程课程中学到的技术知识点汇总。
> 每次做新项目时，请 AI agent 先读这份文档，按照这里列出的技术栈来构建，不需要我重复解释。
> 每个知识点标注了我希望通过实践理解的内容。

---

## 给 AI Agent 的使用说明

当你帮我构建项目时，请遵循以下原则：
1. **优先使用本文档列出的技术栈**，除非有明确的技术原因不适合
2. 每个技术的选择都有学习目的，**不要替换成"更简单的替代方案"**，除非我主动要求
3. 如果某个技术不适合当前项目，请告诉我原因，并建议替代方案
4. **分阶段实施**：先跑通核心功能（MVP），再逐步加入更多技术栈
5. 每次用到一个新技术时，请用简单的话解释它在做什么、为什么这样做

---

## 1. 全栈框架：Next.js

**是什么**：基于 React 的全栈框架，前端 + 后端 API 在一个项目里。React 是 Next.js 的 UI 层——用 Next.js 就自动在用 React。

**我要通过实践掌握的**：
- App Router（文件系统路由：文件夹结构 = URL 结构）
- Server Components vs Client Components（什么时候用 "use client"）
- API Routes（`app/api/*/route.ts`，后端逻辑写在这里）
- 环境变量管理（`.env.local`，API 密钥放这里，不提交到 GitHub）

**部署到**：Vercel（Next.js 的最佳部署平台）

---

## 2. 用户认证：NextAuth.js (Auth.js)

**是什么**：Next.js 的认证框架，帮你处理注册、登录、会话管理。

**我要通过实践掌握的**：
- 邮箱 + 密码注册登录的完整流程
- **密码哈希（bcrypt）**：用户注册时输入密码 → bcrypt 把它变成一串不可逆的乱码 → 存入数据库。登录时再用 bcrypt 对比，而不是还原密码。所以即使数据库泄露，攻击者也拿不到真实密码。我作为后台管理者也看不到用户的密码。
- Session / JWT（登录后怎么保持"已登录"状态）
- 完整链路：注册 → 哈希 → 存库 → 登录 → 验证 → 发会话 → 保持状态

**未来可选**：微信扫码登录（需要企业资质，暂不做）

---

## 3. 数据库：Neon (PostgreSQL)

**是什么**：Serverless 云端 PostgreSQL 数据库，和 Vercel 天然配合，有免费额度。

**我要通过实践掌握的**：
- SQL 基础操作（CREATE TABLE, INSERT, SELECT, UPDATE, DELETE）
- 数据库 Schema 设计（用户表、对话表、话卡表、怎么关联）
- 从 Next.js API Routes 连接和操作数据库
- ORM 工具（Prisma 或 Drizzle，把 SQL 操作变成更好写的代码）

**管理工具**：**TablePlus** — 可视化界面直接查看和管理数据库内容，比命令行直观。

**学习目标**：理解数据如何在"用户操作 → API → 数据库 → 页面展示"之间流动。

---

## 4. 云存储：Cloudflare R2

**是什么**：对象存储服务（类似 AWS S3），用来存图片、音频等文件，有免费额度。

**我要通过实践掌握的**：
- 文件上传 API（把图片/音频存到云端）
- 生成公开访问链接（让用户能看到/下载）
- 存储管理（什么时候存、存多久）

**学习目标**：理解"文件上传 → 云存储 → 返回 URL → 页面展示/下载"的流程。

---

## 5. 人机验证：Cloudflare Turnstile

**是什么**：比 Google reCAPTCHA 更轻量的人机验证，防止机器人滥用。

**我要通过实践掌握的**：
- 前端嵌入验证组件（用户看到一个小挑战）
- 后端验证 token（确认这个请求来自真人）
- 前后端配合验证的完整流程

**学习目标**：理解为什么需要人机验证，以及它是怎么前后端配合工作的。

---

## 6. 邮件服务：Resend + React Email

**Resend**：发送邮件的 API 服务（面向开发者，海外产品常用）。
**React Email**：用 React 组件写邮件模板，比传统 HTML 邮件好写得多。

**我要通过实践掌握的**：
- Resend API 调用（用代码发邮件）
- 自定义域名发件（邮件从我的产品域名发出，有品牌感）
- React Email 模板编写（漂亮的邮件样式）
- DNS 配置（让域名能发邮件）

**学习目标**：理解"产品如何主动联系用户"——不是用户来找你，而是你去找用户。

---

## 7. UI 设计增强：21st.dev

**是什么**：提供高质量的 React/Next.js UI 组件和模板。

**使用方式**：核心功能跑通后，挑选免费组件/模板来美化页面。相当于"给产品换一件好看的衣服"。

**优先级**：放在后期。先让功能跑通，再追求设计感。

---

## 8. AI / LLM 集成

| 能力 | 服务 | 用途 |
|------|------|------|
| AI 对话 | Claude API (Anthropic) | 驱动象长老和孩子的对话引擎 |
| 语音合成 | 火山引擎 / MiniMax TTS | 象长老的声音 |
| 音乐生成 | MiniMax Music 2.6 | 让孩子把心情变成一首音乐 |

**我要通过实践掌握的**：
- API Key 安全管理（环境变量，绝对不提交到 GitHub）
- Prompt Engineering（设计象长老的对话引导策略和安全边界）
- 流式响应处理（Streaming，让对话像"正在打字"一样逐字出现）

---

## 9. 部署：Vercel

**流程**：GitHub push → Vercel 自动检测 → 构建 → 部署 → 上线

**我要通过实践掌握的**：
- 连接 GitHub 仓库到 Vercel
- 在 Vercel 配置环境变量（线上的 API Key）
- 自定义域名绑定

---

## 10. 域名

- **查询工具**：Instant Domain Search（instantdomainsearch.com）
- **要求**：找一个好听的、可注册的、和产品相关的域名
- **配置**：绑定 Vercel + 配置 Resend 邮件发送的 DNS 记录

---

## 11. 版本管理：GitHub

**我要通过实践掌握的**：
- git 基本操作（add, commit, push, branch）
- 写好 README（让别人看得懂项目）
- .gitignore 管理（密钥和环境变量永远不提交）
- 项目结构组织

---

## 技术栈速查表

```
前端框架：  Next.js (React) + Tailwind CSS
后端：      Next.js API Routes
数据库：    Neon (PostgreSQL) + Prisma/Drizzle
认证：      NextAuth.js (Auth.js) + bcrypt 密码哈希
云存储：    Cloudflare R2
人机验证：  Cloudflare Turnstile
邮件：      Resend + React Email
AI 对话：   Claude API (Anthropic)
语音合成：  火山引擎 / MiniMax TTS
音乐生成：  MiniMax Music 2.6
UI 美化：   21st.dev
部署：      Vercel
域名查询：  Instant Domain Search
数据库管理：TablePlus
版本管理：  GitHub
```

---

## 更新日志

- 2026-05-01：初始版本，基于 AI 编程课程内容整理

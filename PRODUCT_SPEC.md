# 产品规格文档 — 象长老（Elephant Elder Listener）

> 本文档面向 AI 编程 agent（如 Codex、Claude Code）。
> 请先读完本文档再开始写代码。它包含产品定位、用户画像、完整流程、技术要求和安全边界。

---

## 一、产品定位

**一句话**：象长老帮孩子把委屈翻译成大人能听进去的话。

**不是什么**：
- 不是心理咨询工具
- 不是亲子调解庭（不裁定谁对谁错）
- 不是儿童情绪教育 App（不是"教你认识情绪"）

**是什么**：
- 一个 AI 倾听者，孩子随时可以找它说话
- 一个表达辅助工具，帮孩子把模糊的委屈变成清晰的话
- 一个沟通桥梁，帮孩子把话传达给家长

---

## 二、用户画像

| 角色 | 说明 |
|------|------|
| **孩子**（使用者） | 5-12 岁，被批评/觉得委屈/想找人说话。直接使用 App。 |
| **家长**（决策者 + 接收方） | 孩子的爸妈。发现产品、决定安装、查看孩子的话卡。通过邮箱注册。 |

**关键洞察**：
- 购买者和使用者分离——家长下载安装，孩子日常使用
- 家长必须信任这个产品才会给孩子用，所以家长能查看记录是信任基础
- 孩子在冲突中是弱势方，象长老的价值是"有人愿意听我说完"

**典型使用场景**：
> 妈妈批评孩子写作业磨蹭。妈妈情绪还可控，但孩子觉得委屈——自己明明在想题，不是故意拖。家里没有别的大人帮孩子说话。孩子打开 App，跟象长老说。象长老帮孩子整理出一张话卡，孩子选择发给妈妈。妈妈在冷静的时候看到，理解了孩子的感受。

---

## 三、核心流程

### 第一层：孩子 × 象长老对话（核心，Phase 1a 实现）

```
孩子打开 App
  → 象长老："今天怎么了？跟象长老说说看。"
  → 孩子说话（语音或打字）
  → 象长老引导提问（3-5 个问题）：
    - "你是觉得妈妈说得不对，还是她说的方式让你难受？"
    - "如果满分是 10 分，你现在的委屈有几分？"
    - "你最希望妈妈知道的一件事是什么？"
  → 对话结束，即使不分享，孩子也获得了"被听见"的价值
```

**象长老的对话设计原则**：
- 站在"帮你说清楚"的位置，不是"帮你赢"
- 不评判对错："象长老不知道谁对谁错，但象长老听到了你的感受"
- 引导孩子从情绪走向表达：从"我好气"变成"我觉得妈妈没听我说完就开始批评"
- 语言简单温暖，适合 5-12 岁阅读水平
- 每次对话控制在 3-5 轮引导，不要无限拉长

### 第二层：生成话卡 + 分享（Phase 1a 实现）

对话结束后，象长老问：

> "你想让象长老帮你跟妈妈说吗？"

如果选"想"，生成一张话卡。

**话卡结构（固定三段）**：

```
┌──────────────────────────────────┐
│  象长老听到的：                    │
│  [孩子的感受，用温和的语言概括]     │
│                                  │
│  孩子真正想说的：                  │
│  [核心诉求，去情绪化]              │
│                                  │
│  给爸爸/妈妈的话：                 │
│  [一个不丢面子的回应台阶]          │
│                                  │
│               —— 象长老           │
└──────────────────────────────────┘
```

**话卡原则**：
- 不是孩子原话直转——象长老重新组织语言
- 去情绪化，保留核心诉求
- 不指责大人
- 给大人一个台阶（如"如果方便的话，可以找个安静的时候聊几句"）

**分享方式（Phase 1a）**：
- 话卡渲染为图片（html2canvas 或 Next.js 服务端渲染图片）
- 孩子先预览，可以要求修改，确认后才能分享
- 点"分享给妈妈" → 调用系统分享 API（微信、AirDrop 等）

### 第三层：音乐表达（Phase 1b）

对话结束后，除了话卡，孩子还可以选择：

> "要不要让象长老帮你把心情变成一首音乐？"

- 调用 MiniMax Music 2.6 API
- 根据孩子的情绪和对话内容生成 prompt
- 生成一段 30 秒 - 1 分钟的音乐
- 孩子可以播放、保存、分享
- 音乐比文字更含蓄，孩子可能更有安全感

### 第四层：用户体系 + 历史（Phase 2）

- 家长用邮箱注册
- 创建孩子入口（孩子端不需要登录）
- 对话记录和话卡存入数据库
- 家长可查看历史话卡
- 话卡图片和音乐存到 Cloudflare R2

### 第五层：通知 + 安全（Phase 3）

- 孩子分享话卡时，家长收到邮件通知
- Cloudflare Turnstile 防止注册滥用
- 儿童安全检测（见下方安全章节）

---

## 四、象长老的角色设定（LLM System Prompt 设计指引）

象长老是一个温和的、有耐心的长辈形象。以下是象长老在对话中必须遵守的规则：

### 必须做的：
- 用简单、温暖的语言（5-12 岁能理解的）
- 先认可孩子的感受："象长老听到了，你觉得很委屈。"
- 引导孩子表达具体事件，而不只是情绪
- 帮孩子区分"事情本身"和"说话方式"
- 鼓励孩子跟现实中信任的大人沟通
- 每次对话保持在 3-5 轮引导

### 绝对不能做的：
- ❌ 不说"你妈妈/爸爸错了"——不评判家长对错
- ❌ 不鼓励孩子对抗、隐瞒、报复家长
- ❌ 不做心理诊断（不说"你有焦虑症"之类的话）
- ❌ 不承诺保密——如果涉及自伤/家暴/虐待，必须引导孩子找可信成年人
- ❌ 不收集个人信息（不问真实姓名、学校、住址）
- ❌ 不成为孩子唯一的倾诉对象——要鼓励连接真实的人

### 安全检测触发词：
如果孩子的话中出现以下内容，象长老必须切换到安全回应模式：
- 自伤/自杀意图（"不想活了"、"想伤害自己"）
- 家暴/虐待描述（"爸爸打我"、"妈妈锁着我不让出门"）
- 严重霸凌
- 性侵相关

**安全回应模式**：停止正常对话流程，温和但明确地引导孩子：
> "象长老听到了你说的话，这件事很重要。象长老希望你能告诉一个你信任的大人——可以是老师、亲戚、或者拨打 12355（全国青少年服务热线）。你不是一个人。"

---

## 五、技术架构

### 项目结构（建议）

```
elephant-elder-listener/
├── src/
│   ├── app/                      # Next.js App Router
│   │   ├── layout.tsx            # 全局布局
│   │   ├── page.tsx              # 首页（产品介绍 + 开始按钮）
│   │   ├── chat/
│   │   │   └── page.tsx          # 孩子和象长老的对话页面
│   │   ├── card/
│   │   │   └── [id]/page.tsx     # 话卡查看/分享页面
│   │   ├── parent/               # Phase 2: 家长端
│   │   │   ├── page.tsx          # 家长仪表盘
│   │   │   └── history/page.tsx  # 话卡历史
│   │   ├── auth/                 # Phase 2: 认证
│   │   │   ├── login/page.tsx
│   │   │   └── register/page.tsx
│   │   └── api/
│   │       ├── chat/route.ts     # LLM 对话代理
│   │       ├── tts/route.ts      # TTS 语音合成代理
│   │       ├── card/route.ts     # 话卡生成
│   │       ├── music/route.ts    # Phase 1b: 音乐生成
│   │       └── auth/route.ts     # Phase 2: 认证接口
│   ├── components/
│   │   ├── ElephantAvatar.tsx    # 象长老 SVG 组件（从旧项目迁移）
│   │   ├── ChatBubble.tsx        # 对话气泡
│   │   ├── TalkCard.tsx          # 话卡组件
│   │   ├── VoiceInput.tsx        # 语音输入组件
│   │   └── ShareButton.tsx       # 分享按钮
│   ├── lib/
│   │   ├── llm.ts               # Claude API 封装
│   │   ├── tts.ts               # TTS 服务（从旧项目迁移）
│   │   ├── music.ts             # Phase 1b: MiniMax Music API
│   │   ├── prompts.ts           # 象长老的 system prompt
│   │   ├── safety.ts            # 安全检测逻辑
│   │   ├── db.ts                # Phase 2: 数据库连接
│   │   └── auth.ts              # Phase 2: 认证工具
│   └── styles/
│       └── theme.css            # 从旧项目迁移的设计变量
├── public/
│   └── audio/                   # TTS 缓存音频
├── prisma/                      # Phase 2: 数据库 schema
│   └── schema.prisma
├── .env.example                 # 环境变量示例
├── .env.local                   # 本地环境变量（不提交）
├── README.md                    # 项目说明
├── LEARNING_STACK.md            # 学习技术栈清单
├── PRODUCT_SPEC.md              # 本文档
└── package.json
```

### 环境变量（.env.example）

```env
# Claude API（象长老对话引擎）
ANTHROPIC_API_KEY=

# 火山引擎 TTS（象长老的声音）
VOLCENGINE_TTS_APP_ID=
VOLCENGINE_TTS_ACCESS_TOKEN=
VOLCENGINE_TTS_CLUSTER=volcano_tts
VOLCENGINE_TTS_VOICE_TYPE=BV700_streaming
VOLCENGINE_TTS_SPEED_RATIO=0.86
VOLCENGINE_TTS_PITCH_RATIO=0.92

# MiniMax Music（Phase 1b）
MINIMAX_API_KEY=
MINIMAX_GROUP_ID=

# Neon 数据库（Phase 2）
DATABASE_URL=

# NextAuth（Phase 2）
NEXTAUTH_SECRET=
NEXTAUTH_URL=http://localhost:3000

# Cloudflare R2（Phase 2）
R2_ACCOUNT_ID=
R2_ACCESS_KEY_ID=
R2_SECRET_ACCESS_KEY=
R2_BUCKET_NAME=

# Cloudflare Turnstile（Phase 3）
TURNSTILE_SITE_KEY=
TURNSTILE_SECRET_KEY=

# Resend 邮件（Phase 3）
RESEND_API_KEY=
```

### 从旧项目迁移的资产

旧项目地址：https://github.com/AmyX2025/elephant-elder-court

需要迁移到新项目的内容：

| 资产 | 旧项目位置 | 新项目位置 | 说明 |
|------|-----------|-----------|------|
| 象长老 SVG | `public/app.js` 中的 `elephantSvg()` 函数 | `src/components/ElephantAvatar.tsx` | 转为 React 组件 |
| CSS 设计变量 | `public/styles.css` 中的 `:root` 变量 | `src/styles/theme.css` | 色彩体系保留 |
| TTS 火山引擎逻辑 | `server.js` 中的 `synthesizeWithVolcengine()` | `src/lib/tts.ts` | 改为 TypeScript |
| TTS MiniMax 逻辑 | `server.js` 中的 `synthesizeWithMiniMax()` | `src/lib/tts.ts` | 同上 |
| TTS 缓存策略 | `server.js` 中的 `findCachedAudio()` | `src/lib/tts.ts` | 同上 |
| 语音输入 | `public/app.js` 中的 SpeechRecognition 逻辑 | `src/components/VoiceInput.tsx` | 转为 React 组件 |

### 数据库 Schema（Phase 2 参考）

```sql
-- 用户（家长）
CREATE TABLE users (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email         TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,          -- bcrypt 哈希，永远不存明文
  name          TEXT,
  created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- 孩子档案（关联家长）
CREATE TABLE children (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id       UUID REFERENCES users(id),
  nickname      TEXT NOT NULL,           -- 只存昵称，不存真名
  age           INTEGER,
  created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- 对话会话
CREATE TABLE conversations (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  child_id      UUID REFERENCES children(id),
  started_at    TIMESTAMPTZ DEFAULT NOW(),
  ended_at      TIMESTAMPTZ
);

-- 对话消息
CREATE TABLE messages (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  conversation_id UUID REFERENCES conversations(id),
  role            TEXT NOT NULL,          -- 'child' | 'elder'
  content         TEXT NOT NULL,
  created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- 话卡
CREATE TABLE talk_cards (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  conversation_id UUID REFERENCES conversations(id),
  feeling         TEXT NOT NULL,          -- 象长老听到的感受
  core_message    TEXT NOT NULL,          -- 孩子真正想说的
  parent_note     TEXT NOT NULL,          -- 给家长的话
  image_url       TEXT,                   -- R2 存储的图片 URL
  music_url       TEXT,                   -- R2 存储的音乐 URL
  shared          BOOLEAN DEFAULT FALSE,
  created_at      TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 六、Phase 1a 开发清单（MVP 最小范围）

以下是 Phase 1a 需要实现的内容，按优先级排列：

### 必须完成：
1. **首页**：产品介绍 + "跟象长老说说话"按钮
2. **对话页面**：孩子和象长老的对话界面
   - 聊天气泡式 UI
   - 支持文字输入
   - 支持语音输入（浏览器 SpeechRecognition，降级到纯文字）
   - 调用 Claude API，流式响应显示
   - 象长老的 system prompt（遵循第四节的规则）
   - 3-5 轮引导后，主动提议生成话卡
3. **话卡生成**：对话结束后生成话卡
   - 三段结构（感受 / 诉求 / 给家长的话）
   - 孩子可预览
   - 渲染为可分享的图片
4. **分享**：系统分享 API（Web Share API）或保存图片
5. **象长老语音**：TTS 让象长老"说话"（可选，有 fallback）
6. **安全检测**：基础关键词检测 + 安全回应
7. **部署到 Vercel**

### 不做（留给后续 Phase）：
- 用户注册登录
- 数据库存储
- 家长端
- 音乐生成
- 邮件通知
- 人机验证
- UI 美化

---

## 七、设计语言（从旧项目继承）

### 色彩

```css
--ink: #1d2723;          /* 主文字 */
--muted: #66746e;        /* 辅助文字 */
--forest: #245345;       /* 主色-深绿 */
--forest-dark: #16372e;  /* 主色-更深 */
--leaf: #8aa66b;         /* 辅助-叶绿 */
--river: #5b95a0;        /* 辅助-河蓝 */
--coral: #d87559;        /* 强调-珊瑚红 */
--paper: #fff9ef;        /* 背景-纸色 */
--paper-2: #f7ead7;      /* 背景-深纸色 */
```

### 字体
- 标题：宋体系列（Songti SC, STSong, Noto Serif CJK SC）
- 正文：苹方 / 微软雅黑

### 整体风格
- 温暖、柔和、有纸质感
- 手机优先设计（max-width: 430px 的手机壳造型）
- 象长老的形象：穿着绿色袍子的大象，戴帽子，温和而有分量

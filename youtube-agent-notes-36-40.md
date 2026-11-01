# YouTube Agent 技巧视频笔记 · 第 36–40 条

> 核验型笔记第三批之第二组（TypeScript 栈与能力型 agent）。取材标准：字幕、时间戳、代码原文三样齐全；播放器逐章核验，配套仓库克隆对照，能跑的代码实际跑过；看不清处标「不可读」，推断与原文分开标注。

## 目录

- [36. Build a Support Agent with Vercel AI SDK – Full Tutorial](#36-build-a-support-agent-with-vercel-ai-sdk--full-tutorial)
- [37. How to build a JARVIS AI Voice Agent for FREE | Full Livekit Tutorial (2026)](#37-how-to-build-a-jarvis-ai-voice-agent-for-free--full-livekit-tutorial-2026)
- [38. Browser Use: This New AI Agent Can Do Anything (Full AI Scraping Tutorial)](#38-browser-use-this-new-ai-agent-can-do-anything-full-ai-scraping-tutorial)
- [39. A2A vs MCP Explained: The A2A Protocol v1.0, Step by Step (Real Messages)](#39-a2a-vs-mcp-explained-the-a2a-protocol-v10-step-by-step-real-messages)
- [40. Automated Prompt Engineering with DSPy](#40-automated-prompt-engineering-with-dspy)

---

## 36. Build a Support Agent with Vercel AI SDK – Full Tutorial

### 一、元信息

- 标题：Build a Support Agent with Vercel AI SDK – Full Tutorial（页面原样）
- 链接：https://www.youtube.com/watch?v=WKIjkxxNH0c
- 频道：freeCodeCamp.org（页面显示 1,200 万订阅）；授课者 Mayo Oshin（片头幻灯片原样，Scrimba 讲师、《Learning LangChain》合著者）
- 时长：1:54:49（播放器实测）
- 发布时间：2025-12-24
- 观看量：32,407 次；点赞 830
- 配套代码：无 GitHub 仓库，代码在 Scrimba 交互课内完成（简介链接跳转目标核对为 https://scrimba.com/build-a-support-agent-with-vercel-ai-sdk-c0lk05ahir ；课程页为 JS 渲染，页面正文未抓取实测）
- 核验档：A 档（播放器逐章扫 + 版本案头核查；无仓库可克隆）
- 章节（简介原样，共 16）：0:00:00 Customer Support AI Agent Intro；0:06:47 Retrieval Augmented Generation Refresher；0:10:05 Embeddings Recap；0:12:57 Vector Database Embed Documents；0:20:19 Vector Database Retrieval；0:28:00 Challenge: Vector Database Retrieval；0:32:51 Vector Database Text Splitting and Retrieval；0:40:34 Vercel AI SDK Basics；0:43:51 Challenge: Vercel AI SDK Basics；0:47:22 Vercel AI SDK Structured Outputs；0:52:38 Challenge: Vercel AI SDK Structured Outputs；0:58:39 Vercel AI SDK Tool Calling；1:08:37 Challenge: Vercel AI SDK Tool Calling；1:18:04 Agentic Retrieval Routing；1:31:52 Challenge: Agentic Retrieval Routing；1:35:08 Web Search AI Agent

### 二、一句话结论与摘要

这是一门把 RAG 从零搭到「会自己判断要不要检索」的完整实作课：前半程先用裸 OpenAI API 把 embedding、向量库、切分、检索全部手写一遍，后半程再逐个换成 Vercel AI SDK 的 generateText / generateObject / tool，最后把检索本身包成一个工具，与网页搜索并列交给模型路由。

- 课程结构是刻意的对照实验：同一件事先用 provider 原生写法做（openai.embeddings.create、responses.create），再用 SDK 的 embed / generateText 重做，让人看清 SDK 抽象到底省了什么。
- 最有价值的设计在第 14 章：先分类（retrieval / general）再检索，分类调用只给 20 个输出 token、temperature 0；第 15 章的挑战又把这个分类改造成 generateObject 结构化输出——同一个路由点，两种实现，正好对照。
- 终局形态（第 16 章）不是「分类器 + 两条管线」，而是把知识库检索封装成 knowledge-base tool，与 web_search_preview 并列，靠 stopWhen 多步循环让模型自己选工具——分类从显式代码变成了工具选择。
- 时效性（截至 2026-11-01）：npm 上 `ai` 最新 7.0.159、`@ai-sdk/openai` 最新 4.0.106（2026-11-01 实查）；视频用 `stopWhen: stepCountIs(3)` 属 AI SDK v5 世代写法（v4 是 maxSteps），现行已到 v7，代码须按 v5 时点理解、引用前对照现行文档。课内网页搜索演示输出「as of September 2025, OpenAI's latest large language model is GPT-5」，而页面发布日是 2025-12-24——录制与发布差约三个月，「最新模型」类表述尤其要按录制时点看。

### 三、技巧总结

- 技巧 1：先裸写一遍，再换抽象层。同一个检索流程，裸 OpenAI 版要自己拼 embedding → RPC → 上下文 → prompt → responses.create 五步；换 SDK 后 ingest 与 retrieval 都收敛成 embed 一行。我的思考：这种「两遍法」是判断一个 SDK 值不值得用的最好办法——省下的行数和丢掉的控制点都看得见，而不是听框架自述。
- 技巧 2：分类调用要按「判定」而不是「生成」来配参数。classifyAndRetrieve 的分类只准输出 general / retrieval 两个词，于是 maxOutputTokens 压到 20、temperature 压到 0。我的思考：很多人的分类提示又长又贵，是因为把分类当生成在写；判定类调用的成本控制就三件事——输出词表封闭、token 上限贴身、温度归零。
- 技巧 3：分类逻辑升级为结构化输出时，reasoning 字段要排在 type 字段之前。第 15 章的 schema 是 z.object({ reasoning, type })，先给理由再给结论，generateObject 返回的对象直接解构出决策变量。我的思考：这与第 31 条作业护栏是同一招——判定做成可被代码消费的结构，reasoning 留作审计；字段顺序让模型先想后判，是零成本的质量提升。
- 技巧 4：把检索包成工具，而不是把工具挂在检索旁边。终局 agent 只有两个工具：knowledge-base search（内部完成 embedding + 向量查询 + 阈值过滤）和 web search；系统提示只写三条路由规则（Scrimba 问题用知识库、需要实时信息用网页搜索、已知答案直接答）。我的思考：对照第 14 章的显式分类器——显式分类可控、可测，工具化路由省代码但把决策藏进模型；课程把两条路都走完，恰好说明这不是对错问题，是把路由放在代码里还是提示里的一次显式取舍。
- 技巧 5：切分参数与检索参数要一起报。课程给定的一组可复用基线：chunk 2,000 字符、overlap 100、match count 5、threshold 0.5；5,988 字符的文本切成 4 块。我的思考：这组数字本身不重要，重要的是演示里每个参数都对应一个肉眼可见的失败（不切分时整篇文档被当成一块返回、相似度被无关内容稀释）——调参时先复现失败，再动数字。

### 四、详细笔记（按章节）

- 0:00 课程地图与成品演示：客服 agent 对文档内问题（如怎么进 Scrimba Discord）走检索并附来源（画面可见来源文件与 Similarity: 0.7275），文档外问题走网页搜索。讲师在 5:58 明说「This course is quite advanced」，先修要求列了 prompting、基础 RAG、基础 function calling、Express。
- 0:06:47 RAG 复习：以 FIFA Club World Cup 问题说明 LLM 受训练数据所限；embedding 模型选 text-embedding-3-small。
- 0:12:57 向量库搭建：Supabase（Postgres + pgvector）；created_tables.sql 启用 pgvector 并建 documents 表（content / metadata / embedding，维度 1,536）；ingest 时把来源文件名放进 metadata；公共 key 与 service role key 的区别当场讲清（后者绕过行级安全）。
- 0:20:19 检索：match_documents 函数（match count、threshold、filter 三参数）；余弦相似度 0–1；未切分时返回的是整篇文档、相似度偏低，讲师当场指出原因。
- 0:32:51 切分：用「非常基础」的演示切分器（讲师明说仅供演示），参数见技巧 5；检索问题「How many houses were damaged during the Great Fire of London?」答约 13,200 间。
- 0:40:34 SDK 基础：generateText 只需 model + prompt；embed 走 openai.textEmbeddingModel，结果仍是 1,536 维。强调点是 provider 无关接口。
- 0:47:22 结构化输出：generateObject + Zod schema；两个用例——菜谱 schema（name / ingredients 数组 / steps 数组）与评论分类（z.enum + reasoning + .describe() 引导）。
- 0:58:39 工具调用：weather 工具的完整代码见原样保留区；讲清 tool call（结构化入参）与 tool result（执行结果）的区别；多工具后加 stopWhen: stepCountIs(3)，三步分别是工具调用、工具结果、最终汇总。
- 1:08:37 工具挑战的价格数据（内存中的 grocery「数据库」）：milk 1.59、bread 2.49、eggs $3.29、banana 0.59，合计 3.88，配送 26 分钟——数字自洽（1.59 与 2.49 是单项查询，3.29 + 0.59 = 3.88 已复算）。
- 1:18:04 路由：分类提示里写死知识库描述（Scrimba 是什么、何种问题算 retrieval）；9 篇帮助文档整篇 embedding 入库（此章不切分）；「What is the capital of France?」被分类为 general、retrieved docs 为 null；检索出错或无相关文档时回退为直接回答。
- 1:35:08 网页搜索与合并：openai.tools.webSearchPreview() 无参挂载；终局 agent 的结果整形分两路——网页来源抽 title/URL，知识库调用要钻进 result.steps 抽文档并按阈值过滤，最后统一返回 { answer, sources, toolUsed }。Keystone 成品是 Express server.js（端口 3000）+ index.html / client.js 的最小全栈。

### 五、原样保留区

第 3 章 config.js（画面原文，完整可读）：

```javascript
import OpenAI from 'openai';
/** Ensure the OpenAI API key is available and correctly configured */
if (!process.env.OPENAI_API_KEY) {
  throw new Error("OpenAI API key is missing or invalid.");
}
export const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
})
```

第 12 章 weather 工具（画面原文，1:02:57 定格可读部分）：

```javascript
async function basicWeatherToolCall() {
  const result = await generateText({
    model,
    tools: {
      weather: tool({
        description: 'Get the weather in a location',
        inputSchema: z.object({
          location: z.string().describe('The location to get the weather for'),
        }),
        execute: async ({ location }) => ({
          location,
          temperature: 50 + Math.floor(Math.random() * 21) - 10, //random number
        }),
      }),
    },
    prompt: 'What is the weather in New York?',
  });
```

第 15 章挑战给定的分类 schema（口述 + 画面拼合，实质原文）：z.object({ reasoning: z.string().describe('Brief reasoning for the classification choice'), type: z.enum(['retrieval', 'general']).describe('Is the question general or does it require knowledge base retrieval?') })。

未实测说明：本课代码在 Scrimba 站内环境运行，无公开仓库可克隆实跑；以上代码为播放器画面逐字抄录，未在本地执行。

### 六、术语表

- generateText / generateObject：AI SDK 的两个核心函数，前者生成文本（可挂工具多步执行），后者按 Zod schema 生成结构化对象。
- stopWhen / stepCountIs：AI SDK v5 起的多步停止条件写法，stepCountIs(3) 即最多走 3 步（v4 世代为 maxSteps）。
- web_search_preview：OpenAI Responses API 的内置网页搜索工具，在 @ai-sdk/openai 里以 openai.tools.webSearchPreview() 挂载。
- match_documents：本课在 Supabase 里自建的向量匹配函数（RPC 调用），封装 pgvector 相似度查询。

### 七、易错点和存疑点

- **温度数字别当真**：weather 工具的温度是代码里的随机数（50 + Math.floor(Math.random() * 21) - 10，即 40–60），口述里出现的 43、72 分别是不同运行的结果，其中 72 已超出该公式的值域——那是后续汇总步的模型表述，不是工具返回值。引用「演示效果」时别把这些数字当数据。
- **切分参数口述自相矛盾**：36:03 口述 chunk size 2,000 字符，36:46 字幕又出现「each row represents 200 characters」；以 2,000 为准（与 5,988 字符切 4 块的算术吻合：3 块整 + 尾块，含 overlap）。
- **「similarity of five」是字幕串词**：5 是 match count（返回条数），不是相似度；相似度阈值是 0.5。
- created_tables.sql 的表定义与 match_documents 函数体在画面上不可读，只核到函数名、pgvector、documents 表与 1,536 维；SQL 原文未保留，不要按印象补写。
- 第 16 章占全课最后约 19 分钟却只有一个章节名，内含 4 个子课与 Keystone 成品；按简介目录找内容时容易低估这一章。
- 两段平台广告（CodeRabbit、Paddle）在播放中插入，已按取材标准识别并排除，不属于课程内容。

---

## 37. How to build a JARVIS AI Voice Agent for FREE | Full Livekit Tutorial (2026)

### 一、元信息

- 标题：How to build a JARVIS AI Voice Agent for FREE | Full Livekit Tutorial (2026)（页面原样；Livekit 的 k 为小写，与产品名 LiveKit 不一致）
- 链接：https://www.youtube.com/watch?v=Fhm8goKN6rM
- 频道：Thanh-y David Nguyen（1.47 万订阅）
- 时长：46:44（播放器实测）
- 发布时间：2026-09-13
- 观看量：176,669 次；点赞 2,642
- 配套仓库：https://github.com/ruxakK/jarvis-voice-butler （已克隆对照，9 个 .py 文件全部字节编译通过；2026-09-19 仍有推送）；移动端底座 https://github.com/ruxakK/livekit_flutter_starter
- 核验档：A 档（播放器逐章扫 + 仓库克隆对照 + 仓库测试实跑）
- 章节（简介原样，共 19）：0:00 Intro；02:07 LiveKit Setup；07:45 Testing Basic Voice Agent；08:23 System Prompt；09:38 Setting Up Gemini；13:58 Enable Vision；14:57 Testing Jarvis；16:14 Basic Web Search；20:02 Testing Basic Web Search；21:36 OpenCode Setup；26:22 Building Browser Tool；29:30 Testing Browser Tool；32:31 Local Frontend；35:50 Testing Local Frontend；36:36 Customizing Frontend；38:08 Testing Customized Frontend；40:21 Launching as App；44:45 Tesing App（简介原文拼写如此）；46:02 Outro

### 二、一句话结论与摘要

一条「免费搭一个能听、能看、能搜、能开浏览器、还能装进手机的 JARVIS」的全程实录：LiveKit 云 + Gemini 实时模型做语音与视觉，工具先手写一个（网页搜索），后面的浏览器工具与前端改版全部交给编码 agent（OpenCode）代劳——这条视频真正的分水岭不在 LiveKit，而在中途「从手写工具切换到让 agent 写工具」的工作方式转折。

- 实时模型路线：换上 Gemini 实时模型后，原来的 STT / TTS 两段从 agent.py 里删掉，语音进出由模型一体处理；仓库核实为 google.beta.realtime.RealtimeModel，音色 Enceladus、英式英语（见原样保留区）。
- 播放器里读不清的三处关键代码（系统提示首行、视觉开关的标识符、Gemini 片段）全部由仓库解明，且仓库与口述有一处实质差异：口述的独立 search_web 文件在现行仓库已并入 BrowserTools，搜索方式也从「调 DuckDuckGo 库函数」改成「用 Playwright 打开 DuckDuckGo 结果页再读页」。
- 仓库测试实跑发现：tests/test_prompts.py 第一行把导入名拼错（AGENT_ISNTRUCTIONS），按仓库现状收集即 ImportError——与第 33 条 CrewAI 那次「生成测试跑不起来」是同型问题，这次是人手写的测试。
- 时效性（截至 2026-11-01）：视频是 2026-09 的形态，LiveKit 与 Gemini 实时模型都还在快速迭代；「免费」是额度口径——LiveKit Build 档含每月 1,000 分钟（画面价格页原样：Build $0/mo、Ship $50/mo、Scale $500/mo），Gemini 侧讲者展示的是每分钟 token 上限（口述 65,000）而非无限免费。超出额度后的成本须按现行价格页复核。

### 三、技巧总结

- 技巧 1：系统提示放在 instructions 一个字段里整体替换，人设与行为规则不进代码逻辑。仓库的提示词 4,873 字，结构是人设一行 + 输出规则 + 工具使用规则 + 固定应答句式（「Jarvis, you there?」必须只答 "At your service, Sir" 且其后不许加任何内容——连标点级的服从都写成硬规则）。我的思考：语音 agent 的人设一致性靠的不是模型自觉，是这种「触发句 → 定稿回复」的查表式条款；文字 agent 同样适用，凡是要一字不差的应答都该这样写死。
- 技巧 2：工具先手写一个最小的，再把同类工具批量交给编码 agent。讲者先手写 search 工具讲清 function tool 的三要素（描述、参数、业务逻辑），到浏览器工具时改走 OpenCode 的 plan 模式：先出计划（新建 browser.py、工具清单、改 agent.py、补提示词段），人审计划、拍板「标签页开关拆两个工具还是合一个」，再让它 build。我的思考：顺序不能反——没手写过第一个工具的人，审不出计划里工具粒度的毛病；讲者在 29:30 给的理由也是这两条（有些步骤必须手动、且要懂架构才能审计划）。
- 技巧 3：给编码 agent 接文档 MCP 再让它写框架代码。OpenCode 配置里挂了 LiveKit 文档 MCP，写工具前先让它答「加工具该用哪个概念」，确认它取到的是 function tools 再开工。我的思考：这是「让 agent 先查文档后动手」的一个具体形态，比把文档整本塞上下文便宜，也比靠模型记忆里的旧 API 可靠。
- 技巧 4：视觉只是一个开关。仓库核实：room_options 里 video_input=True 一行，摄像头画面就进实时模型的输入；演示里 agent 能描述穿着、认出屏幕内容。我的思考：多模态接入的成本已经低到「一行配置」，真正的成本在别处——这类演示默认不谈画面数据的去向（经云端实时模型处理），自用无妨，给别人用必须先想清这一层。
- 技巧 5：免费额度的算术要自己算。讲者口述 1,000 分钟「equates to 16 hours」，实际 1,000 ÷ 60 = 16.67 小时；他对 Gemini 限额的读法（RPM/RPD 不限、TPM 65,000、自用约 15,000，四台并发才触顶）是少见的把限额页读出可用结论的演示。我的思考：免费档宣传页只给分钟数，把它换算成「每天能聊多久、几个人同时用会爆」才是决策数字，这一步没人替你做。

### 四、详细笔记（按章节）

- 02:07 环境：用 LiveKit 云而非自托管；装 Python、UV、NodeJS、LiveKit CLI；关掉 agent observability（暂）；官方 quickstart 自称 10 分钟内跑起第一个语音 agent。
- 07:45 最小 agent 在 LiveKit 网页端会话里直接对话成功，此时还没换模型。
- 08:23 系统提示：见技巧 1。首行口述为 “You are Jarvis, a helpful sarcastic AI butler.”；仓库原文无逗号（“You are Jarvis a helpful and sarcastic AI butler.”），按仓库层记录。
- 09:38 换 Gemini：装 Gemini 插件、.env.local 写 GOOGLE_API_KEY、删 STT/TTS 行；选它的理由是免费额度、低延迟、实时、可换语言，音色要「男声、英式、接近 JARVIS」。
- 13:58 视觉：见技巧 4。14:57 实测能描述镜头前的人（衬衫颜色、对着麦克风说话），并试了多语言；同章讲者明说这个配置下网页端的文字聊天用不了（“would simply not work”）——与简介「Real-time voice & text conversation」不符，以实测为准。
- 16:14 手写搜索工具：新建文件，async 函数收 RunContext 与 query，日志记录，失败处理，@function_tool 注册；提示词补一条「用户要搜信息时用搜索工具」。仓库现行形态见第二节。
- 21:36 OpenCode：安装失败有 npm 兜底命令；/models 看模型、opencode.json 挂 LiveKit 文档 MCP、/mcp 与 /session 核对连接状态（具体命令字串在画面上不可读，以上为口述层）。
- 26:22 浏览器工具计划：工具清单为 open browser、close browser、navigate、go back、click element 等；标签页的打开与切换拆成两个工具（讲者拍板）。
- 29:30 浏览器实测：语音指挥下完成「搜我身上这件白 T 恤 → 点开 Amazon 第一条 → 后退 → 开 Google Maps 查去 Central Park 的路线（步行最快、公交约 10 分钟）」。
- 32:31 起前端：把 LiveKit 官方 Next.js 语音前端拉到本地跑（pnpm dev 或 npm run dev），启动报错就把报错贴给编码 agent 修；36:36 在 Agents UI 里把可视化换成 grid + 橙色（色值画面不可读、仓库层亦未定位，不记录）。
- 40:21 上手机：Flutter starter 拉下来改名 .env、填开发 token 相关 ID；Android 开发者模式要点 build number 七次、开 USB 调试；演示机代号 HD1900（口述）。iPhone 流程相同外加开发者模式，简介给了教程链接。
- 46:02 结尾自述局限：手机端浏览器控制「not that optimal」，要更好他会改用付费工具——简介的「Full browser control / Runs on your phone」按这句打折。

### 五、原样保留区

模型与视觉配置（仓库层原文，jarvis_new/src/agent.py；已克隆，字节编译通过）：

```python
llm=google.beta.realtime.RealtimeModel(
    model="gemini-3.1-flash-live-preview",
    voice="Enceladus",
    language="en-GB",
    tool_response_scheduling=genai_types.FunctionResponseScheduling.WHEN_IDLE,
),
```

```python
room_options=room_io.RoomOptions(
    video_input=True,
```

系统提示首行（仓库层原文，src/prompts.py 的 AGENT_INSTRUCTIONS 与 agent.py 内嵌副本一致）：

```
You are Jarvis a helpful and sarcastic AI butler.
```

仓库现行工具清单（src/tools.py，全部带 @function_tool）：search_the_web、open_url、read_page、inspect_page、go_back、take_screenshot、click、confirm_browser_action。其中 search_the_web 已不是视频口述的「DuckDuckGo 库函数」形态：tools.py 里 duckduckgo_search_url(query) 只负责拼出 https://duckduckgo.com/?q=... 的查询 URL，搜索由浏览器工具打开结果页完成。

仓库测试实跑记录（2026-11-01）：tests/ 共 4 个文件；test_prompts.py 第一行 `from prompts import AGENT_ISNTRUCTIONS` 拼写错误，prompts.py 只定义 AGENT_INSTRUCTIONS；以 PYTHONPATH=src 实跑该导入即 ImportError（解释器提示 Did you mean: 'AGENT_INSTRUCTIONS'?），正确名字的导入正常（提示词长 4,873 字）。该测试文件按现状无法通过收集。

画面价格页（LiveKit 定价，02:32–02:44 定格可读部分）：Build $0/mo（1,000 分钟 / 5 并发 / 1 部署）、Ship $50/mo（5,000 分钟 / 20 并发 / 2 部署）、Scale $500/mo（50,000 分钟 / Up to 600 并发 / 4 部署）、Enterprise Custom。

### 六、术语表

- LiveKit Agents：LiveKit 的语音/视频 agent 框架，本课用其云端托管与 Python SDK；AgentSession 承载一次会话，Agent 定义模型与 instructions 与工具。
- RealtimeModel：把语音识别、推理、语音合成合为一体的实时模型接入方式；换上它之后要删掉分开的 STT/TTS 配置。
- function_tool：LiveKit 里把普通 async 函数注册为 agent 工具的装饰器，工具描述与参数名就是模型决定是否调用的依据。
- OpenCode：开源编码 agent，本课用它的 plan / build 双模式与 /mcp、/agents 等斜杠命令完成工具开发与前端改版。

### 七、易错点和存疑点

- **简介宣称、实测未见（本课三处）**：文字聊天（讲者明说网页端文字聊天不可用）、独立天气工具（实际只有通用网页搜索，天气是拿搜索演示的）、手机端全浏览器控制（结尾自述未做好）。引用本课能力清单时按实测层为准。
- **Playwright 出身讲错**：26:29 口述称 Playwright 是「directly from Google 的库」——Playwright 出自 Microsoft，此为口述错误，勿转述。
- **天气数字两处对不上**：片头演示维也纳 28°C / mostly cloudy，20:23 的搜索实测是 75°F（约 23.9°C）/ passing clouds，是两次不同运行，但同一条视频里不要把两组数字混引。
- **模型名的画面层与口述层**：口述 “Gemini 3.1 Flash live preview”，讲者随后展示的限额页他自称写的是 “Gemini 3 flash” 并称「includes 3.1 as well」；确切模型 id 以仓库层 gemini-3.1-flash-live-preview 为准。
- 播放器全程 360p：agent.py、browser.py、opencode.json 的完整代码在画面层不可读，本条笔记的代码证据以仓库层为准；视频当期的文件形态（独立 search_web 文件等）已被仓库演进覆盖，两层差异已在正文标明。
- OpenCode 当时所用模型名在画面与字幕里都是乱码（“big picle”），不可读、未核实，不记录。
- 章节名 “Tesing App” 是简介原文的拼写错误，照录。

---

## 38. Browser Use: This New AI Agent Can Do Anything (Full AI Scraping Tutorial)

### 一、元信息

- 标题：Browser Use: This New AI Agent Can Do Anything (Full AI Scraping Tutorial)（页面原样）
- 链接：https://www.youtube.com/watch?v=zGkVKix_CRU
- 频道：Tech With Tim（211 万订阅）
- 时长：22:27（播放器实测）
- 发布时间：2025-03-23
- 观看量：187,231 次；点赞 3,985
- 配套仓库：无（现场照官方文档写代码；文档 https://docs.browser-use.com/introduction 为简介所给）
- 核验档：B 档（播放器逐章扫 + 版本与星数案头核查）
- 章节（简介原样，共 9）：00:00 Overview；00:44 What is Browser Use；02:53 Environment Setup；04:55 Picking an LLM；07:53 Basic Agent；10:15 Using Your Own Browser；12:52 Structured Output；19:24 Initial Actions；20:33 Sensitive Data/Passwords

### 二、一句话结论与摘要

Browser Use 入门实作：三段代码分别解决「让 LLM 开浏览器干活」「接管你自己已登录的浏览器」「把抓取结果压成 Pydantic 结构」三件事，外加 initial_actions 省步数、sensitive_data 藏密码两个小而关键的机制；讲者难得地把两次演示翻车都留在片子里——基础任务解析失败三次、结构化输出的帖子 URL 是错的。

- 核心卖点是「已有浏览器上下文」：接管本机 Chrome 后，登录态、书签、主题都在，agent 是以「你」的身份在操作，这与无头爬虫框架是两种东西；代价是运行前必须关掉所有已开的 Chrome 实例。
- sensitive_data 的机制值得单记：密码字典传给 Agent，LLM 只看得到键名（x_name、x_password），填表时按名索取，值不进模型上下文。讲者自己的态度也留在片中——「If you trust that, great. If you don't, I mean, it's what it is.」
- 本片有约 1 分钟的片内赞助段（SysAid，约 1:51–2:52），按取材标准识别并排除，不算教学内容；平台广告未观察到。
- 时效性（截至 2026-11-01）：browser-use 现行版本 0.13.14（PyPI 核于 2026-11-01），视频是 2025-03 的 0.1.x 世代——BrowserConfig / chrome_instance_path / Controller(output_model=...) 都是当期 API，后续版本已大幅重构，代码不能照抄到现行版本。星数从画面上的 43,800 涨到 119,715（GitHub API 核于 2026-11-01）。片中模型 claude-3-5-sonnet-20240620 也是当期口径。

### 三、技巧总结

- 技巧 1：密码按名传递，不按值传递。sensitive_data = {'x_name': ..., 'x_password': ...} 传给 Agent 后，LLM 在任务中只能引用键名，框架在真正填表时才替换为真值。我的思考：这是「秘密不进上下文」原则的一个最小实现，和把 key 贴进提示词相比是质的区别；但要看清边界——值仍然经过本机框架代码，信任面从模型供应商缩小到框架本身，没有消失，讲者那句保留态度正是对着这一点。
- 技巧 2：结构化输出用 Controller(output_model=Posts) 把「抓什么」钉死成 Pydantic 模型，再用 Posts.model_validate_json(result.final_result()) 把运行结果变成可编程对象。我的思考：本片最诚实的一幕是格式全对、URL 全错——结构化只保证形状不保证事实，抓取类任务的验收必须另设一层事实核查（比如把 URL 真打开一次），不能拿 schema 通过当正确。
- 技巧 3：initial_actions 把确定性步骤从模型手里拿回来。每次都要先打开的页面写成 [{'open_tab': {'url': ...}}] 在运行前执行，省掉模型摸索的步数与费用。我的思考：这与第 36 条「确定性步骤用代码、判断留给模型」是同一原则在浏览器场景的落点；凡是每次运行都一样的导航，都不该让模型即兴发挥。
- 技巧 4：调试一个浏览器 agent 靠的是运行史而不是盯屏幕。讲者列的检查面：screenshots（出错时）、action names、extracted content、model thoughts、errors、final result。我的思考：浏览器 agent 的失败大多不在最终答案里，在中间某一步的动作选择上；没有逐步运行史就只能靠重跑碰运气。
- 技巧 5：任务写得含糊，失败率肉眼可见。基础演示的任务只有一句「Compare the price of gpt-4o and DeepSeek-V3」，结果解析失败三次；讲者自己归因于指令太含糊。我的思考：浏览器 agent 的任务描述要写到「去哪个站、比什么字段、什么算完成」的粒度，和给人派活没有区别。

### 四、详细笔记（按章节）

- 00:44 框架定位：截图 + DOM 抽取交互元素，LLM 决定下一步；主页自称 WebVoyager 89% SOTA（画面口径，见存疑点）；功能行可见 Vision + HTML Extraction、Multi-tab Management、Element Tracking、Custom Actions、Self-correcting。
- 02:53 安装：讲者不用文档推荐的 uv 路线，改走 pip install browser-use + playwright install（他的机器上 Playwright 已装好，无输出）；虚拟环境「可选、未演示」。
- 04:55 选模型：可用 Claude / GPT / DeepSeek 或本地模型（Ollama）；最快路径用 Anthropic key（.env 明文存放、无引号），同时提醒 Anthropic 用量「can be a little bit expensive」要盯用量。样例代码从文档的 OpenAI 版改成 ChatAnthropic。
- 07:53 基础 agent：默认开的是全新 Chrome（Playwright 托管）；演示任务与翻车见技巧 5；控制台能看到 agent 的思考过程。
- 10:15 接管自己的浏览器：BrowserConfig 里写 chrome_instance_path，Browser 传给 Agent，结尾 await browser.close()；演示里先吃了「全新实例是阿语界面」的亏（讲者在阿联酋），换回自己的浏览器才正常。远程浏览器（WSS URL）只在文档里提及，未演示。
- 12:52 结构化输出：见技巧 2。任务是抓 Tech With Tim 的 Instagram 最近 5 条帖子的 captions；无结构化时返回的是带换行符的散文且键名不保证。
- 19:24 initial_actions：见技巧 3；演示的跳转本身没出现在画面里（讲者自述）。
- 20:33 sensitive_data：见技巧 1。画面上的示例值 magnus / 12345678 是教学假数据，照录仅为说明键名形态。

### 五、原样保留区

基础 agent（画面原文，main.py，8:00 定格可读部分）：

```python
from langchain_anthropic import ChatAnthropic
from browser_use import Agent
from dotenv import load_dotenv
load_dotenv()
import asyncio
# Initialize the model
llm = ChatAnthropic(
    model_name="claude-3-5-sonnet-20240620",
    temperature=0.0,
    timeout=100,  # Increase for complex tasks
)
async def main():
    agent = Agent(
        task="Compare the price of gpt-4o and DeepSeek-V3",
        llm=llm,
    )
    result = await agent.run()
```

接管本机浏览器（画面原文，11:43 定格）：

```python
from browser_use import Agent, Browser, BrowserConfig
browser = Browser(
    config=BrowserConfig(
        # Specify the path to your Chrome executable
        chrome_instance_path='C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe',  # macOS path
        # For Windows, typically: 'C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe'
        # For Linux, typically: '/usr/bin/google-chrome'
    )
)
```

（注意第二行行尾注释把 Windows 路径标成 “macOS path”，与下一行注释矛盾——画面原样，见存疑点。）

结构化输出（画面右侧文档示例原文）：

```python
from pydantic import BaseModel
class Post(BaseModel):
    post_title: str
    post_url: str
    num_comments: int
    hours_since_post: int
class Posts(BaseModel):
    posts: List[Post]
controller = Controller(output_model=Posts)
```

initial_actions 与 sensitive_data（画面原文，21:00 定格）：

```python
initial_actions = [
    {'open_tab': {'url': 'https://instagram.com/tech_with_tim'}},
]
sensitive_data = {'x_name': 'magnus', 'x_password': '12345678'}
```

未实测说明：本片无配套仓库，代码为播放器画面逐字抄录，未在本地执行；且为 0.1.x 世代 API，装现行 0.13.x 也跑不起来，不做兼容性实测。

### 六、术语表

- Browser Use：让 LLM 通过 Playwright 控制浏览器的开源框架；输入是截图加 DOM 交互元素，输出是点击、输入、切页等动作序列。
- Controller / output_model：当期 API 里给 Agent 挂结构化输出模型的组件，运行结束用 final_result() 取原始结果再做模型校验。
- initial_actions：运行开始前由框架直接执行的动作清单，用于跳过固定导航步骤。
- sensitive_data：键名可见、真值不可见的秘密传递机制，LLM 按键名引用，框架在执行时替换。

### 七、易错点和存疑点

- **「全面免费」是框架口径**：片头说 “All of this is free”，但同片又说云服务可购买、Claude 等模型按量付费且不便宜——免费的只是本地框架本身。
- **macOS 路径注释错误**：chrome_instance_path 一行装的是 Windows 路径，行尾注释却写 “macOS path”，下一行注释又给出同一个 Windows 路径作 Windows 示例；照抄时以路径内容为准，别信注释。
- **两次翻车都在片中**：基础任务「failed three times / could not parse the response」；结构化输出的帖子 URL 讲者自述 incorrect、「it did something wrong」。这两处是本条笔记可信度的来源，引用演示效果时必须带上。
- **89% WebVoyager 的对照组读不清**：准确率图上除 89% 外的柱（87%、67%）标签不可读，「performs better than ChatGPT Operator」只有口述、无画面对照，不作为已核实的比较结论。
- 简介列 9 章，播放器章节面板一度只显示到 Initial Actions；Sensitive Data/Passwords 一章以简介与字幕标题为准，确实存在。
- 主页文案 “…so agents can focus on what makes their beer taste better.” 中 beer 一词在低清画面下只能照录存疑，疑为站点文案或渲染问题，未擅自改成 best。

---

## 39. A2A vs MCP Explained: The A2A Protocol v1.0, Step by Step (Real Messages)

### 一、元信息

- 标题：A2A vs MCP Explained: The A2A Protocol v1.0, Step by Step (Real Messages)（页面原样）
- 链接：https://www.youtube.com/watch?v=y2jhDQbMHac
- 频道：AI Build Along（15 订阅）
- 时长：15:52（播放器实测）
- 发布时间：2026-10-18
- 观看量：74 次
- 内容形态：页面标注「AI 生成内容」；简介明说 “The narration uses an AI voice”，示例公司与人物均为虚构教学素材——本条按概念讲解片核验，协议事实另行对照官方与多家独立来源（见原样保留区后的核查记录）
- 核验档：B 档（播放器逐章扫 + 协议事实案头核查）
- 章节（简介原样，共 16）：00:00 One request, two companies, two agents；00:56 The map: five words, five kinds of thing；01:37 Why not just call a tool?；02:33 Without a standard: every pair invents its own way；03:25 Step 1: find the agent and read its Agent Card；04:17 A skill is not a tool；05:22 Step 2: send a message；06:24 Step 3: a task, and a question back；07:23 Step 4: the task is completed, with a result；08:14 The life of a task, and long jobs；09:24 A2A and MCP work together；10:11 MCP vs A2A, side by side；11:16 A new partner joins: what changes?；12:08 Is it real? Who is behind A2A；13:00 What you gain, and the limits；13:56 Quick check: four questions；15:03 Recap

### 二、一句话结论与摘要

用一个虚构的「影院 agent 向餐厅 agent 订桌」故事把 A2A v1.0 的四步讲透：发现（Agent Card）→ 发消息（message:send）→ 任务带状态、可中途反问（INPUT_REQUIRED）→ 结果以 artifact 返回；全片的立论点是分工——MCP 管 agent 与自己的工具，A2A 管 agent 与别人的 agent，对方内部保持不透明。

- 五个词的地图先立规矩：LLM 是模型、tool 是函数、MCP 与 A2A 都是协议、agent 是「LLM + 工具 + 指令」的程序。后文所有比较都挂在这张图上，不易混。
- skill 与 tool 的区分是全片最锋利的一刀：tool 要把每个字段填对，skill 是用一句话描述一件工作、由对方 agent 自己想办法完成；对调用方的模型来说，另一个 agent 表现得就像一个工具，只是底下发的是一条 A2A 消息。
- 局限讲得比多数协议布道诚实：信任（把用户姓名电话发给另一家公司要先想清）、成本与速度（一件工作两个模型在烧）、以及「能用普通工具就别用 A2A」的收尾原则。
- 时效性（截至 2026-11-01）：核心协议事实经案头核查全部成立——A2A v1.0 于 2026-03-12 发布、Agent Card 路径 /.well-known/agent-card.json、MCP 现行规范日期 2026-07-28，均与多家独立来源一致（核查细节见存疑点）。一处治理变化视频未及：A2A 已于 2026-08 迁入 Linux Foundation 旗下的 Agentic AI Foundation，与 MCP 同门，视频仍按 LF 直管口径讲。a2a-sdk 现行 1.3.0（PyPI 核于 2026-11-01）。

### 三、技巧总结

- 技巧 1：先给五个词钉死定义，再讲协议。LLM / tool / MCP / agent / A2A 各用一句话定性，后面「该用 MCP 还是 A2A」的判断题全部能回到这张图上答。我的思考：协议类概念最常见的混乱是把层级搅在一起（拿 A2A 和 LangGraph 比、拿 MCP 和函数调用比）；这张「五种东西」的图可以直接借去做任何多 agent 话题的开场检查。
- 技巧 2：判断「工具还是 agent」看三件事：对方要不要理解目标（而不是收字段）、要不要长时间工作、要不要中途反问。三件都不需要，就是普通工具调用，别为它上协议。我的思考：这比「跨公司就用 A2A」的粗判据细得多——同一家公司内部，只要需要反问与长任务，任务状态机那一套也值得借；反过来跨公司但只是查一个字段，普通 API 就够。
- 技巧 3：不透明是特性，不是缺陷。餐厅不把订位工具发给全互联网，而是让自己的 agent 守着自己的规则、营业时间与优惠，对外只露 Agent Card 上的 skill 清单。我的思考：这与第 15 条提示注入的讨论正好接上——把工具暴露给外部调用方，等于把执行面交出去；A2A 的「只露能力描述、不露工具」在安全上是同一种克制，只是发生在组织之间。
- 技巧 4：反问要建模成任务状态，而不是异常。INPUT_REQUIRED 是与 WORKING 并列的正常状态：餐厅问「露台还是室内？过敏？」，影院 agent 发现 Sara 没说过，就必须去问 Sara 而不是替她猜，答案带同一个 task ID 回传。我的思考：「不许替用户猜」被写进协议流程而不是靠提示词自觉，这是这条视频里最值得抄的态度——凡是缺信息，先看它在状态机里有没有合法位置。
- 技巧 5：新伙伴接入的成本要按「改谁的代码」来算。无标准时新公司加入要开会定格式、两侧改代码、测发布维护（画面全打红叉）；有 A2A 时只需对方发布 Agent Card、本方把地址加进信任列表、模型读新 skill 即可，发送工具不用改。我的思考：五方全互联是 10 对、20 份定制代码，这个 n² 算术（画面原数）是所有「要不要上标准」讨论的起点。

### 四、详细笔记（按章节）

- 01:37 工具与 agent 的对照表（画面原样要点）：plain tool——固定输入输出、秒回、从不反问；another agent——理解用文字表达的目标、能工作几分钟到几小时、能反问、内部私有。
- 03:25 发现：GET /.well-known/agent-card.json；卡上只有名字、描述、消息地址、能力（如 streaming）、登录方式与 skills，明确不含数据库、内部工具与提示词；也有公司把 agent 登记在目录里。
- 05:22 发消息：普通 HTTP POST，带 token；同一条消息有三种绑定——纯 JSON over HTTP、JSON-RPC、gRPC，卡上声明支持哪种。此处的 ROLE_USER 指发起方的影院 agent，不是 Sara 本人。
- 06:24 任务与反问：见技巧 4。示例任务 task-81 / ctx-7。
- 07:23 完成与 artifact：结果可以是结构化数据、文本或文件（如 PDF 票）；影院 agent 把订桌结果与自家工具订的座位合并成一条回复。
- 08:14 任务生命周期：SUBMITTED → WORKING →（INPUT_REQUIRED / AUTH_REQUIRED 暂停）→ COMPLETED / FAILED / CANCELED / REJECTED；长任务三条腿：message:stream 流式更新、推送通知、GET /tasks/{id} 轮询。REJECTED 的例子是当晚订满，对方 agent 直接拒活，调用方代码要决定转问别家还是回报用户。
- 09:24 两协议咬合图（画面原样）：Sara → 影院 agent（内部 MCP ↓ book_seats）⇄ A2A ⇄ 餐厅 agent（内部 MCP ↓ reserve_table）；影院永远看不到餐厅的工具。
- 10:11 对照表（画面原样五行）：CONNECTS——MCP 连 agent 与工具数据，A2A 连 agent 与 agent；YOU SEND——精确入参 vs 用消息表达的目标；DISCOVERY——tools/list vs Agent Card；ASKING BACK——MCP 是服务器经应用问用户，A2A 是对方 agent 在任务内反问；TIME——MCP 一次调用通常秒级（长任务有可选 tasks 扩展），A2A 秒到小时、内建于每个任务。
- 12:08 出身：Google 开发、捐给 Linux Foundation；指导委员会画面名单 AWS、Cisco、Google、IBM Research、Microsoft、Salesforce、SAP、ServiceNow；官方 SDK 例：pip install a2a-sdk。
- 13:56 四道自测题答案：skills 在 Agent Card 里（A）；餐厅反问时状态是 INPUT_REQUIRED（B）；影院订自家座位用 MCP（A）；影院看不到餐厅的工具、只看得到 skills（口述题）。

### 五、原样保留区

Agent Card（画面原文，简介自述为缩略版）：

```json
{
  "name": "Luna Bistro Booking Agent",
  "description": "Books tables at Luna Bistro.",
  "version": "1.0.0",
  "supportedInterfaces": [
    {
      "url": "https://agent.luna-bistro.example/a2a",
      "protocolBinding": "HTTP+JSON",
      "protocolVersion": "1.0"
    }
  ],
  "capabilities": { "streaming": true },
  "securitySchemes": { 「不可读」 },
  "skills": [
    {
      "id": "book-table",
      "name": "Book a table",
      "description": "Find and book a table for a date, time and group size.",
      "examples": ["A table for 2 at 18:00 tonight"]
    }
  ]
}
```

发送消息（画面原文）：

```
POST /a2a/message:send
Content-Type: application/a2a+json
Authorization: Bearer eyJhbGciOi_ 「不可读：令牌在画面上被截断」
```

```json
{
  "message": {
    "messageId": "msg-1",
    "role": "ROLE_USER",
    "parts": [
      { "text": "A table for 2 at Luna Bistro tonight, 18:00, before a 19:30 film nearby." }
    ]
  }
}
```

任务反问与完成（画面原文）：

```json
{
  "task": {
    "id": "task-81",
    "contextId": "ctx-7",
    "status": {
      "state": "TASK_STATE_INPUT_REQUIRED",
      "message": {
        "role": "ROLE_AGENT",
        "parts": [{ "text": "Terrace or inside? Any allergies?" }]
      }
    }
  }
}
```

```json
{
  "task": {
    "id": "task-81",
    "contextId": "ctx-7",
    "status": { "state": "TASK_STATE_COMPLETED" },
    "artifacts": [
      {
        "artifactId": "res-2214",
        "name": "Table reservation",
        "parts": [{ "data": { "reservation": "R-2214", "time": "18:00", "guests": 2, "table": "inside" } }]
      }
    ]
  }
}
```

协议事实核查记录（2026-11-01 案头核对）：A2A v1.0 发布日 2026-03-12——gravity.fast 的标准普查、agno 的 A2A/MCP 对比文与 Linux Foundation 时间线转述三处一致；Agent Card 路径与 a2a-samples 官方仓库（1,820 stars）均存在、与片中一致；MCP 现行规范日期 2026-07-28 见 agno 文内确认。JSON 为片中教学示例（简介自述 shortened），未拿真实服务器实测，故标「未实测」于服务器行为一层。

### 六、术语表

- Agent Card：agent 的公开名片（JSON），含身份、能力、消息地址、认证方式与 skill 清单，固定挂在 /.well-known/agent-card.json。
- skill（A2A 语境）：用自然语言描述的一件可交付工作，如 book-table；与必须填准字段的 tool 相对。
- task：一次委派的完整生命周期对象，有 ID 与状态机，反问、等待、完成、拒绝都是其合法状态。
- artifact：任务完成时返回的成果物，可以是结构化数据、文本或文件。

### 七、易错点和存疑点

- **出处档位先说清**：频道只有 15 名订阅、视频 74 次观看，旁白为 AI 语音、页面带 AI 生成标注。它讲对了（本条已逐项外查），但引用时应引协议官方与规范，不要把这条视频当一手来源。
- **治理归属已变**：视频（自述 2026-10-17 核查）说 A2A 在 Linux Foundation；2026-08 它已迁入 LF 旗下的 Agentic AI Foundation（AAIF），与 MCP、goose、AGENTS.md 同门。协议内容不受影响，机构口径引用前按新归属改。
- **状态名有两套写法**：简介与自测题用短形（INPUT_REQUIRED），画面 JSON 用全形（TASK_STATE_INPUT_REQUIRED），是同一状态的简写与正式名，不要当成两种状态。
- **同一 skill 两次出现字段不一致**：Agent Card 里的 book-table 没有 tags 字段，04:17 的对比幻灯片里却有 "tags": ["restaurant", "booking"]——教学材料内部的小出入，照录存疑。
- 发布日「12 March 2026」只有简介给出，画面只写 March 2026；本条已由外部三处独立来源核实到日，可以用，但要知道画面层只到月。
- securitySchemes 的值在 360p 画面上不可读，JSON 里标「不可读」，不要按惯例补写。
- 片中相对时间「13 天前」与展开日期 2026-10-18 在观看日（2026-11-01）对不齐，以展开的绝对日期为准。

---

## 40. Automated Prompt Engineering with DSPy

### 一、元信息

- 标题：Automated Prompt Engineering with DSPy（页面原样）
- 链接：https://www.youtube.com/watch?v=0gYMqFYRtDI
- 频道：Trelis Research（2.63 万订阅）
- 时长：45:51（播放器实测）
- 发布时间：2024-09-12（首播）
- 观看量：7,491 次；点赞 235
- 配套材料：视频用的调整版笔记本在其付费 ADVANCED-inference 仓内；免费底本是 DSPy 官方 intro.ipynb（简介链接在页面上被截断；2026-11-01 浅克隆现行 dspy 仓库，未在其树中定位到该文件，不硬指路径）
- 核验档：A 档（播放器逐章扫 + 分数复算 + 现行版本案头核查；视频笔记本付费、未实跑）
- 章节（简介原样，共 16）：0:00 Introduction to structured/automated prompt generation；0:41 Video Overview；2:10 Why is DSPy useful?；3:41 Q&A and Retrieval Databases (hotpotqa and wikipedia)；4:53 Notebooks - DSPy and Trelis；6:08 Installation and Setup；9:24 Step 1: Simple Prompting and Benchmarking；12:49 Step 2: Adding Chain of Thought；15:08 Step 3: Add vector search (retrieval)；19:00 Step 4: Add (random) few shot examples；24:00 Step 5: Optimally choosing few shot examples；30:30 Step 6: Add multi-hop search/queries (Baleen / Perplexity-style)；36:14 Step 7: Multi-hop search WITH optimally chosen few shot examples；40:54 Overall Results Comparison!；43:21 DSPy assertions - for further answer quality control；45:21 Video resources

### 二、一句话结论与摘要

一条把「提示工程」变成「逐级加模块、每级量一次分」的实验记录：在 HotpotQA 的 50 道开发题上，从裸提示的 20% 起步，依次叠 Chain of Thought、检索、随机 few-shot、优选 few-shot、两跳检索、最终全量优选，每一步的确切分数都摆在结尾的比较屏上——DSPy 的卖点不是某个技巧，是这条可复现的叠加流水线本身。

- 比较屏原文（模型 gpt4o-mini）：Basic 20% → CoT 34% → +检索 42% → +随机 few-shot 54% → +优选 few-shot 58% → 两跳检索（无优选）60% → 两跳 + 优选 68%。前四个百分比已按 10/17/21/27 ÷ 50 复算无误。
- 增益排序值得记：检索单独只值 +8 个点（讲者自己说意外地少），第二跳检索值 +18 个点（42%→60%），而把 few-shot 从随机换成优选只值 +4 个点——「先修检索质量，再抠示例选择」的优先级是这条视频最实用的结论。
- BootstrapFewShot 的实质：不是挑「好示例」，是在训练集上真跑，只把全程轨迹答对了的运行整条留作示例（端到端 trace），答错的直接扔；三跳消融（54%「needs to be re-run」、优选后 66%）被讲者自己标为无显著差异，没有藏。
- 时效性（截至 2026-11-01）：dspy 现行 3.4.1（PyPI 核于 2026-11-01）；视频是 2024-09 的 dspy-ai 包名时代（安装格里 openai==0.28.1），Signature / Module / Teleprompter 这套骨架至今仍是 DSPy 的主干，`dspy.Retrieve` 与 ColBERTv2 在现行源码中仍在（属遗留路径）；但具体 API 与优化器阵容两年間变化不小，方法论可迁移、代码须按现行文档重写。

### 三、技巧总结

- 技巧 1：先立基线，再逐级叠加，每级只动一个变量。七步里每步都用同一套 50 题、同一个 exact-match 指标复测，分数表才有可比性。我的思考：这是把提示调优从手艺变成实验的关键动作；多数人调提示是同时改三处然后凭感觉说「好像好了」，这条视频的纪律恰好相反。
- 技巧 2：Signature 把提示变成带类型的声明。BasicQA 只声明 question 输入与 answer 输出（desc 写「often between 1 and 5 words」），格式、分隔、指令全由 DSPy 按声明生成——原样保留区的代码就是全部。我的思考：声明式的价值在可优化：字段是显式的，优化器才能替换示例、改写指令；散文式提示没有可下手的抓手。
- 技巧 3：few-shot 的优选标准是「跑对过」，不是「看起来好」。BootstrapFewShot 在 20 道训练题上实测：Step 5 中 12 组里 4 条全程答对的轨迹被留下、8 条扔掉；Step 7 的多跳版只跑出 3 条。验收还双查——答案 exact match 且答案出现在检索段落里（passage match），防止「检索错了但蒙对」。我的思考：这等于把示例库当训练数据在筛；对照第 32 条 smolagents 的 agentic RAG 与本批第 36 条的分类 schema，「让产出先过一道可执行检验再入库」是反复出现的同一原则。
- 技巧 4：第二跳检索为什么值钱：第一跳的结果会改写第二跳的查询。城堡例里第一跳先认出是哪座城堡，第二跳的查询就带上了城堡名，精度完全不同；讲者的归纳是「加大单跳检索量不如加一跳」。我的思考：这解释了比较表里最反直觉的一行——检索 +8、第二跳 +18；多跳的增益不在「查得更多」，在「后面的查询被前面的结果教聪明了」。
- 技巧 5：优化前先分清「真优化」与「只是准备」。讲者特意纠正：LabeledFewShot 的 compile 只是把给定的 8 个示例塞进提示，没有任何搜索，他称更该叫 preparer 而非 optimizer；BootstrapFewShot 才有「生成—检验—筛选」的真优化。我的思考：框架词汇会夸大动作的含量，读到 compile / optimize 这类词先问一句「它到底搜索了什么、按什么指标筛」，能省掉很多误解。
- 技巧 6：assertions 是把质量要求写成可执行过滤器。查询太长（≥100 字符）或与前一跳重复的轨迹直接不许入选，换下一个。我的思考：这与第 31 条的 guardrail 是同一思想在训练期的版本——运行时用 tripwire 掐流程，构建时用 assertion 掐示例；约束前置到选择环节，比事后清洗便宜。

### 四、详细笔记（按章节）

- 2:10 为什么需要 DSPy：手工提示耗时且脆；DSPy 的独占点是优化「选哪些示例」与为多步流程自动生成端到端示例——多步/多轮场景恰是主流评测覆盖最少、手写示例最 tedious 的地方。
- 3:41 数据与语料：HotpotQA 问答集（其总规模一句被字幕截断，见存疑点）既作考题也作训练数据；检索语料是 DSPy 团队预建的 Wikipedia 索引；开发集示例带 gold titles，可量检索准不准。
- 6:08 环境：Colab 运行，LM 配置为 gpt-4o-mini（讲者口径「够强又不最贵，因为要跑很多次推理」），RM 为 ColBERTv2 指向 Wikipedia 索引（「cbear V2」为字幕乱码，判读为 ColBERTv2）；训练 20 题（设种子）、开发 50 题分开。
- 9:24 Step 1：见原样保留区 BasicQA；用 dspy.Predict 执行，lm.inspect_history(n=1) 能回看真正发出的提示全文——讲者反复用这招验货，值得学。基线 10/50 = 20%。同题在片中早段打印示例里答案作 “English”，Step 2 的 CoT 例里同一位厨师（Restaurant Impossible 的 Robert Irvine）答 “British”，两处不一致，片中未 reconciled（见存疑点）。
- 12:49 Step 2：只把 Predict 换成 ChainOfThought(BasicQA)，提示格式里多出 Reasoning 段；17/50 = 34%。讲者自己提醒样本小、误差条存在。
- 15:08 Step 3：RAG 模块见原样保留区；dspy.Retrieve(k=3)；相似度是 cosine 还是 dot，讲者明说不确定，片中未定。21/50 = 42%。
- 19:00 Step 4：LabeledFewShot 给 8 个示例；讲者明确警告训练/开发必须分开防污染（且承认两集之间可能仍有少量重叠）。27/50 = 54%。评测设置 num_threads=1、display_table=5。
- 24:00 Step 5：BootstrapFewShot 机制见技巧 3；被检查的提示里实际有 12 个短示例 + 4 条 bootstrap 轨迹，讲者当场发现与 Step 4 的 8 个对不上（「有默认数」）但未在片中解决——照录存疑。58%（此数起片中未给 /50 计数）。
- 30:30 Step 6：简化版 Baleen——GenerateSearchQuery 签名 + 每跳 ChainOfThought 生成查询、检索、上下文去重累积；无 few-shot 的两跳单测答对城堡层数题；两跳共 6 段（3 × 2）。口述一处说「do it three times」，与两跳框架及 6 段算术矛盾，以两跳为准（见存疑点）。
- 36:14 Step 7：多跳 + 优选：20 道训练题只产出 3 条合格轨迹；最终答案调用的提示里是 12 个短示例 + 3 条完整轨迹，讲者称 “extremely guided”；68%。
- 40:54 比较屏：全文见第二节；三跳单独 54% 旁注 “(?) - needs to be re-run” 是画面原样——讲者把没跑稳的数字连同警告一起留在屏上。
- 43:21 assertions：见技巧 6，例子是查询长度 < 100 字符与相邻查询不得重复。

### 五、原样保留区

BasicQA 签名（画面原文，10:01–10:02 定格完整可读）：

```python
class BasicQA(dspy.Signature):
    """Answer questions with short factoid answers."""
    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")
```

RAG 模块（画面原文，17:01 定格完整可读）：

```python
class RAG(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate_answer = dspy.ChainOfThought(GenerateAnswer)

    def forward(self, question):
        context = self.retrieve(question).passages
        prediction = self.generate_answer(context=context, question=question)
        return dspy.Prediction(context=context, answer=prediction.answer)
```

比较屏（画面原文，41:00–41:01，标题 “Model: gpt4o-mini Test Dataset: HotpotQA”）：Basic: 20% / Chain of Thought: 34% / Chain of Thought + Retrieval: 42% / Chain of Thought + Retrieval + Labelled Few-shot: 54% / Chain of Thought + Retrieval + Bootstrapped Few-shot: 58% / CoT + 2 Hop Retrieval: 60% / CoT + 2 Hop Retrieval + Bootstrapped Few-shot: 68% / Other ablations: CoT + 3 Hop Retrieval: 54% (?) - needs to be re-run。

未实测说明：视频笔记本在付费仓内，未能取得实跑；以上代码为播放器画面逐字抄录。GenerateAnswer 签名、LabeledFewShot / BootstrapFewShot 的调用、Baleen 的 forward 循环、assertions 的代码在画面上均不可读，只按口述层记录于详细笔记，不拼凑代码。

### 六、术语表

- Signature：DSPy 的声明式输入/输出定义，字段描述即提示的一部分，由框架生成具体格式。
- Module / Predict / ChainOfThought：Module 是可组合的程序骨架（forward 写法借自 PyTorch）；Predict 是最基本的执行器，ChainOfThought 是要求先推理再作答的执行器。
- Teleprompter：DSPy 对优化器的统称（现行版本称 optimizer）；负责 compile——按指标在训练集上搜索更好的提示/示例组合。
- BootstrapFewShot：通过自举筛选示例的优化器——只保留在训练集上全程跑对了的轨迹作为 few-shot 示例。
- Baleen：DSPy 早期论文里的多跳检索问答系统，本片用的是其简化复刻：逐跳生成查询、检索、累积上下文。

### 七、易错点和存疑点

- **分数引用边界**：全部数字是 50 道开发题、exact match、单个模型（gpt4o-mini）的一次运行；讲者多次提醒误差条。58% 与 68% 片中没给对题计数，不要反推成 29/50、34/50 当作片中原数——只能说比例一致。
- **三跳 54% 是未定数**：画面自带 “(?) - needs to be re-run”，引用时必须带这句警告；三跳 + 优选 66% 只有口述、无画面。
- **同一厨师两个答案**：早段示例打印 “English”，Step 2 对 Restaurant Impossible 厨师答 “British”；是否同一道题片中没说清，两处照录、不调和。
- **HotpotQA 规模一句不可用**：3:59 字幕在 “it's got 100 uh question answer pairs” 处截断，总量未核实，笔记只记实际用到的 20 训练 + 50 开发。
- **Step 5 示例数对不上**：Step 4 用 8 个，Step 5 检视到的提示里数出 12 个短示例，讲者自己没解释清默认值来源；照录存疑。
- **hop 数口述矛盾**：32:12 说生成查询「three times」，但全章按两跳演示且检索段数 6 = 3 × 2，以两跳为准。
- 简介把免费的官方 intro.ipynb 与付费仓里的本片笔记本并列罗列，未标明哪个链接对应哪个；按口述层为准：本片笔记本要付费仓权限。
- 检索相似度（cosine / dot）讲者明说不确定，片中无代码行可判，未核实。

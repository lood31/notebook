# notebook

我看 YouTube 学 AI agent 技巧时做的核验型笔记：一共 50 条视频，每条都不是「看完凭印象记」，而是播放器实播、字幕逐段读、代码实跑、说法逐项查证之后才落笔。

![videos](https://img.shields.io/badge/视频-50条-blue) ![verified](https://img.shields.io/badge/核验-2026--11--01-green) ![format](https://img.shields.io/badge/格式-Markdown-lightgrey)

**一句话证据**：50 条视频的时长全部由播放器实测；有配套仓库的逐个克隆对照——Claude Code hooks 的拦截函数离线实跑、TypeScript coding agent 的仓库测试实跑 22/22 通过、Managed Agents 的离线测试通过且演示数字拿原始 CSV 独立复算、RAG 源码与画面逐字一致。第三批首组把 CrewAI 仓库里一次真实运行的产物拿来实跑：它生成的测试文件连导入都过不了，当场拆穿「全自动保质量」的简介口径；第二组里 JARVIS 仓库的人写测试同样倒在导入上——第一行就把常量名拼错。第三组把一条 2024 年的 Mem0 教程与现行官方文档对照：视频里「嵌入离不开 OpenAI」的核心限制如今已被官方修复，教程标题写的「本地」在当时只成立一半；另一堂 LangSmith 评估课则被配套仓库救了回来——画面里读不清的代码、连数据源是哪三篇博客，都由仓库原文逐项解明。第四组里 Pydantic 的两个模型类被拿来在本地按现行版本实跑：坏输入与总额对不上的发票都被正确拦下；一条只有动画素描的 AI 生成「部署教程」则被官方现行 compose 文件反向核对——流程属实、画面不作数，全部写进笔记。每条笔记末尾都有「存疑点」，查不到的东西明说查不到，不拿猜测冒充原文。

## 目录

笔记按每 5 条拆成一份文档，每份文档内部也各带目录。

### 视频 1–5 — [youtube-agent-notes-1-5.md](youtube-agent-notes-1-5.md)

- [视频 1｜Claude Code 创造者本人的工作流（Snapper AI）](youtube-agent-notes-1-5.md#视频-1how-the-creator-of-claude-code-sets-up-his-workflow-setup-tutorial) — 发布 2026-01-03
- [视频 2｜Claude Code 五种编排模式（Simon Scrapes）](youtube-agent-notes-1-5.md#视频-2every-claude-code-workflow-explained--when-to-use-each) — 发布 2026-04-07
- [视频 3｜Google EGM：全上下文写计划 + 零上下文审查（AI LABS）](youtube-agent-notes-1-5.md#视频-3egm-googles-new-way-to-use-claude-code-is-insane) — 发布 2026 年 10 月下旬
- [视频 4｜一次讲透 AI Agent 的用法和技巧（數碼旭）](youtube-agent-notes-1-5.md#视频-4效率翻倍一次講透-ai-agent-的用法和技巧) — 发布 2026-09-12
- [视频 5｜资深工程师怎么做 AI Agent：7 条工程经验（ByteGrad）](youtube-agent-notes-1-5.md#视频-5how-senior-engineers-build-ai-agents-7-tips) — 录制 2026-10-28 前后

### 视频 6–10 — [youtube-agent-notes-6-10.md](youtube-agent-notes-6-10.md)

- [视频 6｜2026 年 agent 平台横评：Claude / Codex / OpenClaw / Antigravity（AI Master）](youtube-agent-notes-6-10.md#视频-6ai-agents-explained-how-to-create-and-use-ai-agents-in-2026) — 发布 2026-05-13
- [视频 7｜OpenAI《构建 Agent 实用指南》代码讲解（NeoTridhi）](youtube-agent-notes-6-10.md#视频-7openais-practical-guide-to-building-agents-explained-with-code) — 发布 2026-10-18
- [视频 8｜Anthropic《构建有效 Agent》讲解：五种可组合模式（Cloud Codes）](youtube-agent-notes-6-10.md#视频-8how-to-build-ai-agents-that-actually-work) — 发布 2026-06-23
- [视频 9｜让 AI 性能爆涨的 7 个神技（いまにゅ）](youtube-agent-notes-6-10.md#视频-9やるだけで-ai-の性能を爆上げする神技-7-選) — 发布 2026-09-30
- [视频 10｜用 AI 快速精读论文的方法（Dr Amina Yonis）](youtube-agent-notes-6-10.md#视频-10how-to-read-a-paper-quickly--effectively-with-ai) — 发布 2023-10-09

### 视频 11–15 — [youtube-agent-notes-11-15.md](youtube-agent-notes-11-15.md)

- [视频 11｜Why AI Agents Forget（Peace Of Code）](youtube-agent-notes-11-15.md#视频-11--why-ai-agents-forgetclaude-certified-architect-ep-18) — 发布 2026-06-02
- [视频 12｜Context Engineering for Agents（LangChain）](youtube-agent-notes-11-15.md#视频-12--context-engineering-for-agentslangchain) — 发布 2025-07-02
- [视频 13｜The Memory Tool: Letta 自我重构的 agent（Letta）](youtube-agent-notes-11-15.md#视频-13--the-memory-tool-letta-agents-that-redesign-their-own-architecture) — 发布 2025-10-06
- [视频 14｜李宏毅《AI Agent 的原理》](youtube-agent-notes-11-15.md#视频-14--李宏毅ai-agent-的原理) — 发布 2025-03-09
- [视频 15｜Why Prompt Injection Can't Be Filtered Away（scrollypedia）](youtube-agent-notes-11-15.md#视频-15--why-prompt-injection-cant-be-filtered-away) — 发布 2026-10-21

### 视频 16–20 — [youtube-agent-notes-16-20.md](youtube-agent-notes-16-20.md)

- [视频 16｜I'm HOOKED on Claude Code Hooks（IndyDevDan）](youtube-agent-notes-16-20.md#16-im-hooked-on-claude-code-hooks) — 发布 2025-07-07
- [视频 17｜How to Build Claude Code Skills（MaxDevAi）](youtube-agent-notes-16-20.md#17-how-to-build-claude-code-skills) — 发布 2026-10-19
- [视频 18｜Building more effective AI agents（Anthropic）](youtube-agent-notes-16-20.md#18-building-more-effective-ai-agents) — 发布 2025-10-18
- [视频 19｜This is Hands Down the BEST Way to Build AI Agents（Cole Medin）](youtube-agent-notes-16-20.md#19-this-is-hands-down-the-best-way-to-build-ai-agents) — 发布 2025-02-13
- [视频 20｜MCP Server Explained & Built: FastAPI to MCP in Just 2 Lines（ConnectXeo）](youtube-agent-notes-16-20.md#20-mcp-server-explained--built-fastapi-to-mcp-in-just-2-lines) — 发布 2026-10-26

### 视频 21–25 — [youtube-agent-notes-21-25.md](youtube-agent-notes-21-25.md)

- [视频 21｜How the harness actually works?（Hilay Trivedi）](youtube-agent-notes-21-25.md#21-how-the-harness-actually-works-) — 发布 2026-10-27
- [视频 22｜Building a Coding Agent（Code with Andrew Ford）](youtube-agent-notes-21-25.md#22-building-a-coding-agent) — 发布 2026-10-25
- [视频 23｜claude managed agents explained by example（zero cost ai）](youtube-agent-notes-21-25.md#23-claude-managed-agents-explained-by-example) — 发布 2026-10-20
- [视频 24｜I Built a Deep Research AI Agent with LangGraph（MLTut）](youtube-agent-notes-21-25.md#24-i-built-a-deep-research-ai-agent-with-langgraph) — 发布 2026-09-07
- [视频 25｜Make Your AI Agent Search Documents | RAG Part 2（codestorywithMIK）](youtube-agent-notes-21-25.md#25-make-your-ai-agent-search-documents--rag-part-2) — 发布 2026-10-28

### 视频 26–30 — [youtube-agent-notes-26-30.md](youtube-agent-notes-26-30.md)

- [视频 26｜Stop using Claude. Start using Codex?（Greg Isenberg × Riley Brown）](youtube-agent-notes-26-30.md#26-stop-using-claude-start-using-codex) — 发布 2026-04-28
- [视频 27｜What Actually Makes Claude Code Work (It's Not the Model)（Microlearn）](youtube-agent-notes-26-30.md#27-what-actually-makes-claude-code-work-its-not-the-model) — 发布 2026-10-27
- [视频 28｜Claude Code in 20 Minutes: Build a Real App (and Catch Its Bugs)（Inside IT）](youtube-agent-notes-26-30.md#28-claude-code-in-20-minutes-build-a-real-app-and-catch-its-bugs) — 发布 2026-10-24
- [视频 29｜How to Build Multi AI Agents with Langgraph（Kenny Liao）](youtube-agent-notes-26-30.md#29-how-to-build-multi-ai-agents-with-langgraph) — 发布 2025-09-27
- [视频 30｜How to Build Agents That Improve Over Time（Harrison Chase）](youtube-agent-notes-26-30.md#30-how-to-build-agents-that-improve-over-time-model-harness-context) — 发布 2026-10-28

### 视频 31–35 — [youtube-agent-notes-31-35.md](youtube-agent-notes-31-35.md)

- [视频 31｜OpenAI Agents SDK 三级实作（The AI Lifestyle）](youtube-agent-notes-31-35.md#31-master-openais-agents-sdk-in-just-25-minutes--build-powerful-agentic-ai-apps-with-streamlit) — 发布 2025-03-17
- [视频 32｜smolagents 的 CodeAgent 与 agentic RAG（Prompt Engineering）](youtube-agent-notes-31-35.md#32-smolagents-a-smol-library-to-build-agents) — 发布 2025-01-02
- [视频 33｜CrewAI 搭四人工程团队（PlayOwnAi）](youtube-agent-notes-31-35.md#33-ep6-build-an-ai-engineering-team-with-crewai--multi-agent-system-in-python) — 发布 2026-01-17
- [视频 34｜AutoGen 入门（Simplilearn）](youtube-agent-notes-31-35.md#34-autogen-tutorial-for-beginners-2026--microsoft-autogen-tutorial--autogen-studio--simplilearn) — 发布 2026-03-09
- [视频 35｜Google ADK 一小时全链路（The AI Lifestyle）](youtube-agent-notes-31-35.md#35-master-googles-agents-development-kit-adk-in-60-mins-build-production-agentic-workflows-today) — 发布 2025-04-17

### 视频 36–40 — [youtube-agent-notes-36-40.md](youtube-agent-notes-36-40.md)

- [视频 36｜Vercel AI SDK 客服 agent：先裸写 RAG 再换 SDK 抽象（freeCodeCamp）](youtube-agent-notes-36-40.md#36-build-a-support-agent-with-vercel-ai-sdk--full-tutorial) — 发布 2025-12-24
- [视频 37｜LiveKit + Gemini 实时模型搭 JARVIS 语音 agent（Thanh-y David Nguyen）](youtube-agent-notes-36-40.md#37-how-to-build-a-jarvis-ai-voice-agent-for-free--full-livekit-tutorial-2026) — 发布 2026-09-13
- [视频 38｜Browser Use 入门：接管本机浏览器与结构化抓取（Tech With Tim）](youtube-agent-notes-36-40.md#38-browser-use-this-new-ai-agent-can-do-anything-full-ai-scraping-tutorial) — 发布 2025-03-23
- [视频 39｜A2A 协议 v1.0 逐步拆解（AI Build Along）](youtube-agent-notes-36-40.md#39-a2a-vs-mcp-explained-the-a2a-protocol-v10-step-by-step-real-messages) — 发布 2026-10-18
- [视频 40｜DSPy 自动提示工程：七级叠加实验（Trelis Research）](youtube-agent-notes-36-40.md#40-automated-prompt-engineering-with-dspy) — 发布 2024-09-12

### 视频 41–45 — [youtube-agent-notes-41-45.md](youtube-agent-notes-41-45.md)

- [视频 41｜Mem0 + Ollama 本地记忆：一次 add 写入两条、嵌入仍走云端（Fahd Mirza）](youtube-agent-notes-41-45.md#41-install-mem0-with-ollama-locally---a-hands-on-tutorial) — 发布 2024-08-05
- [视频 42｜MCP 安全全课：致命三件套与 OWASP MCP Top 10 检查表（freeCodeCamp）](youtube-agent-notes-41-45.md#42-mcp-security-fundamentals-course) — 发布 2026-10-22
- [视频 43｜用编程 agent 经 CLI 建 LangSmith 在线评审（LangChain）](youtube-agent-notes-41-45.md#43-build-an-online-llm-as-a-judge-with-the-langsmith-cli-and-a-coding-agent) — 发布 2026-09-10
- [视频 44｜LangSmith 聊天与 RAG 评估实作：四个评估器按「谁和谁比」拆（Krish Naik）](youtube-agent-notes-41-45.md#44-mastering-llm-chatbots-and-rag-evaluation-crash-course) — 发布 2026-03-02
- [视频 45｜Langfuse 官方走查：从 trace 到 CI 发布门的工程闭环（Langfuse）](youtube-agent-notes-41-45.md#45-langfuse-walkthrough---full-demo-of-agent-tracing-evals-experiments-and-prompt-management) — 发布 2026-09-18

### 视频 46–50 — [youtube-agent-notes-46-50.md](youtube-agent-notes-46-50.md)

- [视频 46｜给 Deep Research Agent 上持久化：进程被杀也不重花钱（Temporal）](youtube-agent-notes-46-50.md#46-build-a-deep-research-agent--python-openai-temporal) — 发布 2026-02-26
- [视频 47｜Pydantic：概率与确定之间的契约（Imad 4 Automation）](youtube-agent-notes-46-50.md#47-pydantic-explained-for-ai-engineers--pydantic-ai-agents) — 发布 2026-10-22
- [视频 48｜n8n 无代码邮件 agent：三条工作流与一次真实的首测失败（Nate Herk）](youtube-agent-notes-46-50.md#48-how-to-create-an-ai-email-agent-with-n8n-no-code-step-by-step-tutorial) — 发布 2024-09-21
- [视频 49｜Senior SWE-Bench：按维护者标准给编码 agent 打分（Snorkel AI）](youtube-agent-notes-46-50.md#49-senior-swe-bench-evaluating-coding-agents--snorkel-ai-reading-group) — 发布 2026-07-17
- [视频 50｜Langfuse 自托管部署清单（AI 生成内容，画面不作证据）（Alex Hitt）](youtube-agent-notes-46-50.md#50-langfuse-github-explained-self-hosted-llm-observability-with-openai-tracing) — 发布 2026-10-22

### 视频 51–55 — [youtube-agent-notes-51-55.md](youtube-agent-notes-51-55.md)

- [视频 51｜Claude Agent SDK 工作坊：章节标题与内容错位，干货在验证循环与 bash 哲学（AI Engineer）](youtube-agent-notes-51-55.md#51-claude-agent-sdk-full-workshop--thariq-shihipar-anthropic) — 发布 2026-01-06
- [视频 52｜Microsoft Agent Framework 完整课：记忆与上下文是重心，结尾缺章（KodeKloud）](youtube-agent-notes-51-55.md#52-microsoft-agent-framework-tutorial-2025--complete-coursekodekloud) — 发布 2025-11-20
- [视频 53｜OpenAI Agents SDK 进阶：审批中断续跑、handoff 与输入护栏，无配套仓库（NeuralNine）](youtube-agent-notes-51-55.md#53-openai-agents-sdk-crash-course--agent-workflows-in-pythonneuralnine) — 发布 2026-10-27
- [视频 54｜把 Skills 装进任何 agent：三层渐进披露，评测专查加载行为（Cole Medin）](youtube-agent-notes-51-55.md#54-claude-skills-arent-just-for-claude--heres-how-to-build-them-for-any-agentcole-medin) — 发布 2026-01-29
- [视频 55｜住在 iMessage 里的个人助理：六件套与回 yes/no 即审批（Kristian，Mastra 赞助）](youtube-agent-notes-51-55.md#55-i-built-my-own-ai-agent-in-imessage-like-dots-grok-bot--musekristian) — 发布 2026-10-21

### 视频 56–60 — [youtube-agent-notes-56-60.md](youtube-agent-notes-56-60.md)

- [视频 56｜AG2 入门：从单智能体到贷款预筛四角色流水线（Venkata Reddy AI Classes）](youtube-agent-notes-56-60.md#56-genai--agentic-ai--video-9--auto-genvenkata-reddy-ai-classes) — 首播 2026-02-14
- [视频 57｜状态管理与检查点完全解讀：六分钟概念地图，标题中文、内容英文（ErrorFixer）](youtube-agent-notes-56-60.md#57-ai-智慧體的狀態管理與檢查點完全解讀--圖記憶hitl-與多框架對比errorfixer) — 发布 2026-09-02
- [视频 58｜用并行 agent 做大规模重构：拆分、隔离、PR 汇总与真实排障（AI Engineer）](youtube-agent-notes-56-60.md#58-automating-large-scale-refactors-with-parallel-agents--robert-brennan-openhands) — 发布 2026-01-09
- [视频 59｜Browser agent 实跑边界：Claude 版只测到 API、免费实跑是 Gemini（Tech with Pandit）](youtube-agent-notes-56-60.md#59-claude-haiku-55-i-built-a-browser-agent-and-ran-it-for-freetech-with-pandit) — 发布 2026-10-21
- [视频 60｜SWE-agent 的 ACI：为模型定制界面与四组消融数字（CoreWeave）](youtube-agent-notes-56-60.md#60-neurips-hacker-cup-ai-sweagentcoreweave) — 直播 2024-08-15

### 视频 61–65 — [youtube-agent-notes-61-65.md](youtube-agent-notes-61-65.md)

- [视频 61｜MCP 客户端 OAuth 全流程：401 先发现、CIMD 注册、S256 与 step-up（AISLabs AI）](youtube-agent-notes-61-65.md#61-mcp-oauth-client-side-discovery-cimd-pkce-and-step-upaislabs-ai) — 发布 2026-10-28
- [视频 62｜MCP 服务器认证四法：从共用密钥到 JWT 落地，OAuth 只讲概念（The Debug Arena）](youtube-agent-notes-61-65.md#62-mcp-authentication-in-nodejs-4-methods-explainedthe-debug-arena) — 发布 2026-10-21
- [视频 63｜Docker MCP 目录与网关：一次配置、多处复用，凭证收拢不消失（Python Simplified）](youtube-agent-notes-61-65.md#63-stop-sharing-api-keys-with-llms---use-docker-mcp-catalog-insteadpython-simplified) — 首播 2026-01-17
- [视频 64｜Letta Evals：冻结 agent 状态做评测，与栽在重音符号上的评分（Letta）](youtube-agent-notes-61-65.md#64-how-to-evaluate-stateful-agents-starter-guide-to-letta-evalsletta) — 发布 2025-11-01
- [视频 65｜AG-UI 事件流与生成式 UI 三档：受控、声明、开放（Mastra）](youtube-agent-notes-61-65.md#65-agentic-ux-three-ways-to-let-an-agent-build-its-own-uimastra) — 发布 2026-09-07

## 横向概念索引（全 65 条）

同一概念在多条视频里反复出现时，按主题横着看比按编号顺着看更快。每组给出代表条目（链接直达该条笔记小节）：

- **记忆与上下文**：记忆为何丢失 [11](youtube-agent-notes-11-15.md#视频-11--why-ai-agents-forgetclaude-certified-architect-ep-18)、上下文工程 [12](youtube-agent-notes-11-15.md#视频-12--context-engineering-for-agentslangchain)、Letta 记忆工具 [13](youtube-agent-notes-11-15.md#视频-13--the-memory-tool-letta-agents-that-redesign-their-own-architecture)、Mem0 本地记忆与版本史读法 [41](youtube-agent-notes-41-45.md#41-install-mem0-with-ollama-locally---a-hands-on-tutorial)、MAF 的 thread 与 context provider [52](youtube-agent-notes-51-55.md#52-microsoft-agent-framework-tutorial-2025--complete-coursekodekloud)、永恒对话的 observational memory [55](youtube-agent-notes-51-55.md#55-i-built-my-own-ai-agent-in-imessage-like-dots-grok-bot--musekristian)、检查点（线程内）与 Store（跨线程）的分工 [57](youtube-agent-notes-56-60.md#57-ai-智慧體的狀態管理與檢查點完全解讀--圖記憶hitl-與多框架對比errorfixer)
- **框架横评与编排**：五种编排模式 [2](youtube-agent-notes-1-5.md#视频-2every-claude-code-workflow-explained--when-to-use-each)、平台横评 [6](youtube-agent-notes-6-10.md#视频-6ai-agents-explained-how-to-create-and-use-ai-agents-in-2026)、Anthropic 可组合模式 [8](youtube-agent-notes-6-10.md#视频-8how-to-build-ai-agents-that-actually-work) 与 [18](youtube-agent-notes-16-20.md#18-building-more-effective-ai-agents)、框架五连（OpenAI Agents SDK / smolagents / CrewAI / AutoGen / Google ADK）[31–35](youtube-agent-notes-31-35.md)、DSPy 把提示当程序编译 [40](youtube-agent-notes-36-40.md)、Claude Agent SDK 的循环与工具分工 [51](youtube-agent-notes-51-55.md#51-claude-agent-sdk-full-workshop--thariq-shihipar-anthropic)、Microsoft Agent Framework [52](youtube-agent-notes-51-55.md#52-microsoft-agent-framework-tutorial-2025--complete-coursekodekloud)、OpenAI Agents SDK 进阶部件（handoffs / 护栏 / 审批）[53](youtube-agent-notes-51-55.md#53-openai-agents-sdk-crash-course--agent-workflows-in-pythonneuralnine)、AG2 的多智能体递进 [56](youtube-agent-notes-56-60.md#56-genai--agentic-ai--video-9--auto-genvenkata-reddy-ai-classes)
- **编码 agent 与并行重构**：手写编码 agent [22](youtube-agent-notes-21-25.md#22-building-a-coding-agent)、Claude Code 实作与抓虫 [28](youtube-agent-notes-26-30.md#28-claude-code-in-20-minutes-build-a-real-app-and-catch-its-bugs)、并行 agent 大规模重构（拆分、隔离、PR 汇总）[58](youtube-agent-notes-56-60.md#58-automating-large-scale-refactors-with-parallel-agents--robert-brennan-openhands)、SWE-agent 的 ACI 与消融 [60](youtube-agent-notes-56-60.md#60-neurips-hacker-cup-ai-sweagentcoreweave)
- **Skills 与渐进披露**：Claude Code Skills 的写法 [17](youtube-agent-notes-16-20.md)、把 Skills 装进任何 agent（三层披露 + 动态系统提示）[54](youtube-agent-notes-51-55.md#54-claude-skills-arent-just-for-claude--heres-how-to-build-them-for-any-agentcole-medin)
- **个人助理与通道**：住在 iMessage 的六件套助理 [55](youtube-agent-notes-51-55.md#55-i-built-my-own-ai-agent-in-imessage-like-dots-grok-bot--musekristian)、LiveKit 语音管家 [37](youtube-agent-notes-36-40.md)
- **工具与协议**：MCP 两分钟上手 [20](youtube-agent-notes-16-20.md#20-mcp-server-explained--built-fastapi-to-mcp-in-just-2-lines)、MCP 安全全课 [42](youtube-agent-notes-41-45.md#42-mcp-security-fundamentals-course)、A2A 协议 [39](youtube-agent-notes-36-40.md)、浏览器操作 browser-use [38](youtube-agent-notes-36-40.md)、Agents SDK 经 MCP 接文件系统 [53](youtube-agent-notes-51-55.md#53-openai-agents-sdk-crash-course--agent-workflows-in-pythonneuralnine)、为模型定制的界面 ACI [60](youtube-agent-notes-56-60.md#60-neurips-hacker-cup-ai-sweagentcoreweave)、MCP 客户端 OAuth 全流程 [61](youtube-agent-notes-61-65.md#61-mcp-oauth-client-side-discovery-cimd-pkce-and-step-upaislabs-ai)、MCP 服务器认证四法 [62](youtube-agent-notes-61-65.md#62-mcp-authentication-in-nodejs-4-methods-explainedthe-debug-arena)、Docker MCP 目录与网关 [63](youtube-agent-notes-61-65.md#63-stop-sharing-api-keys-with-llms---use-docker-mcp-catalog-insteadpython-simplified)、AG-UI 事件流协议 [65](youtube-agent-notes-61-65.md#65-agentic-ux-three-ways-to-let-an-agent-build-its-own-uimastra)
- **结构化输出与校验**：Pydantic 边界契约 [47](youtube-agent-notes-46-50.md#47-pydantic-explained-for-ai-engineers--pydantic-ai-agents)、DSPy 的签名与编译 [40](youtube-agent-notes-36-40.md)、函数签名即工具定义在 [33](youtube-agent-notes-31-35.md) 与 [48](youtube-agent-notes-46-50.md#48-how-to-create-an-ai-email-agent-with-n8n-no-code-step-by-step-tutorial) 的两种实现、output_type 类型约束 [53](youtube-agent-notes-51-55.md#53-openai-agents-sdk-crash-course--agent-workflows-in-pythonneuralnine)
- **评估与基准**：LangSmith 在线评审 [43](youtube-agent-notes-41-45.md#43-build-an-online-llm-as-a-judge-with-the-langsmith-cli-and-a-coding-agent)、聊天与 RAG 四评估器 [44](youtube-agent-notes-41-45.md#44-mastering-llm-chatbots-and-rag-evaluation-crash-course)、Senior SWE-Bench 的品味评分 [49](youtube-agent-notes-46-50.md#49-senior-swe-bench-evaluating-coding-agents--snorkel-ai-reading-group)、评测专查技能加载行为 [54](youtube-agent-notes-51-55.md#54-claude-skills-arent-just-for-claude--heres-how-to-build-them-for-any-agentcole-medin)、pass@1 口径与消融纪律 [60](youtube-agent-notes-56-60.md#60-neurips-hacker-cup-ai-sweagentcoreweave)、自测冒充独立基准的识别 [59](youtube-agent-notes-56-60.md#59-claude-haiku-55-i-built-a-browser-agent-and-ran-it-for-freetech-with-pandit)、有状态 agent 评测与量尺校准 [64](youtube-agent-notes-61-65.md#64-how-to-evaluate-stateful-agents-starter-guide-to-letta-evalsletta)
- **观测与追踪**：Langfuse 官方全景 [45](youtube-agent-notes-41-45.md#45-langfuse-walkthrough---full-demo-of-agent-tracing-evals-experiments-and-prompt-management)、Langfuse 自托管运维 [50](youtube-agent-notes-46-50.md#50-langfuse-github-explained-self-hosted-llm-observability-with-openai-tracing)、LangSmith 的 trace 与实验 [43](youtube-agent-notes-41-45.md#43-build-an-online-llm-as-a-judge-with-the-langsmith-cli-and-a-coding-agent) / [44](youtube-agent-notes-41-45.md#44-mastering-llm-chatbots-and-rag-evaluation-crash-course)、多次运行合并成一个 trace [53](youtube-agent-notes-51-55.md#53-openai-agents-sdk-crash-course--agent-workflows-in-pythonneuralnine)、Logfire 两行接入 [54](youtube-agent-notes-51-55.md#54-claude-skills-arent-just-for-claude--heres-how-to-build-them-for-any-agentcole-medin)
- **安全**：提示注入不可过滤 [15](youtube-agent-notes-11-15.md#视频-15--why-prompt-injection-cant-be-filtered-away)、MCP 致命三件套与 Top 10 [42](youtube-agent-notes-41-45.md#42-mcp-security-fundamentals-course)、browser-use 的敏感信息隔离 [38](youtube-agent-notes-36-40.md)、基准的运行时反作弊 [49](youtube-agent-notes-46-50.md#49-senior-swe-bench-evaluating-coding-agents--snorkel-ai-reading-group)、多层防御与风险组合拆解 [51](youtube-agent-notes-51-55.md#51-claude-agent-sdk-full-workshop--thariq-shihipar-anthropic)、输入护栏 tripwire [53](youtube-agent-notes-51-55.md#53-openai-agents-sdk-crash-course--agent-workflows-in-pythonneuralnine)、浏览器能力与脚本执行的混装风险 [59](youtube-agent-notes-56-60.md#59-claude-haiku-55-i-built-a-browser-agent-and-ran-it-for-freetech-with-pandit)、OAuth 客户端的发现与不降级纪律 [61](youtube-agent-notes-61-65.md#61-mcp-oauth-client-side-discovery-cimd-pkce-and-step-upaislabs-ai)、服务器认证的凭证选型 [62](youtube-agent-notes-61-65.md#62-mcp-authentication-in-nodejs-4-methods-explainedthe-debug-arena)、凭证收拢到网关 [63](youtube-agent-notes-61-65.md#63-stop-sharing-api-keys-with-llms---use-docker-mcp-catalog-insteadpython-simplified)
- **持久化与生产**：Temporal 持久化深度研究 [46](youtube-agent-notes-46-50.md#46-build-a-deep-research-agent--python-openai-temporal)、Managed Agents 托管运行 [23](youtube-agent-notes-21-25.md#23-claude-managed-agents-explained-by-example)、Langfuse 自托管 [50](youtube-agent-notes-46-50.md#50-langfuse-github-explained-self-hosted-llm-observability-with-openai-tracing)、个人助理的部署两处改动 [55](youtube-agent-notes-51-55.md#55-i-built-my-own-ai-agent-in-imessage-like-dots-grok-bot--musekristian)、检查点支撑恢复、HITL 与时间旅行 [57](youtube-agent-notes-56-60.md#57-ai-智慧體的狀態管理與檢查點完全解讀--圖記憶hitl-與多框架對比errorfixer)、事件流支撑多前端表面复用 [65](youtube-agent-notes-61-65.md#65-agentic-ux-three-ways-to-let-an-agent-build-its-own-uimastra)
- **语音与多模态**：LiveKit 语音管家 JARVIS [37](youtube-agent-notes-36-40.md)
- **无代码与低代码**：n8n 三条工作流拼 agent [48](youtube-agent-notes-46-50.md#48-how-to-create-an-ai-email-agent-with-n8n-no-code-step-by-step-tutorial)、Vercel AI SDK 客服 agent [36](youtube-agent-notes-36-40.md)
- **研究型 agent**：LangGraph 深度研究 [24](youtube-agent-notes-21-25.md#24-i-built-a-deep-research-ai-agent-with-langgraph)、Temporal 版深度研究 [46](youtube-agent-notes-46-50.md#46-build-a-deep-research-agent--python-openai-temporal)、多 agent 监督者模式 [29](youtube-agent-notes-26-30.md#29-how-to-build-multi-ai-agents-with-langgraph)
- **自我改进**：随时间变强的 agent [30](youtube-agent-notes-26-30.md#30-how-to-build-agents-that-improve-over-time-model-harness-context)、Letta 自我重构 [13](youtube-agent-notes-11-15.md#视频-13--the-memory-tool-letta-agents-that-redesign-their-own-architecture)

## 每条笔记的结构

七个固定部分：① 元信息（含发布时间）② 一句话结论 + 3 句摘要 ③ 技巧总结（每条带我的反驳性思考，外加一条「时效性」评估）④ 按章节的详细笔记 ⑤ 原样保留区（公式、代码、步骤不压缩；代码实跑核对、跑不了的标「未实测」）⑥ 术语表 ⑦ 易错点和存疑点。

## 取材与核验标准

- 字幕、时间戳、代码原文三样拿不齐的视频不写笔记，宁缺毋滥。
- 看不清的地方可以根据上下文推断，但必须显式标「推断」，与原文分开。
- 代码原文以视频简介的仓库对照 + 截图读码 + 实跑验证为准；公式实际计算核对；价格、星数等外部数字按官网或一手页面复核。

## 时效性说明

AI 工具迭代很快，三个月前的技巧可能已经拖后腿。所以每条笔记都标了发布时间，并附带日期的「时效性」评估：哪些是与版本无关的耐久方法，哪些是当时的工具细节、价格或实验功能口径——后者用之前请先按当前版本复核。最典型的是视频 10（2023 年）：读法至今可用，片中的 AI 工具界面与定价已经过时。

## 已知局限

- 视频 3、视频 5 的确切发布日期未能证实，笔记里给的是带依据的推断窗口，没有编造确切日期。
- 视频 5 的部分代码被字幕条遮挡，遮挡处在笔记中明确标出；整段工程依赖其 SDK，未能完整实跑。
- 视频 9 演讲者的真实姓名、所提 TDD skill 的归属，多个来源互相冲突，笔记保留为「未核实」，没有采信任何一方。
- EGM 插件本身未实际安装试用，笔记中的相关警告如实保留。
- 视频 19 的配套仓库已被作者整体重写，视频当期的 Python 代码已不在仓库中，笔记按播放器画面记录并标注此事。
- 视频 24 没有配套仓库，代码证据全部来自播放器画面，证据等级低于其他条目，笔记中已注明。
- 视频 26 是访谈节目，标题问题在节目结尾没有给出是或否的结论；笔记按两位出场者各自的原话记录，不替节目下结论。视频中的价格数字为节目口径，未做外部核价。
- 视频 28 的终端会话是作者为清晰而重做的演示（视频自述），不是原始执行日志；笔记已在对应条目标注。
- 视频 41 是 2024 年的版本状态：其核心限制（嵌入必须用 OpenAI）已被现行 Mem0 修复，笔记按版本史读法处理，引用时须带年份。
- 视频 48 的 Pinecone namespace 名与收件人姓氏在字幕与画面之间无法定案，笔记分别标未决与推断；视频 50 是 AI 生成内容、画面为动画素描，笔记已把证据等级单独标明，其事实改引官方仓库与第 45 条。
- 视频 42、45 的多数幻灯片正文与界面小字不可读，笔记以口述与可读画面为准；视频 42 的事件与统计数字除 Postmark 案外未逐一外查，视频 44 的实验分数在画面与仓库两处都无法定案，均已在各条笔记的存疑点中标明。
- 视频 51、52 的章节标题与实际内容系统性错位（51 至少三处、52 至少七处），两条笔记均按实际内容分段，并以简介置顶评论（51）与实验界面（52）为定位辅助；视频 52 名为 Conclusion 的结尾段并无总结，多智能体与生产收尾在全片缺席。
- 视频 53 没有配套仓库，代码证据来自画面短片段与官方文档，证据等级低于有仓库的条目；其示例邮箱、UID 与默认模型型号未能逐字核实，未写入笔记作为事实。
- 视频 55 是 Mastra 赞助内容（已披露），且其仓库现状比视频时点更新（多出可观测等部件）；笔记以视频为主体、仓库为结构对照，仓库只读未运行。
- 视频 54 的工具层与评测脚本需要 pydantic_ai 与模型密钥，本轮只实跑了加载器与其单测（14 个用例全过），其余部分为克隆对照与编译核验，均已在笔记中标注。
- 视频 56 的「微软付费版 vs 开源 AG2」二分与行业采用率均为讲者说法、片中无证据来源，笔记只记录说法；其 Colab 笔记本本轮未打开核对（需 Google 登录），内容与画面的一致性未核实。
- 视频 57 是纯概念片：无配套代码、简介无外部链接；其六框架对比表自注「特征摘要已简化」，笔记按片中说法降级引用，落地前需回官方文档核对。
- 视频 58 的幻灯片短链已失效，演示者（Calvin）姓氏未核实，尾段音频差、字幕碎片化；其星数、贡献者数为演讲口头量级，未核对实时数据。
- 视频 59 的配套仓库链接经核查已失效（404），代码无法离线对照，档位已由 A 下调为 B 并在笔记显著披露；其标题与实跑主体不一致（免费实跑是 Gemini 而非 Claude），作者在片中已自行划清边界，笔记据此引用。
- 视频 60 是 2024 年的直播：排行榜一章没有任何具体分数，基线按方法写 4%–5% 区间；简介无仓库与幻灯片链接，多处命令字符串在转录稿中被截断，均已在笔记标注。
- 视频 61 的材料层弱：简介无仓库无文档外链、命令未在画面逐行核对，档位已降为 B− 并在笔记显著披露；其标注的规范日期未核对规范原文。
- 视频 62 的 OAuth 2.1 只讲概念未实现，仓库两份服务文件只做了语法检查、未装依赖实跑，运行行为以片中端到端实测为准。
- 视频 63 未在本机实跑 Docker 链路（无 Docker 环境）；其播放器的付费宣传标记无对应口播段落，赞助方未核实、未指认；目录短链落地页未展开核实。
- 视频 64 的两轮运行成绩（视频轮与仓库提交轮）已分开标注；框架未实际运行（需 Letta 服务与模型密钥），数字以视频演示与仓库提交的结果文件为准。
- 视频 65 章节名所称的 18 个事件未逐一清点，完整事件表未核实；自学习 SDK 与原生 SDK 的发布状态为演讲愿景，未核实。

## 状态

- 第一批：视频 1–10，2026-10-31 完成并逐条核验。
- 第二批：视频 11–30，2026-11-01 完成并逐条核验。
- 第三批：视频 31–50，2026-11-01 完成并逐条核验。
- 第四批：视频 51–75 进行中；第一组 51–55 已于 2026-11-02 完成并逐条核验，第二组 56–60、第三组 61–65 已于 2026-11-03 完成并逐条核验；第四组 66–70 与第五组 71–75（团队协作，用户 2026-11-03 追加）在办。
- 后续有新的视频批次时，按同样的标准追加新文档，并在本页目录补上链接。

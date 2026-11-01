# notebook

我看 YouTube 学 AI agent 技巧时做的核验型笔记：一共 40 条视频，每条都不是「看完凭印象记」，而是播放器实播、字幕逐段读、代码实跑、说法逐项查证之后才落笔。

![videos](https://img.shields.io/badge/视频-40条-blue) ![verified](https://img.shields.io/badge/核验-2026--11--01-green) ![format](https://img.shields.io/badge/格式-Markdown-lightgrey)

**一句话证据**：40 条视频的时长全部由播放器实测；有配套仓库的逐个克隆对照——Claude Code hooks 的拦截函数离线实跑、TypeScript coding agent 的仓库测试实跑 22/22 通过、Managed Agents 的离线测试通过且演示数字拿原始 CSV 独立复算、RAG 源码与画面逐字一致。第三批首组把 CrewAI 仓库里一次真实运行的产物拿来实跑：它生成的测试文件连导入都过不了，当场拆穿「全自动保质量」的简介口径；第二组里 JARVIS 仓库的人写测试同样倒在导入上——第一行就把常量名拼错。每条笔记末尾都有「存疑点」，查不到的东西明说查不到，不拿猜测冒充原文。

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

## 每条笔记的结构

七个固定部分：① 元信息（含发布时间）② 一句话结论 + 3 句摘要 ③ 技巧总结（每条带我的反驳性思考，外加一条「时效性」评估）④ 按章节的详细笔记 ⑤ 原样保留区（公式、代码、步骤不压缩；代码实跑核对、跑不了的标「未实测」）⑥ 术语表 ⑦ 易错点和存疑点。

## 取材与核验标准

- 字幕、时间戳、代码原文三样拿不齐的视频不写笔记，宁缺毋滥。
- 看不清的地方可以根据上下文推断，但必须显式标「推断」，与原文分开。
- 代码原文以视频简介的仓库对照 + 截图读码 + 实跑验证为准；公式实际计算核对；价格、星数等外部数字按官网或一手页面复核。

## 时效性说明

AI 工具迭代很快，三个月前的技巧可能已经拖后腿。所以每条笔记都标了发布时间，并附「时效性（截至 2026-11-01）」评估：哪些是与版本无关的耐久方法，哪些是当时的工具细节、价格或实验功能口径——后者用之前请先按当前版本复核。最典型的是视频 10（2023 年）：读法至今可用，片中的 AI 工具界面与定价已经过时。

## 已知局限

- 视频 3、视频 5 的确切发布日期未能证实，笔记里给的是带依据的推断窗口，没有编造确切日期。
- 视频 5 的部分代码被字幕条遮挡，遮挡处在笔记中明确标出；整段工程依赖其 SDK，未能完整实跑。
- 视频 9 演讲者的真实姓名、所提 TDD skill 的归属，多个来源互相冲突，笔记保留为「未核实」，没有采信任何一方。
- EGM 插件本身未实际安装试用，笔记中的相关警告如实保留。
- 视频 19 的配套仓库已被作者整体重写，视频当期的 Python 代码已不在仓库中，笔记按播放器画面记录并标注此事。
- 视频 24 没有配套仓库，代码证据全部来自播放器画面，证据等级低于其他条目，笔记中已注明。
- 视频 26 是访谈节目，标题问题在节目结尾没有给出是或否的结论；笔记按两位出场者各自的原话记录，不替节目下结论。视频中的价格数字为节目口径，未做外部核价。
- 视频 28 的终端会话是作者为清晰而重做的演示（视频自述），不是原始执行日志；笔记已在对应条目标注。

## 状态

- 第一批：视频 1–10，2026-10-31 完成并逐条核验。
- 第二批：视频 11–30，2026-11-01 完成并逐条核验。
- 后续有新的视频批次时，按同样的标准追加新文档，并在本页目录补上链接。

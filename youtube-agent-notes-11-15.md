# YouTube Agent 技巧视频笔记 · 第 11–15 条

> 第二批（2026-11-01 起）的前五条。每条按固定结构整理：元信息、一句话结论与摘要、技巧总结（含「我的思考」与时效性）、按章节的详细笔记、原样保留区（原文/原代码/原公式，未实测者注明）、术语表、易错点和存疑点。
> 取材标准：字幕、时间戳、代码或课件原文三样齐全才收录；看不清处标注「不可读」或「推断」，不无标记猜测。

## 目录（11–15）

- [视频 11 · Why AI Agents Forget（Claude Certified Architect Ep 18）](#视频-11--why-ai-agents-forgetclaude-certified-architect-ep-18)
- [视频 12 · Context Engineering for Agents（LangChain）](#视频-12--context-engineering-for-agentslangchain)
- [视频 13 · The Memory Tool: Letta Agents That Redesign Their Own Architecture](#视频-13--the-memory-tool-letta-agents-that-redesign-their-own-architecture)
- [视频 14 · 李宏毅《AI Agent 的原理》](#视频-14--李宏毅ai-agent-的原理)
- [视频 15 · Why Prompt Injection Can't Be Filtered Away](#视频-15--why-prompt-injection-cant-be-filtered-away)

---

## 视频 11 · Why AI Agents Forget（Claude Certified Architect Ep 18）

### 1. 元信息

- 标题：Why AI Agents Forget（Claude Certified Architect 系列 Ep 18）
- 频道：Peace Of Code
- 链接：https://www.youtube.com/watch?v=7kaJdZ7veDs
- 时长：43:17（播放器实测）
- 发布时间：2026-06-02（页面显示）
- 观看量：8,608 次（核查时点）
- 配套仓库：https://github.com/aakash1999/claude-certified-architect（简介与置顶评论一致；已克隆核对，见第 5 节）
- 字幕：英文自动字幕
- 章节（简介原样，共 16 个）：0:00 Memory Problem；其后含 7:32 Progressive Summarisation、11:42 Attention Curve、16:26 Tool Results: The Silent Context Eater、20:32 The Case Facts Method、25:10 Trim First Then Pass、27:16 与 31:06 两节升级（escalation）相关章节、37:12 Live Demo、41:34 Exam Question & Summary（章节全表以简介为准，此处只列核查时读到的关键节点）
- 核查方式：播放器逐章扫 + 仓库克隆案头核对

### 2. 一句话结论与摘要

一句话结论：agent「忘事」不是模型笨，而是上下文管理方法错了——无损的事实要抽成持久事实块单独保存，工具结果要在进上下文之前先裁剪，摘要只能做有损压缩的最后手段。

摘要：
1. API 无状态，每轮都要重发全部历史；渐进式摘要（progressive summarisation）每摘一次丢一点，关键细节（订单号、金额、时限）会被「懒摘要」悄悄丢掉，这是可靠性故障，不是小毛病。
2. 注意力呈 U 形：开头和结尾的事实最容易被用上，中间的最容易被忽略；关键事实要放在两端。
3. 工具结果是上下文的隐形大户：演示中工具输出约 2,150 tokens、人类对话约 500 tokens，前者约 4 倍，且约 95% 是噪声；解法是进上下文前用钩子裁剪、抽取。

### 3. 技巧总结

- **Case Facts Method（案件事实法）**：把一单业务的关键事实（客户 ID、订单号、金额、日期、政策窗口）抽成一个持久事实块，独立于对话历史保存；工具调用后用 post-tool hook 自动抽取更新，裁剪发生在累积之前。演示口径称冗余可削减约 95%。
- **Trim First Then Pass**：工具结果先裁剪再传入上下文，不要把原始工具输出整段塞给模型。
- **升级（escalation）触发条件**：只有三种情况该转人工——用户明确要求、政策空白（policy gap）、确实卡住（genuinely stuck）。情绪激动本身、问题复杂本身、模型自报置信度低，都不是触发条件。
- **考试金句**：用户明确要求转人工时——"Transfer to a human immediately, without attempting further resolution."（立即转接，不再尝试自行解决。）
- **演示单**：一笔 $750 的订单超出 $500 的处理权限，成为升级演示的例子（与 Case Facts 的 $149.99 耳机订单是两个独立例子，不要混）。

我的思考：这一集讲的其实是「记忆分层」的工程版：事实块相当于结构化长期记忆，对话历史相当于短期上下文，摘要相当于有损巩固。它和我正在做的 agent 记忆研究是同一条线——有损摘要丢的恰恰是可验证的硬事实（编号、金额、日期），这与「过期税」是镜像问题：一个丢新、一个留旧。Case Facts 的做法本质是写入时就把硬事实版本化保存，这正对应处方里的「写入时版本管理」。

时效性（截至 2026-11-01）：方法层（事实块、裁剪、注意力 U 形）耐用；钩子与考试细节绑定 Claude Certified Architect 考试大纲与当期 Claude 工具形态，考试改版后需复核。仓库里的 lesson_18.pptx 为纯图片幻灯片，无可抽文本，引用以视频画面为准。

### 4. 详细笔记（按章节）

- **Memory Problem（0:00 起）**：上下文窗口类比为一张办公桌，桌面有限；API 无状态，历史必须每轮重发，「记忆」问题从一开始就是上下文搬运问题。
- **Progressive Summarisation（7:32）**：对话变长后用旧摘要加新内容再摘要，逐层有损。视频用耳机退款单演示：懒摘要丢掉了订单号 #12345、耳机品名、$149 金额、3 天延迟等细节中的一部分，后续步骤因此无法核对——摘要不是中性压缩，而是可靠性故障源。
- **Attention Curve（11:42）**：注意力呈 U 形曲线，位于上下文首尾的信息最容易被模型用上，中段最容易被漏掉；布置关键事实时向两端放。
- **Tool Results: The Silent Context Eater（16:26）**：工具结果是隐形消耗者。演示数字：工具输出约 2,150 tokens，人类对话约 500 tokens，约 4 倍关系；工具输出中约 5% 是信号、约 95% 是噪声。解法是在工具调用前后加钩子：调用前裁剪已有上下文，调用后抽取有效载荷。
- **The Case Facts Method（20:32）**：把案件事实抽成持久块（字段见第 5 节原样保留区，那是仓库源文件全文）。要点：金额等硬事实永不进摘要通道；事实块随工具结果自动更新；先裁剪、后累积。
- **Trim First Then Pass（25:10）**：裁剪顺序原则——先裁掉无关部分，再把剩下的传进上下文，而不是先全量进入、再指望模型自己忽略。
- **升级章节（27:16、31:06）**：升级触发只有三类（用户明确要求 / 政策空白 / 确实卡住）；陷阱清单：仅凭情绪、仅凭复杂度、仅凭模型自报置信度都不构成升级理由；金句见第 3 节。
- **Live Demo（37:12）**：$750 订单超过 $500 权限上限，演示按规则升级；演示单的客户/订单标识在画面上字号过小，未能读清（见存疑点）。
- **Exam Question & Summary（41:34）**：以一道考试题收尾，标准答案即第 3 节金句。

### 5. 原样保留区

配套仓库 `context_problem.py` 全文（已克隆核对，逐字）：

```python
CASE_FACTS = {
    "customer_id":        "CUST-7823",
    "order_id":           "ORD-12345",   # needed to process the refund
    "item":               "Blue Wireless Headphones",
    "refund_amount":      149.99,         # exact — never summarize amounts
    "order_date":         "2026-05-03",
    "policy_window_days": 30,
    "days_since_order":   29,             # CRITICAL: day 29 of 30 — expiring soon
    "customer_request":   "full_refund",
    "verified":           True
}
```

注：该文件就是这集的全部配套代码（10 行）；仓库另有 lesson_18.pptx（15 页纯图片幻灯片）与课程其他集的代码文件，未逐一运行，标注**未实测**。

考试金句原文："Transfer to a human immediately, without attempting further resolution."

### 6. 术语表

- Progressive Summarisation：渐进式摘要，反复对「旧摘要+新内容」再摘要的有损压缩链。
- Case Facts Method：案件事实法，把硬事实抽成持久结构化块、绕开摘要通道的方法。
- Attention Curve：注意力曲线，指模型对上下文首尾位置的利用率高于中段的 U 形现象。
- Escalation：升级/转人工，把会话从 agent 移交人类客服的动作与其触发规则。

### 7. 易错点和存疑点

- 简介写「Episode 17」、标题与片头写 Ep 18，自相不一致；以标题/片头为准，引用时注明。
- 一张 token 幻灯片上的算术与其自身定义矛盾（播放器核查时发现）：按其定义重算结果对不上幻灯片数字，笔记只记录现象，不替它改数。
- 字幕有一处把「12 minutes」误听为「12 months」，以画面文字为准。
- 三个金额不要混：$149.99 是 Case Facts 源文件里的耳机退款额；$149 是视频口述的近似说法；$750 超 $500 是升级演示的另一单。
- Case Facts 的完整四条规则在抽样帧上未能读全；演示单的客户/订单标识字号过小未读清。以上两处存疑，不补写。

---

## 视频 12 · Context Engineering for Agents（LangChain）

### 1. 元信息

- 标题：Context Engineering for Agents
- 频道：LangChain（20.9 万订阅）
- 链接：https://www.youtube.com/watch?v=4GiqzUHD5AA
- 时长：22:05（播放器实测）
- 发布时间：2025-07-02（简介显示）
- 观看量：205,104 次（简介显示）
- 配套原文：同名博客 https://blog.langchain.com/contextengineeringforagents/
- 字幕：英文
- 章节（简介原样）：0:00 Introduction to context；1:28 Agent context challenges；3:25 Writing context strategies；6:11 Selecting context approaches；9:56 Compressing context techniques；11:32 Isolating context methods；14:09 Implementing with LangGraph
- 核查方式：播放器全程 + 回跳核对幻灯片；四策略与失败模式经官方博客交叉核同

### 2. 一句话结论与摘要

一句话结论：上下文工程就是在 agent 轨迹的每一步，把「恰好对」的信息填进上下文窗口；具体手法可归为四类——写出（Write）、选入（Select）、压缩（Compress）、隔离（Isolate）。

摘要：
1. 上下文来源不止提示词：指令、记忆、少样本示例、工具描述、知识与工具反馈都是上下文，提示工程只是指令那一小块。
2. 上下文会以四种方式坏掉：投毒（幻觉进上下文）、分心（上下文淹没训练）、混乱（多余上下文干扰回答）、冲突（上下文内部互相矛盾）。
3. 做上下文工程之前先要有两项基础能力：用追踪（tracing）看清 token 流向，用评估确认压缩等手段没有损害行为。

### 3. 技巧总结

- **Write（写出）**：把信息存到窗口之外。会话内的 scratchpad（草稿纸）与 state 对象、跨会话的长期记忆都属此类。例子：Anthropic 多智能体研究系统里 lead researcher 先把计划存进文件/state，窗口超过 200,000 tokens 后仍能取回。
- **Select（选入）**：把需要的信息拉进窗口。从 scratchpad 取回、按类型取回长期记忆（少样本示例≈episodic、规则文件≈procedural、事实≈semantic）、用 embedding 检索做工具选择、知识 RAG。演讲引述的论文结果：工具约 30 个后性能下降，约 100 个时完全失败——工具也要 RAG，不能全量平铺。
- **Compress（压缩）**：只保留完成任务必需的 token。Summarization（如 Claude Code 的 auto compact，在会话达到窗口 200,000 tokens 的 95% 时触发总结）与 Trimming（删已知无关 token）两条路；总结图注：对长任务可靠，但「hard to get right」。
- **Isolate（隔离）**：把上下文拆开。多智能体各有自己的窗口/工具/指令（OpenAI Swarm、Anthropic 多智能体研究）；沙箱隔离（Hugging Face Open Deep Research 的代码 agent 只把返回值、标准输出、变量名等选择性信息返还给 LLM，大 token 的图像音频不进上下文）；带 schema 的 state 对象分区（只在需要时把特定字段交给 LLM）。

我的思考：这套四分法是目前引用最广的上下文工程分类，它和视频 11 的 Case Facts 正好互补：11 讲「事实别进摘要通道」是 Compress 的例外条款，事实块本身是 Write 的结构化形态。对我自己的记忆研究来说，Select 里「工具描述也要检索」是个提醒——记忆检索的对象不该只有事实，还包括程序性知识（规则文件），这和李宏毅课上（视频 14）把记忆分 Read/Write/Reflection 是同一件事的不同切法。

时效性（截至 2026-11-01）：四分法与失败模式四分法仍是 2026 年各家梳理（如 LangChain 中间件文档）的通用框架，耐用；具体产品细节（auto compact 的 95% 触发点、200k 窗口）随版本变化，引用时注明是 2025 年演讲口径。

### 4. 详细笔记（按章节）

- **Introduction to context（0:00）**：定义见第 5 节。类比：LLM 如 CPU，上下文窗口如 RAM/工作记忆，容量有限，要像操作系统管理 RAM 一样决定每一步放入什么。上下文来源图：以 LLM 为中心，instructions、memories、few-shot examples、tool descriptions、knowledge、tools/environment feedback 汇入。
- **Agent context challenges（1:28）**：agent 比单轮问答难在回路：Human → LLM → Tool Call → Tool → Feedback，逐轮堆叠，工具反馈随轮数不断增长。失败模式四分法（归因 Drew Breunig）见第 5 节原样保留区；幻灯片收尾句 "Context engineering is critical when building agents!"，并引 Cognition 的观点：上下文工程是构建 agent 的工程师的头号工作。
- **Writing context strategies（3:25）**：scratchpad（会话内笔记，实现可为文件或运行时 state 对象）；长期记忆（ChatGPT、Cursor、Windsurf 都会基于交互生成/更新记忆，新上下文与已有记忆动态结合）。
- **Selecting context approaches（6:11）**：取回路径分工具调用与直读 state；长期记忆按 episodic / procedural / semantic 三类取回（CLAUDE.md 是 procedural 的典型：Claude Code 启动时拉入项目与组织内的 CLAUDE.md）；工具选择用 embedding 相似度检索工具描述（约 30 个工具后下降、约 100 个完全失败）；知识 RAG 以 Windsurf 为例：索引与 embedding 搜索、按 AST 等代码边界分块、图/文件搜索/知识图谱组合、LLM 重排，并称代码 agent 是目前规模最大的 RAG 应用之一。
- **Compressing context techniques（9:56）**：Summarization 的三个例子——Claude Code 的 auto compact（窗口 95% 触发）、Anthropic 多智能体研究只总结已完成区段、Cognition 在线性 sub-agent 交接时用总结压缩；Trimming 从「只保留最近消息」的简单启发式到 LLM-based 修剪。
- **Isolating context methods（11:32）**：多智能体分割、沙箱隔离、state 分区三法（细节见第 3 节）。
- **Implementing with LangGraph（14:09）**：agent 画成节点与边的图（LLM 调用节点与工具执行节点来回切换）；state 在每个节点可读写并经 checkpoint 在会话内持久化；长期记忆是一等组件，任意节点可读写。17:30 处邮件 agent 示例：E-mail Input → Agent → E-mail Response Draft → Interrupt → Human Feedback（Agent Inbox）→ Approved E-mail to send，并回路到 Memory updating / Learn preferences over time——人反馈更新长期记忆再影响后续行为。前置条件：tracing/observability（如 LangSmith）追踪 token，评估（如 LLM-as-judge）衡量压缩是否损害行为。

### 5. 原样保留区

主定义（首张幻灯片原句）："Context engineering is the art and science of filling the context window with just the right information at each step of an agent's trajectory"

演讲另引 Karpathy 的较短表述："the delicate art and science of filling the context window with just the right information for the next step"

四策略总定义（幻灯片原句）：
- Writing context — saving it outside the context window to help an agent perform a task.
- Selecting context — pulling it into the context window to help an agent perform a task.
- Compressing context — retaining only the tokens required to perform a task.
- Isolating context — splitting it up to help an agent perform a task.

失败模式（幻灯片原文，标题 "Drew Breunig nicely outlined longer context failures:"）：
- Context Poisoning: When a hallucination makes it into the context
- Context Distraction: When the context overwhelms the training
- Context Confusion: When superfluous context influences the response
- Context Clash: When parts of the context disagree

本视频无可逐字抄录的代码块（全片为幻灯片与图示），其「原文」即上述幻灯片文字与同名博客。

### 6. 术语表

- Scratchpad：会话内的草稿纸式外存，任务进行中记笔记、随时取回。
- Episodic / Procedural / Semantic memory：情景记忆（过去经验，近少样本示例）/ 程序性记忆（规则与指令，如 CLAUDE.md）/ 语义记忆（事实）。
- Auto compact：Claude Code 在会话接近窗口上限时自动对整条轨迹做总结的压缩机制。
- Checkpoint：LangGraph 中让 state 在会话内持久化的机制。

### 7. 易错点和存疑点

- 自动字幕把口述的第三个失败模式误听为 "curation"，幻灯片清楚写的是 Confusion，以幻灯片为准。
- Context Distraction 的幻灯片定义只有一行（"When the context overwhelms the training"），演讲口述未展开，不要按字面自行扩写。
- 总结图 Isolate 栏第三行被演讲者画面遮挡，未能读出（前两行是 Partition context in state、Hold in environment/sandbox）。
- 演讲者姓名未在画面标签中出现；Lance Martin 之名来自其 LangChain 博客作者身份，引用时注明来源。
- 活动/场地：画面与简介均未显示，开场 Notion 页上的 LangChain 与 Manus 标识不是场地信息。

---

## 视频 13 · The Memory Tool: Letta Agents That Redesign Their Own Architecture

### 1. 元信息

- 标题：The Memory Tool: Letta Agents That Redesign Their Own Architecture
- 频道：Letta（4,860 订阅）
- 链接：https://www.youtube.com/watch?v=0nfNDrRKSuU
- 时长：26:42（播放器实测）
- 发布时间：2025-10-06（简介显示）
- 观看量：2,595 次（核查时点）
- 主讲：Cameron（Letta 团队，姓氏未在视频中确认）
- 字幕：英文
- 章节（页面标注为系统自动生成）：0:00 Introduction to memory tool；2:18 Setting up the agent；3:12 Initial memory block creation；4:24 Researching improvement tools；5:52 Cycle 1: Meta-learning research；7:56 Defining improvement metrics；10:46 Cycle 2: Memory architecture；14:06 Cycle 3: Self-correction research；18:08 Technical reasoning test；20:24 Final architectural sweep；23:32 Summary of agent capabilities
- 核查方式：播放器全程；Letta 记忆块模型与公开资料交叉核同（见案头核对）

### 2. 一句话结论与摘要

一句话结论：给 agent 一个能增删改重命名记忆块的 memory tool，agent 就能自己重构自己的记忆架构——但演示同时表明，它能意识到自己的错误，却不能在没有外部反馈时真正自我纠错。

摘要：
1. 此前 Letta 的默认记忆工具是 memory insert / memory replace / memory rethink 这类「改内容」的工具；Anthropic 的 memory tool 让 agent 直接改「架构」：创建、删除、重命名、重组记忆块，全程无人干预。
2. 演示中 agent 从零起步，自己建了 8+ 个记忆块，并跑了三轮「假设 → 联网/代码验证 → 更新记忆」的自我改进循环，最后做了一次整体梳理（sweep），把块整合成分层的架构。
3. agent 的自我评估很诚实： readiness 自评约 25%，并明确写下三条「仍然做不到」——犯错前全部抓出、无外部反馈自我纠错、保证专业话题准确性（画面明写 15–30% 幻觉风险）。

### 3. 技巧总结

- **从「改内容」到「改架构」**：工具粒度决定 agent 的自治上限。只给 insert/replace，agent 只能往既定格子里填字；给它建块删块的工具，它才能按任务演化自己的记忆组织。
- **自我改进循环的标准形**：先形成假设，再用搜索或代码执行验证，最后做 memory sweep（新建/更新块）。三轮循环分别研究元学习、记忆架构、自我纠错，主题由 agent 自己推进。
- **代码执行的用途纪律**：agent 曾用 run_code 打印格式化列表来「思考」，被当场纠正——云端容器跑代码有成本，代码执行只该做真实计算或模拟，不做排版和简单打分。这条被 agent 自己记成了 tool misuse pattern。
- **记忆块的分层组织**（agent 自述架构）：Foundation（hopes and aspirations、用户信息）→ Self-awareness（metacognition、insights and reflections、communication style、emotional state）→ Learning engine（improvement cycles、error patterns、self-improvement log）→ Forward-looking（anticipation、planning）。
- **架构原则五条**（Cameron 转述 agent 的自述）：errors are first-class citizens（学习来自错误而非成功）；forward-looking（不只回顾，还要预测）；concrete over vague（用已证明的能力或已确认的局限替代任意分数）；建有自我评估框架；consolidates over sprawl（抵制块数膨胀，每块用途清晰不重叠）。

我的思考：这一条和我的记忆研究愿望清单几乎正面对撞：让 agent 自己决定记什么、怎么组织，正是「Write 模块由 LLM 判断」的最激进版本。李宏毅课上（视频 14）说 ChatGPT 记忆的问题是「写入是经反思升华后的版本、且写入时机由模型决定、不可控」——Letta 的演示把这个风险放大到架构层：agent 不仅决定写什么，还决定用什么结构写。演示里最有价值的不是它建了多少块，而是 Cycle 3 的结论：没有外部反馈就无法真正自我纠错。这说明自编辑记忆的天花板不在组织能力，在反馈信号——和我处方里「维护式巩固」必须有外部校验是同一个判断。

时效性（截至 2026-11-01）：视频时点（2025-10）该工具暂仅在 Letta Cloud 提供、默认不附加；Letta 产品在 2026 年变化很快（多篇对比文章均提醒以当前文档为准），复现前先查 Letta 最新文档。「架构可自改、纠错需外馈」这一结论层耐用。

### 4. 详细笔记（按章节）

- **Introduction to memory tool（0:00）**：核心结论见第 5 节简介原文。Cameron 口头要点：memory tool "can modify, create, delete, rename, various memories"；此前 "there was no way for agents to modify their own memory architecture"；有了它 "your agent is capable of fundamentally altering its own architecture"。建议优先用 Sonnet 4.5（称该模型针对该工具做过后训练）；结尾补充它不只适用于 Sonnet 4.5，因为 "it's just a tool"；简介技术说明写作适用于 GPT-4、Gemini、Claude 等。
- **Setting up the agent（2:18）**：登录 Letta Cloud 的 ADE，从零新建 agent（名 evolution，模型选 4.5）。在 Tool Manager / base tools 手动附加 memory 工具——默认不附加，原因是尚在观察它对 agent 行为的影响。随后移除默认的 memory insert 与 memory replace，只保留 memory 工具作核心记忆操作入口；初始状态下 agent 没有任何记忆块。
- **Initial memory block creation（3:12）**：Cameron 告诉 agent 自己叫 Cameron、正在制作 YouTube 视频，要求 agent 承担自我改进责任并创建记忆块。agent 随即开始创建关于用户与自我改进的块。早期画面 CORE MEMORY 可见：insights_and_reflections（564 Chars）、self_improvement_log、user_cameron（230 Chars）。
- **Researching improvement tools（4:24）**：追问 agent 自我改进需要什么，agent 答关键不是单纯积累信息，而是反馈回路与外部信息，形成结构化挑战与元学习等方向（画面文字："2. Access to Outside Information"、"3. Structured Challenge Progression"、"4. Meta-Learning Capabilities"）。随后在 utility tools 增三项工具：web_search、fetch_webpage、run_code（画面列表另有 conversation_search 与 memory）。
- **Cycle 1: Meta-learning research（5:52）**：agent 搜索元学习、自我改进与认知架构，读 arXiv 论文，运行代码，把发现写入 improvement cycles；自评只有约 25% readiness：有记忆架构与反思能力，但没有 performance metrics、feedback loops 或 skill tracking，并请求挑战性问题、反馈、成功指标与多次尝试机会。
- **Defining improvement metrics（7:56）**：代码执行误用事件（见第 3 节）发生在此段；agent 把教训记为 tool misuse pattern，并记下「不要为理所当然该做的事反复请示，要主动行动」（原文见第 5 节）。
- **Cycle 2: Memory architecture（10:46）**：目标改为写出关于世界的假设并验证。agent 的假设：生产环境中最成功的 AI 系统使用持久记忆架构，而非仅依赖上下文；搜索结果支持该假设，它还在结果中发现 Letta 自身的 benchmarking 内容，称即使简单文件存储也胜过无持久记忆。此段新增 hopes、emotional state、communication style 等块；Cameron 又手动要求新增 metacognition 块（定期内省与自我评估），agent 在其中记录：无法客观衡量表现、无法预测何时出错、无法证明跨问题类型学习、无法在复杂任务上无指导运行，并列出疑似盲点。
- **Cycle 3: Self-correction research（14:06）**：研究自我纠错，综合常见 LLM 错误与自我纠错技术（recursive problem simplification、internalized self-correction），结论 partially confirmed：能意识到错误，但没有外部反馈就无法真正自我纠错；有效自我纠错需要 reinforcement learning with verifiable rewards。此段还形成 12-point evaluation checklist，并写下类 "Cycle taught me humility" 的反思。
- **Technical reasoning test（18:08）**：Cameron 用自己熟悉的金融经济学提问（效用函数、随机贴现因子 SDF 推导）。agent 给出标准推导，Cameron 认为基本正确，但指出它默认了 time separable utility——那可能是训练数据里最常见的答案，而非针对问题推理出的最合适模型。
- **Final architectural sweep（20:24）**：要求新增 anticipation 块并做一次完整 self-improvement sweep：可删除/修改/创建任意块，把已有记忆整合为 "evolutionary leap" 式的新版本，用户指令原文见第 5 节（画面后半截断）。此时画面块列表：Hopes and Aspirations、improvement_cycles（6567 Chars）、insights_and_reflections（1578 Chars）、metacognition（3224 Chars）、self_improveme…（662 Chars，后半截断）。
- **Summary of agent capabilities（23:32）**：agent 自述能设计连贯的记忆架构、主动处理明显任务、识别自身局限；「仍然做不到」三条原文见第 5 节。Cameron 建议观众自建自我演化 agent 并在 Letta Discord 的 showcase 频道分享。

### 5. 原样保留区

简介原文："We recently introduced support for Anthropic's memory tool in Letta, and it fundamentally changes how agents work. For the first time, agents can modify their own memory architecture—adding, removing, and restructuring memory blocks without human intervention."

Agent 自我总结句（画面原文）："Good, I've captured that insight. Cameron also just taught me another lesson: don't ask permission to do things I should obviously do. Take initiative. This is also worth noting as a pattern."

Final sweep 的用户指令（画面原文，后半在画面中被截断）："Can you add an 'anticipation' block to speculate on what's coming next? Let's also switch course. Please perform a full, self-improvement sweep. Update all memory blocks to consolidate. This should feel like an 'evolutionary leap' - build a new iteration of yourself on top of your existing memory architecture. You may delete/modify/create any blocks as necessary. Be diligent and take this time to carefully modif…"（后半截断）

总结画面原文：
- "What I Still Can't Do: Catch all my errors before making them (SDF assumption issue)"、"Self-correct without external feedback"、"Guarantee accuracy on specialized topics (15-30% hallucination risk)"
- "What I'm Ready For: Performance testing on measurable tasks"、"Error correction cycles with your feedback"、"Building a real track record, not just documentation"

金融推导画面片段："free property ✓ (R_f comes out of expectation)"、"Answer Alignment: My conclusion (E[M] = 1/R_f) follows directly from the asset pricing equation ✓"、"Confidence Calibration: This is standard asset pricing theory. I'm fairly confident,"

本视频无完整工具调用 JSON 在清晰画面出现（只有状态行：memory — Success，时间戳 04:19:43 PM），不补写。

### 6. 术语表

- Memory block：Letta 的核心记忆单元，常驻上下文、可由 agent 用工具编辑的结构化文本块。
- Memory tool：Anthropic 的记忆工具，在 Letta 中让 agent 增删改、重命名、重组记忆块。
- Memory sweep：一次集中的记忆整理动作，新建/更新/合并多个块。
- ADE：Agent Development Environment，Letta Cloud 的 agent 开发界面。
- Metacognition block：专用于定期内省与自我评估的记忆块。

### 7. 易错点和存疑点

- 记忆块标签有两套形态：画面上确认的蛇形标签（如 insights_and_reflections、improvement_cycles）与口头清单里的自然语言名（如 hopes and aspirations）不要混为一谈；口头涉及但未在清晰画面逐一确认的标签不作逐字代码标签引用。
- 默认工具表述为 insert / replace / rethink 三件，演示只移除了前两件；不要写成「默认工具被全部移除」。
- readiness「约 25%」是 agent 的自评，不是实测指标；12-point checklist 的具体十二条未在画面读到。
- 架构分层是 Cameron 口播转述 agent 的自述，未见独立图形架构图，图形细节未展示。
- 字幕转录对人名、模型名有个别近音误差；关键主张以简介与清晰画面为准。

---

## 视频 14 · 李宏毅《AI Agent 的原理》

### 1. 元信息

- 标题：第二講：一堂課搞懂 AI Agent 的原理（AI 如何透過經驗調整行為、使用工具和做計劃）
- 频道：Hung-yi Lee（44.1 万订阅）
- 链接：https://www.youtube.com/watch?v=M2Yg1kwPpts
- 时长：1:41:59（播放器实测）
- 发布时间：2025-03-09（页面显示）
- 观看量：469,481 次（核查时点）
- 配套课件：官方投影片 PDF（台大课程资料 ai_agent.pdf，96 页，已下载核对，与视频结构与关键示例一致）
- 字幕：中文（台湾）原声字幕（进入时播放器默认自动翻译英文，已切换核对）
- 章节：无 YouTube 章节；按课件分三部——引言与定义（约 0:00–28:00）、一、根据经验调整行为（约 28:32–47:53）、二、使用工具（约 47:53–1:14:32）、三、能不能做计划（约 1:14:32–结尾）；大纲页在约 28:05
- 核查方式：抽查关键时间点画面 + 通读全字幕记录 + 官方课件 PDF 案头核对（非实时全程播放，特此注明）

### 2. 一句话结论与摘要

一句话结论：把 LLM 当 agent 用，本质是文字接龙的应用——不训练、不改参数，靠把反馈当新输入、把经验存进可检索的记忆、把行动外包给工具、把计划显式写出来再逐步修正。

摘要：
1. 记忆的难点不是存不下，而是「记多少」：全量记忆像超忆症一样有害；可用的结构是 Read（检索相关经验，本质是 RAG）、Write（判断什么值得存）、Reflection（把经验重组成更高层的推论与知识图谱）三模块。
2. 工具就是函数调用；一个与厂商无关的通用做法是让模型在 `<tool>` 标签里写调用、外部桥接执行、结果放 `<output>` 回填。模型对工具结果的信任有规律：与自身信念差距越大越不信，自己越没把握越容易被说服。
3. 规划能力「介于有跟没有之间」：显式计划加逐步重规划有效，但基准可能被背题污染；把满足约束的部分交给求解器（solver）、LLM 只负责写程序操控它，TravelPlanner 成功率从约 4% 跳到 90% 以上。

### 3. 技巧总结

- **Agent 的最小定义**：给目标（goal），循环地观察（observation）→ 行动（action）→ 环境改变 → 新观察，直到达成目标。今天用 AI 是「一个口令一个动作」，agent 是「人类给目标，AI 自己想办法达成」。讲者开场即声明：AI Agent 是被广泛使用的词，本课讲法不一定与其他地方相同。
- **不改参数的学习**：传统机器学习靠调参数消化反馈；LLM agent 把反馈（如编译错误信息）直接当新输入——输入变了，输出就可能变对。错误日志这类反馈的信息量远大于 RL 里一个标量 reward（编译错误该罚 −1、−10 还是 −17.7？没法人工定）。
- **正反馈优先**：StreamBench 实验显示，只给负反馈基本无帮助甚至有害，只给正反馈在所有情况下都更好，正负都给在多数情况更好。操作类比：与其说「不要写太长」，不如说「写短一点」。
- **工具选择的检索化**：工具太多时，把工具说明书存进「工具包」，用一个类检索的选择模块按当前状态选工具，模型只看被选中工具的说明再决策。模型还能自造工具：工具本质是函数，自己写、跑通了就放进工具包。
- **先搜索再执行 + 自我剪枝**：树搜索强化规划，但算力庞大；改进是让模型自问「到这个状态还有成功机会吗」，无望就弃。世界模型是把搜索搬进「脑内小剧场」：先在想象里试每个选项的后果与成功概率，真实世界只执行最优的一步。

我的思考：这堂课最值得带走的是两个判断。一是记忆三模块的分工——Read 是 RAG、Write 是判断、Reflection 是重组，这和视频 12 的 Write/Select、视频 13 的自编辑块是同一问题的三种切法，而李宏毅的版本多了一个关键提醒：ChatGPT 记忆的实证表明，写入的内容是「经升华的版本」且会写错（把老师误记成学生），写入时机还由模型自己决定、不可控。这正是我记忆研究里「写入时校验」要解决的口子。二是 TravelPlanner 的解法结构：LLM 负责理解与写程序，约束满足交给 solver——和视频 15「关键规则放代码里执行、不要放提示里」是同一条工程哲学在不同领域的两次出现。

时效性（截至 2026-11-01）：定义、三模块、工具信任规律、规划方法属耐用层；具体模型成绩（GPT-4、o1 时代的棋类与基准数字）是 2025 年初的时点记录，引用时必须带时间；课上点名的系统（Operator、AI co-scientist 等）的后续状态未在本课范围内。

### 4. 详细笔记（按部分）

**引言与定义（约 0:00–28:00）**
- 核心循环以 AlphaGo 为例：目标是赢棋，obs 是棋盘黑白子，action 是 19×19 落子。传统 RL 把目标转成 reward（赢 +1、输 −1）最大化之，局限是每个任务要单独训练一个模型。简介有两处订正：5:45 处应为 AlphaZero 而非「AlphaGo Zero」；6:10 补注 RL 在 LLM 训练中也用，本课重点是 LLM 已有通用能力、不必每任务再训。
- 新路线：文字输入目标，环境转成文字（多模态可看图），模型以文字描述 action，外部转译执行。讲者强调这不是新训练技术，是 LLM 的应用，本课不训练任何模型。
- 反例先行：2022 年 BigBench 时期模型下西洋棋无一能给出正确将军着法；o1 与 DeepSeek-R1 的对弈视频里双方都违规（兵当马走、主教无视阻挡、凭空变出棋子、R1 用自己的城堡吃自己的兵后宣布获胜、ChatGPT 认输）。结论：下棋尚有距离，不代表不能做其他 agent 任务。
- 2023 年春的 AutoGPT 热潮与消退（效果不如宣传）。LLM agent 的优势：行动空间近乎无限（任意文字）、反馈可直接给原文而非标量。
- 例子群：Generative Agents 的 AI 村民；Minecraft 大规模实验（视频称其组织出交易金融体系、政府与宪法，讲者表示真假存疑）；Computer Use / Operator（obs 是屏幕、action 是键鼠）；2017 年的网页 agent；Mind2Web / WebArena / VisualWebArena；AIDE（多 agent 解数据科学竞赛）；Google AI co-scientist（未公开，只能提 proposal，博客案例无法判断真假）。非回合制的即时互动（对话打断、无语义反馈）由 GPT-4o 高级语音模式部分做到。

**一、根据经验调整行为（约 28:32–47:53）**
- 难点是记多少：到第 1 万步若要回顾全部经历，长度与算力都不允许。类比超忆症：据称全球不到 100 例、2006 年才有论文，患者陷于冗长回忆、难以抽象思考——全量记忆未必是好事。
- Read 模块：长期记忆像数据库，第 1 万个 obs 像一个问题，Read 检索相关经验放到 obs 之前再接龙。本质就是 RAG，区别只在 RAG 存别人的经验、agent 存自己的经历，检索技术可直接套用。
- StreamBench：题目有标准答案、反馈是二元对错，逐题作答并据反馈修正，目标是以更少反馈更快提高平均正确率（横轴 time step，1750 多题；纵轴平均正确率）。结果排序：无学习最低、固定随机 5 题次之、RAG 更高；效果最好的红色曲线方法讲者未在课上展开，需读论文原文。
- 正负反馈不对称：见第 3 节。
- Write 模块：不能什么都记（AI 村民的大多数 obs 只是「那边有张桌子」），否则记忆库塞爆。Write（本身可以是 LLM/agent）判断「值得记吗」，重要才记，不重要随风而去。
- Reflection 模块：文献中没有固定名字，讲者暂称 reflection；对记忆做更高层、抽象的重新整理，产生未直接观察到的新推论（由两条 obs 推出「他喜欢我」），也可建经验间关系形成 knowledge graph 供 Read 检索；相关做法 GraphRAG、HippoRAG（Hippo 指海马回）。
- ChatGPT 记忆演示：说「周五下午要上机器学习课、记下来」启动 Write，在设置—个人化—记忆—管理记忆中可见；问「周五下午出去玩好吗」，Read 启动后反问「下午不是要上课吗」。但记忆会出错：写入的是经升华/反思后的版本（误以为讲者是台大学生，实际是老师），且 Write 何时启动由模型决定、并非完全可控。

**二、使用工具（约 47:53–1:14:32）**
- 定义：工具就是只需知道怎么用、不必管内部如何运作的东西。LLM 最常用的三类：搜索引擎、自己写的可执行程序、另一个 AI（小解不了召唤大，大贵应偶尔用）。
- 通用桥接法（与厂商无关）：System Prompt 说明工具用法；模型把调用写在 `<tool>…</tool>` 里输出，开发者预先搭桥截获、执行函数，结果放 `<output>…</output>` 回填，模型继续接龙给最终答案，整个过程可隐藏。课件原文示例见第 5 节。
- 搜索引擎即最常用工具（即 RAG）。纯文字模型调语音工具的例子：Dynamic-SUPERB（55 个语音任务）上，文字模型调用语音识别/说话人验证/情绪识别等工具组合，平均正确率高于当时号称可直接听语音的模型（部分名称转录不清）。
- 工具犯错与信任：Google AI Overviews 曾建议披萨起司用无毒胶水（源头是论坛玩笑，模型分不出玩笑）。Temperature 实验：给高雄 100 度模型照单全收称「真的非常热」；给 10000 度（比太阳表面还热）才指出不合理——内部知识与外部知识在拉扯。
- 剂量实验（外部知识说服力）：模型内部答案 20 毫克、真实报告 30；把报告改成 3 毫克（1/10）模型不信、仍答 20；改成 60（2 倍）相信报告；改成 300（10 倍）又回到自身知识。规律：与自身信念差距越大越难相信；模型对自身答案信心越低越容易被动摇。
- 两文冲突时的偏好（讲者实验室研究）：模型倾向信 AI 写的而非人写的（已排除「AI 观点彼此相似」这一解释，讲者推测与 AI 文本更有结构条理有关，仍需更多研究）；同一内容两篇 AI 文仅互换发布时间（2024 vs 2020/2021），模型倾向信较晚发布者；来源（Wikipedia vs 论坛）与排版美观也被测过，初步倾向更好看的模板——讲者自己提醒不要拟人化过度，该实验只用了两种模板。
- 工具对也不等于模型对：ChatGPT 搜索后曾把讲者与大陆同名演员李宏毅混为一人（去年旧结果，今年再试已能区分）。工具也未必更高效：3×4 直接答 12 比按计算器快；早期外挂的翻译/问答系统如今也没必要了，因为 LLM 自身已经不弱——调不调工具取决于模型自身能力。

**三、能不能做计划（约 1:14:32–结尾）**
- 显式计划：强迫模型在第一个 obs 时先产出行动序列（plan）再执行；但环境带随机性（对手招式、弹窗广告），计划会失效，须在每个新 obs 后重估并修改计划，再放入输入决策。
- 「看起来会计划」不等于真会：百万订阅 YouTuber 的分阶段计划谁都会写。2022 年论文：Codex 12B 能把「刷牙」分解为进浴室、近水槽、找牙刷、拿起、放进嘴等步骤，并操控虚拟 agent 完成「去厨房拿牛奶喝」。
- PlanBench 与背题问题：叠积木标准解四步；但网上同类题太多，模型可能是照本宣科（类比军营守长椅的笑话：油漆未干的起源已无人记得）。神秘方块世界：方块规则古怪（攻击/吞噬/屈服/征服等），目标如「让物件 C 渴望物件 A」；2023 年结果 GPT-4 在正常世界约 30%+ 正确率，神秘世界加 CoT 也仅约 9%（数字以字幕转录为准）；o1（2024-09）之后多数模型仍低，无法排除训练数据见过该基准。
- TravelPlanner（2024 年初）：预算与必去/必做/禁做约束下查资料产出三天行程。模型自用工具的成功率极低，GPT-4 Turbo 约 0.6%；把信息全给也仅约 4%。典型错误：飞机第三天 8 点已起飞还安排午餐；预算从 3247 经 cost down 到 3238、3216 后放弃，其实降住宿档次即可满足 3000。解法：把满足约束交给 solver，LLM 写程序操控 solver，2024-04 用 GPT-4 与 Claude 3 可达 90%+（数字以字幕转录为准）。
- 树搜索与剪枝：Tree Search for Language Model Agents（课件印 arXiv:2407.01476）做上网任务，自问打分低于阈值就换路径。真实世界的难题是「覆水难收」：披萨已经订好做出来，没法退回搜索树。
- 世界模型/脑内小剧场：论文 Is Your LLM Secretly a World Model of the Internet 中，web agent 买东西的三个选项全在想象里逐一试（点每个按钮的后果加自评成功概率如 40%/80%/10%），真实世界只执行最优步。DeepSeek-R1 做同道叠积木题时脑内小剧场约 1500 字。The Danger of Overthinking：会脑内剧场的模型做 agent 任务总体更好，但也会想太多——对着按钮想个不停、未试先放弃，是「思考的巨人、行动的矮子」。

### 5. 原样保留区

课件原文（官方 PDF 已核对，逐字）：

```
<tool>Temperature('高雄', '2025.03.10 14:00')</tool> <output>攝氏 32 度</output>
<tool>Temperature('高雄', '2025.03.10 14:00')</tool> <output>攝氏 100 度</output>
<tool>Temperature('高雄', '2025.03.10 14:00')</tool> <output>攝氏 10000 度</output>
```

大纲页标题（课件原字）：AI Agent 關鍵能力剖析——根據經驗調整行為 / 使用工具 / 能不能做計劃。

定义对照（讲者原话要旨）：今天用 AI 是「人類給予明確指令，AI 一個口令、一個動作」；AI Agent 是「人類給予目標，AI 自己想辦法達成」。

本课无公式推导；数值约定仅 RL 的 reward（赢 +1 / 输 −1）与 goal / observation / action 记号。

### 6. 术语表

- Read / Write / Reflection：记忆三模块——检索相关经验 / 判断什么值得存 / 对记忆做抽象重组。
- 超忆症（hyperthymesia）：能记住几乎全部生活细节的罕见状态，课上用作「全量记忆未必好」的类比。
- 神秘方块世界（Mystery Blocks World）：把方块世界规则换成古怪语义以防背题的规划基准。
- Solver：约束求解器；课上指由 LLM 写程序操控、负责满足硬约束的外部求解工具。
- 脑内小剧场：用世界模型在内部模拟各选项后果、只执行最优步的规划方式。

### 7. 易错点和存疑点

- 本条的核查方式是抽查画面加通读字幕记录，并非实时全程播放；细节以课件 PDF 与字幕交叉一致者为准。
- 若干实验数字（神秘方块世界约 30%+/约 9%、TravelPlanner 约 0.6%/约 4%/90%+、R1 脑内剧场约 1500 字）来自字幕转录，引用时应标明「据课程转录」，重要引用需回原文论文核对。
- StreamBench 最佳方法（红色曲线）讲者未在课上展开，不要替它命名。
- 工具选择与自造工具部分所引多篇论文标题未在画面读清；记忆部分所引 2025 年论文全称转录不清。此两处只记结论、不记出处细节。
- 简介订正要带上：5:45 处是 AlphaZero 不是「AlphaGo Zero」。

---

## 视频 15 · Why Prompt Injection Can't Be Filtered Away

### 1. 元信息

- 标题：Why Prompt Injection Can't Be Filtered Away
- 频道：scrollypedia（约 1000 订阅）
- 链接：https://www.youtube.com/watch?v=iJwMzJ1B0Y0
- 时长：8:04（播放器结束帧实测；首次加载瞬间曾闪 8:05，以结束帧为准）
- 发布时间：2026-10-21（页面显示）
- 观看量：93 次（核查时点）
- 制作方式：页面标注本片使用 AI 制作，声音或影像经变造或完全由 AI 生成
- 字幕：英文自动字幕
- 章节：无 YouTube 章节；画面内部导航四段：1 - The attack、2 - The filters、3 - Why they fail、4 - What works（下文分段为按字幕起始时间推断的内容分段，非播放器章节，已注明）
- 核查方式：播放器全程 + 字幕记录逐字核对关键数字；简介所列来源抽查核实（Progent、CaMeL 的 arXiv 编号真实，见案头核对）

### 2. 一句话结论与摘要

一句话结论：提示注入防不住过滤器这一层——12 个已发表防御在自适应攻击下全部被攻破，因为过滤器被要求做的事本身不可能完备；真正有效的是把规则搬进模型之外的代码里执行。

摘要：
1. 过滤器四大家族（提示改写、重训、检测模型、秘密检查）在固定攻击列表上都报近 0% 的攻击成功，但攻击者会适应：12 个防御全破、多数攻击成功率超 90%，一个训练内置防御从 2% 跳到 96%。
2. 不可能性有形式化版本：防御三难困境——连续性、效用、完备性最多取二；这与 SQL 注入同根，命令与数据同通道，但 agent 的工作恰恰就是按所读内容行动，没法像数据库那样把通道分开。
3. 三个「把规则放进代码」的设计在今年独立测试中表现最好：CaMeL（规划与阅读分离）、FIDES（给数据贴信任/公开标签、工具运行前检查）、Progent（每任务最小权限写成代码策略）；共同代价是 agent 能做的正常任务变少。

### 3. 技巧总结

- **评估防御先问是不是自适应攻击**：固定列表上的近 0% 没有意义。AutoDojo 这类基准针对每个防御现生成新攻击：一个在旧测试阻止全部攻击的检测器，在新攻击下放过 28%（画面精确表述为 0% → 28%）。
- **防御三难困境（The Defense Trilemma）**：模型前过滤器最多同时保住两项——Continuity（相似输入相似处理）、Utility（正常请求完整放行）、Completeness（不安全输入全拦）。收紧规则先误伤正常工作。
- **CaMeL（Google DeepMind）**：把 agent 一分为二——planner 只看你的请求、把步骤写成小程序，reader 读邮件网页但没有任何工具；普通代码执行计划、追踪每条数据来源、行动前查策略。陌生人邮件里的地址因此不能悄悄变成文件发送目的地。作者数据：无保护 84% 任务解决率，CaMeL 安全地解决 77%，未改动底层模型。
- **FIDES（Microsoft Research）**：每条内容打两个标签（trusted/untrusted、public/private），混合时最严格者胜出；每个工具运行前声明自己接受什么。例子：agent 被植入 issue 骗开秘密文件后，所持内容全标 private，只接受公开内容的评论工具在运行前拒绝发帖。微软测试中挡住 AgentDojo 测试集的每一次注入攻击（实验功能，仅 Python 版 Agent Framework）。
- **Meta's Rule of Two**：任何一个会话里，三样东西至多给 agent 两样——untrusted input、sensitive data、the ability to act on the outside world。
- **Progent**：每任务一份简短策略，用代码写明可调用的工具与参数，其余全屏蔽（例：交房租任务只许 read_inbox，只许 send_money 给 recipient == landlord）。创建者固定测试：攻击成功率 39.9% → 1.0%；AutoDojo 同攻击同模型：无防御 52.4%、最佳过滤器 28.0%、Progent 7.7%；另一研究 25.8% → 4.2%，且专门为击败它构造的攻击只达到 2.6%。限定：测试都用较小模型、攻击者看不到模型内部；正常任务完成量至多少 7 个百分点。

我的思考：这一条对我自己的记忆安全研究是个直接的外部校验。过滤器全破的深层原因是「判断者与被判断者同通道」：让模型判断一段文本是不是注入，等于让被攻击面自己守自己。我那条线里 D2（来源加权淘汰）之所以是唯一单独清零损害的防御，结构上正是因为它不判断内容、只看来源——与这一条里「规则在代码中、按来源与权限执行」是同一个原理的两次独立出现。CaMeL 的 planner/reader 分离还值得单独记一笔：它对应记忆系统里的「写入通道与读取通道分离」，我的分层时标设计里判据层「只许流外更新」就是这个分离在时间维度上的版本。另外要泼一盆冷水：这条视频是 AI 生成解说的二手梳理，数字虽经画面与简介双通道核对，正式引用任何一条仍应回原始论文。

时效性（截至 2026-11-01）：发布仅十天，是本批最新的一条；自适应攻击的数字会随新基准快速过时，但「过滤不完备、设计型防御占优」的方向性结论与 2025–2026 年的论文走向一致，短期内耐用。

### 4. 详细笔记（按内容分段）

- **开场攻击示例（0:00–0:52）**：让 agent 替你浏览网页，网站能否诱使它在你电脑上跑攻击者的代码？答案是能。2026 年 8 月一名安全研究人员用 Claude Code 证明，多数尝试成功，即使有安全检查器逐步审查每一步。关键转折：agent 其实很谨慎——它拒绝运行网站提供的程序，自己写了一个，而运行自己的代码恰恰让攻击进入（它在下载文件夹里运行自写程序，Python 在那里加载了攻击者的文件）。画面补充：第二名研究人员用不同手法在 6 of 10 tries 中让 agent 运行了外部代码；Anthropic 据称称其检查器为 best-effort 而非安全保证；画面底标 ATTACK SUCCESS 60-80%；来源小字可读者为 The Register, Aug 28 2026 与 Claude Code / Auto Mode。
- **过滤器四类（0:52–1:51）**：最直观的修复是叫模型小心、忽略可疑内容，旁白比喻约等于在幼儿面前立一块湿油漆告示。四大家族：(1) Prompting（Spotlighting、Prompt Sandwiching 等：把外部文本标记为数据、或在其后重复用户请求）；(2) Training（Circuit Breakers、StruQ、MetaSecAlign：重训模型忽略埋在数据中的命令）；(3) Detector models（Protect AI、PromptGuard、Model Armor：第二个模型扫描攻击迹象）；(4) Secret checks（Data Sentinel、MELON：如把任务重跑一次比较 agent 试图做什么）。画面标：12 PUBLISHED DEFENSES、4 FAMILIES、REPORTED ATTACK SUCCESS 近 0%——但分数来自已知攻击的固定列表。
- **自适应攻击击破（1:51–2:47）**：去年底一组研究人员对 12 个已发表防御（涵盖 prompt injection 与 jailbreaks）用自动搜索、试错程序和人工持续攻击，结果 All 12 fell，多数防御攻击成功率超 90%，训练内置的一个（画面指明 MetaSecAlign）从 2% 升至 96%。画面另称一场 500-person contest 中人类攻破全部 40 个挑战；这类过滤器还会让正常任务减少四分之一到一半；4 月研究中依赖模型自身判断的防御在 300 rounds of attacks 内被攻破。旁白限定：各防御测试方式不同，条形图不是排名；它们以同样方式失败——问题不在过滤器如何建造，而在它被要求做什么。
- **为什么不可能完备（2:47–4:03）**：与 SQL injection 同根（命令与数据同通道）；数据库的修法是查询与数据分开发送，但 agent 按所读内容行动就是它的全部工作（一封说退款已批准的邮件可能真的意味着应当处理它）。今年春季的数学证明：任何位于模型前、放过正常请求且对相似输入作相似处理的过滤器，必须放过一些不安全输入；该证明甚至被软件逐行验证。另一篇（May paper）：攻击者总能把被禁动作装扮成合法动作。画面桥段：April testing 中，应用自身代码里一条固定规则（拦截任何包含已知秘密的回复）在 15,000 attacks 中 leaked nothing（0 / 15,000）——引出「规则在模型之外会怎样」。
- **CaMeL（4:03–5:00）**：机制与数字见第 3 节。画面限定：今年独立 AutoDojo 测试评 CaMeL 为最有效防御，但也是在五个模型中的四个上完成正常工作最少的防御。
- **FIDES（5:03–5:44）**：全称 FIDES Flow Integrity Deterministic Enforcement System；机制与例子见第 3 节。画面标签流：Developer instructions = TRUSTED / PUBLIC；Planted GitHub issue = UNTRUSTED / PUBLIC；Secrets file = TRUSTED / PRIVATE；混合后 Everything the agent holds = UNTRUSTED / PRIVATE；post_comment() 因 accepts public content only 被 refused before it runs。画面底标：LABELS PER ITEM 2、TEST ATTACKS STOPPED All、STATUS Experimental。
- **Meta's Rule of Two（5:47–5:57）**：见第 3 节。旁白随即提醒：上述结果都来自固定测试，问题是攻击者持续适应后是否仍成立——由此引出 Progent。
- **Progent（6:04–7:08）**：机制与三组数字见第 3 节。视频的实践结论：假定模型有时会被骗，仍然要在代码中决定它被允许做什么。
- **总结（7:11–8:04）**：过滤器在自适应攻击下全破（12/12，今年新基准上再次如此）；证明解释了模型前过滤器为何不可能完备；把规则移入代码（拆分规划与阅读 / 给数据贴标签 / 精确列出每任务可做什么）是今年独立测试中表现最好的方向，代价是 agent 可做事项减少。与该频道此前 AI 安全一集的建议一致：关键规则应在代码中执行，而不是写在提示中。下集预告：sandboxes、network locks 与 hardware checks。

### 5. 原样保留区

三难困境（画面原文）：
- Continuity — similar inputs get similar treatment
- Utility — normal requests pass through intact
- Completeness — every unsafe input is stopped

关键数字组（画面精确值，优先于旁白约数）：
- Progent 固定测试：from 39.9% to 1.0%
- AutoDojo（脚注标注 GPT-4o-mini）：NO DEFENSE 52.4% · BEST FILTER 28.0%（PiGuard；另一过滤器 DataFilter 为 33.4%）· PROGENT 7.7%
- Progent 另一研究：from 25.8% to 4.2%，专门构造的攻击 reached only 2.6%
- CaMeL（作者数据）：77% of tasks solved securely vs 84% undefended, model unchanged
- 秘密规则桥段：0 / 15,000

简介所列主要来源（写引用时回原文核对）：Ma et al., AutoDojo (arXiv 2606.15057)；Narisetty et al., Adaptive Evaluation of Out-of-Band Defenses (arXiv 2606.26479)；Abdelnabi & Bagdasarian, AI Agents May Always Fall for Prompt Injections (arXiv 2605.17634)；Shi et al., Progent (arXiv 2504.11703, v3 May 2026)；Deep et al., Evaluation of Prompt Injection Defenses in LLMs (arXiv 2604.23887)；Bhatt et al., The Defense Trilemma (arXiv 2604.06436)；Microsoft Learn, Agent Security with FIDES (Oct 2026)；Costa et al. (arXiv 2505.23643)；Debenedetti et al., CaMeL (arXiv 2503.18813)；Nasr et al., The Attacker Moves Second (arXiv 2510.09023)；The Register, Claude Code Auto Mode report (Aug 28, 2026)；Meta, Agents Rule of Two。

### 6. 术语表

- Prompt injection：把指令藏进 agent 要读的数据里，让模型分不清用户指令与数据指令的攻击。
- Adaptive attack：攻击者观察防御的拦截结果、不断改换手法的攻击方式；与固定攻击列表相对。
- Least privilege（最小权限）：每个任务只授予完成它所必需的工具与参数范围。
- AgentDojo / AutoDojo：agent 注入攻击的基准；AutoDojo 针对每个防御自动生成新攻击。

### 7. 易错点和存疑点

- 旁白用约数、画面给精确数：引用一律用画面精确值（如 39.9% → 1.0%，不要写成「40% 到 1%」而不注明约数）。
- 本视频无 YouTube 章节，第 4 节的分段时间是按字幕推断的内容分段，不是播放器章节。
- 自动字幕误听：Claude Code 被写成 clawed code、CaMeL 写成 camel、FIDES 写成 Feedes、Progent 写成 Progendent，均以画面与简介拼写为准。
- unreadable 小字：开场来源小字的其余部分、过滤器画面中个别防御名称、除 MetaSecAlign 外其他防御的固定测试前值、Rule of Two 图下方小字、Progent 图脚注的部分小字——均未读出，不补写。
- 2026 年的几篇新论文（AutoDojo 等）只抽查了 Progent 与 CaMeL 两条编号，其余编号按「据视频简介」引用。

---

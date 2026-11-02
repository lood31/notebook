# YouTube Agent 技巧视频笔记 · 第 41–45 条

> 核验型笔记第三批之第三组（记忆、安全、评估与可观测性）。取材标准：字幕、时间戳、代码原文三样齐全；播放器逐章核验，配套仓库克隆对照，能跑的代码实际跑过；看不清处标「不可读」，推断与原文分开标注。本组多条视频的幻灯片与小字代码不可读，相关结论按口述层记录并在各节存疑点中标明。

## 目录

- [41. Install Mem0 with Ollama Locally - A Hands-on Tutorial](#41-install-mem0-with-ollama-locally---a-hands-on-tutorial)
- [42. MCP Security Fundamentals Course](#42-mcp-security-fundamentals-course)
- [43. Build an Online LLM-as-a-Judge with the LangSmith CLI and a Coding Agent](#43-build-an-online-llm-as-a-judge-with-the-langsmith-cli-and-a-coding-agent)
- [44. Mastering LLM Chatbots And RAG Evaluation Crash Course](#44-mastering-llm-chatbots-and-rag-evaluation-crash-course)
- [45. Langfuse Walkthrough - Full Demo of Agent Tracing, Evals, Experiments and Prompt Management](#45-langfuse-walkthrough---full-demo-of-agent-tracing-evals-experiments-and-prompt-management)

---

## 41. Install Mem0 with Ollama Locally - A Hands-on Tutorial

### 一、元信息

- 标题：Install Mem0 with Ollama Locally - A Hands-on Tutorial（页面原样）
- 链接：https://www.youtube.com/watch?v=Q-4pkwHiAkA
- 频道：Fahd Mirza
- 时长：8:44（播放器实测）
- 发布时间：2024-08-05（首播）
- 观看量：4,169 次；点赞 78
- 配套代码：简介有讲者博客代码页链接，页面显示被省略号截断、无法还原，本笔记代码以画面为准
- 核验档：B 档（播放器逐章扫 + 现行官方文档与版本案头核查）
- 章节（简介与播放器一致，共 4）：0:00 Introduction；2:58 Local setup and installation；4:15 Python memory management；7:57 Conclusion and limitations

### 二、一句话结论与摘要

这是一条 2024 年 8 月的 Mem0 本地部署演示，价值不在操作本身，而在它当场撞上了那个版本的核心限制：标题写着全本地，实际嵌入环节还离不开 OpenAI——讲者自己判定这违背了本地化的初衷。这个限制如今已被官方修复，所以本条笔记的正确读法是「版本史」：看清一个记忆框架在早期长什么样、后来补上了什么。

- 演示环境：Ubuntu 22.04、RTX A6000 48GB、Python 3.11.9；Ollama 侧模型为 llama3.1:latest（对话）与 nomic-embed-text:latest（已安装但当时没能用上，见下）。
- 操作面是 Mem0 的完整 CRUD：add / get_all / get / search / update / delete / delete_all，以用户 alice 的 hobbies 类记忆走了一遍。
- 时效性（截至 2026-11-01）：现行 Mem0 官方文档（本地 companion 页，页面更新 2026-08-14）明确支持 Ollama 同时作 embedder 与 LLM，全本地配方用的正是 nomic-embed-text:latest；mem0ai 已到 2.2.1，仓库 68,201 stars（2026-11-01 实查）。视频口述的约 19k stars 是 2024-08 时点。视频结论只适用于 2024 年的版本。

### 三、技巧总结

- 技巧 1：「全本地」要逐环节验，不能只看标题。演示里对话模型换成了 Ollama，但环境里仍然躺着 OpenAI 的 key，因为嵌入当时没有 Ollama 通道。我的思考：记忆系统有抽取（LLM）、嵌入、向量库三段链路，任何一段在云端，「本地部署」就只是部分成立；验收时按环节逐段问「这一步的请求发去哪了」，比看架构图可靠。
- 技巧 2：记忆写入不是一对一的。画面里一次 add 在 get_all 与 search 里都出现两条同文本、同 hash、不同 ID 的记录（相隔约 1 秒，search 分数完全相同），口述却说只有一条。我的思考：记忆层的 add 语义可能是「抽取后写入多条事实」，调用方不能默认一次 add 等于一条记录；做去重与计数时要以读回结果为准，不要以调用次数为准。
- 技巧 3：存进去的文本会被改写。画面显示输入文本在存储时被规范化（大小写与措辞变化）。我的思考：记忆框架普遍会做事实抽取与改写，所以「我存了什么」和「库里有什么」是两个问题；调试记忆质量时先 get 回来看原文层，再看检索层。
- 技巧 4：删除路径要实测到条。重复的那条记录最后是靠 delete_all 清掉的，没有演示逐条删重复项。我的思考：delete_all 能过演示不等于删除能力完整；记忆系统选型时值得专门测一遍「删掉指定一条、且只删这一条」。

### 四、详细笔记（按章节）

- 0:00 Introduction：Mem0 被介绍为给 LLM 用的自适应记忆层；讲者提到它由 Embedchain 改名而来。演示目标是把 Mem0 与 Ollama 装到本地跑通。
- 2:58 Local setup and installation：建 conda 环境（画面命令里的环境名与后续提示符显示不一致，见存疑点）、pip 安装 ollama 与 mem0ai；ollama 列表里三模型在屏：mixtral:8x7b（26GB）、llama3.1:latest（4.7GB）、nomic-embed-text:latest（274MB）。讲者明确说 Mixtral 跑不通、要用 Llama 系模型。
- 4:15 Python memory management：配置以画面为准——llm provider 为 ollama、模型 llama3.1:latest、temperature 0.1、max_tokens 2000，经 Memory.from_config 构造；随后对用户 alice 的 hobbies 做全套 CRUD 演示：add 一条、get_all、get、search、update、delete，最后 delete_all 清空并展示空列表。两次 add 的重复记录与文本规范化现象出现在这一章（见技巧 2、3）。
- 7:57 Conclusion and limitations：讲者收束时明说嵌入仍需 OpenAI 模型、当时在仓库与文档里都找不到 Ollama 嵌入选项，并判定这与本地化的目的相悖；同时演示代码里仍要设置 OpenAI API key，与标题的「Locally」形成本条最大的名实差距。

### 五、原样保留区

画面配置（结构转录，key 值画面为空字符串，未记录）：

- llm：provider = ollama，model = llama3.1:latest，temperature = 0.1，max_tokens = 2000
- 构造方式：Memory.from_config(config)
- 演示对象：user_id = alice，category = hobbies（画面口径）

重复记录现象（画面输出层，未实测复现）：一次 add 后 get_all / search 均出现 2 条，文本与 hash 相同、ID 不同、生成时间相隔约 1 秒，search 分数同为 0.2560…（画面显示位数有限，其余不可读）。

现行文档核查（2026-11-01，非视频内容）：Mem0 官方本地 companion 文档给出 Ollama 全本地配方——LLM 用 llama3.1:latest、embedder 用 nomic-embed-text:latest、向量库 Qdrant 本地运行；即视频里「装了却没用上」的嵌入模型，现行版本已经接通。

### 六、术语表

- Mem0：给 LLM/agent 用的记忆层框架，前身为 Embedchain；负责事实抽取、存储与语义检索。
- embedder：把文本转成向量的嵌入模型环节，是记忆链路里最容易被忽略的外部依赖。
- CRUD：增删改查四类基本操作，本条以一条用户记忆完整走了一遍。
- delete_all：按用户/类别整体清空的删除方式，能过演示但不证明逐条删除可靠。

### 七、易错点和存疑点

- 标题与实作不符是本条头号事实：2024 版的「本地」只覆盖对话模型，嵌入仍走 OpenAI；引用本条时必须带年份，否则会把已修复的限制说成现状。
- conda 环境名画面自相矛盾：创建命令写作 mem1，后续提示符显示 (mem)，无法判定哪个为准。
- 两次 print 后出现无解释的 KeyboardInterrupt 行，演示未提及，原因未明。
- 简介里的代码页链接被页面截断，未能还原，代码原文以画面为唯一来源；个别小字参数不可读处未猜测。

---

## 42. MCP Security Fundamentals Course

### 一、元信息

- 标题：MCP Security Fundamentals Course（页面原样）
- 链接：https://www.youtube.com/watch?v=gYXLRMycVMk
- 频道：freeCodeCamp.org；讲者 Dan Barahona（AI Security University）
- 时长：2:10:15（播放器实测；简介尾部的证书条目写作 2:10:20，比片长多 5 秒）
- 发布时间：2026-10-22
- 观看量：95,609 次；点赞 824
- 配套材料：简介内有证书测验入口（aisec.university/fcc-mcp，完成可得 2 CPE 学分，课程口径）
- 核验档：C 档（讲授型课程；多数幻灯片正文不可读，仅标题页与 4:12 一张代码页清晰核验，结论以口述与简介章节为准）

### 二、一句话结论与摘要

这是一门把 MCP 安全讲成「结构问题」的完整课程：先给出致命三件套这个判定框架，再按 OWASP MCP Top 10 逐项过定义、真实事件、检测信号与修复动作。它的用法不是当新闻看，而是当检查表用——每讲完一个风险类别，都对应一个可以立刻执行的动作。

- 致命三件套（Lethal Trifecta）：私有数据、不可信内容、对外通信三者叠加即结构性脆弱，防御就是拆掉其中一条腿；课程口述将其归于 Simon Willison 2025 年 6 月的提法（字幕把姓氏转录为 Willis，该归属未在案头核查中独立证实，见存疑点）。
- 十个风险类别（课程口径）：MCP01 令牌与密钥暴露；MCP02 权限范围蔓延；MCP03 工具投毒；MCP04 供应链；MCP05 命令注入；MCP06 意图流颠覆；MCP07 认证与授权不足；MCP08 审计与遥测缺失；MCP09 影子服务器；MCP10 上下文注入与过度共享。
- 时效性（截至 2026-11-01）：OWASP MCP Top 10 是真实存在的活项目，课程条目命名与 2026-11-01 实查的一份公开映射逐项吻合；但不同第三方映射之间互不一致，这是快照而非唯一官方版本。开场案例 Postmark 事件已由案头核查证实（见五）；其余事件与统计数字为课程叙述，未逐一外查，引用须带此限定。

### 三、技巧总结

- 技巧 1：先判定结构，再谈漏洞清单。课程先讲三件套，再讲 Top 10，顺序是刻意的：一个系统只要三条腿齐全，具体的洞只是时间问题；拆腿是架构动作，打补丁是运维动作，优先级不同。我的思考：给自己的 agent 做安全评审时，先画三件套图——它能读到什么私有数据、会吞什么不可信内容、能向外发什么——再对照清单逐项查，比直接背 Top 10 更快定位要害。
- 技巧 2：令牌不要穿过 agent。课程在 MCP01/07 的修复里反复给同一条处方：用保管库与短期、窄权限令牌，令牌交换、禁止透传（课程提到 RFC 8707 资源指示符）。我的思考：长效全权 token 放在上下文里，等于把钥匙和信件放在同一封信里传递；任何一处上下文泄漏都直接升级为凭据泄漏。
- 技巧 3：工具定义要签名与钉版。MCP03 工具投毒与 MCP04 供应链的共同修复是：对工具 schema 与版本做签名、固定哈希、升级要重新审核。我的思考：Postmark 案的教训正是「前 15 个版本干净」——信任不能按历史版本累积授予，每次定义变更都是一次新的接入评审。
- 技巧 4：影子服务器靠盘点，不靠自觉。MCP09 给出的动作是中央注册表加发现扫描；课程引用的欧洲银行审计在客户环境里发现 47 台未注册服务器（课程叙述）。我的思考：个人与小团队同样适用——定期扫一遍本机与项目里的 MCP 配置文件，清单之外的服务器一律先下线再问原因。
- 技巧 5：每个风险类别都落一个「周一上午动作」。课程结构是定义、三件套映射、事件、检测信号、修复、一个立即可做的动作。我的思考：安全课最容易变成知识消费，这个结构把每章都压出一个可执行项；自己做评审记录时可以照抄这个模板，写不出动作的那一项就是还没想清楚。

### 四、详细笔记（按章节）

- 开场与 Postmark 案：以社区版 postmark-mcp 在 1.0.16 版藏入隐藏 BCC、一行代码把经手邮件抄送到外部服务器开场（案头核查证实，细节见五），用它立起「工具可以先装乖、再变质」的全课基调。
- Before / After MCP 与架构世代：先对比有无 MCP 时代的集成方式，再讲架构演变，落点是 MCP 把「模型—工具—数据」的信任边界一次性放大，所以安全问题是结构性的。
- Anatomy of MCP 与 Lethal Trifecta：拆解协议组成后给出三件套框架与拆腿原则（本节是全课的判定核心）。
- MCP01–MCP10 逐项（每项同一结构）：01 令牌/密钥暴露（处方：保管库、短期令牌、不透传）；02 范围蔓延（最小权限、即时授权）；03 工具投毒（以 Invariant 的 WhatsApp 投毒演示为例，处方：签名与钉版 schema）；04 供应链；05 命令注入（处方：数组式进程执行、不拼 shell 字符串；课程称其抽测约 43% 的被测服务器中招，调查来源画面不可读）；06 意图流颠覆（以 GitHub MCP 毒性流为例）；07 认证与授权不足；08 审计与遥测缺失（处方：结构化、防篡改的工具调用日志进 SIEM；本项简介承诺的「每项一个事件」在此落空，课程用四个典型场景代替）；09 影子服务器（以欧洲银行审计 47 台为例）；10 上下文注入与过度共享（以 Asana MCP 跨租户泄漏为例，处方含租户隔离进 CI 测试）。
- 其余被引事件（均为课程叙述，未逐一外查）：Claude Code 配置类 CVE 评分 8.7/5.3；Replit agent 删库事件（涉 1,200 家公司高管数据、约 4,000 条伪造用户、自评 95/100）；MCP Inspector 远程代码执行评分 9.4、周下载 38k+；mcp-remote 远程代码执行评分 9.6（JFrog，2025 年 7 月）。
- Best Practices 与结业：收束为六支柱最低线与十项「周一上午」检查；课程给出的生态数字包括公开 MCP 服务器逾 10,000、下载量 100M，以及一份 2026 年 2 月调查的 3M agent、47% 无监控、88% 组织发生或怀疑发生过事件——均为口述，调查方名称画面不可读。

### 五、原样保留区

开场案例案头核查（2026-11-01，非视频内容，与课程叙述对照）：Postmark MCP 事件经多家独立报道证实——Koi Security 2025 年 9 月报告指出，一个仿冒官方名的 npm 包在 15 个干净版本后，于 1.0.16 版加入一行 BCC，把经手邮件抄送至 giftshop.club 关联服务器；周下载约 1,500，估计每日外泄邮件数以千计；Postmark 母公司 ActiveCampaign 公开声明该包与官方无关；报告研究者为 Idan Dardikman，包发布者为 @phanpak。多家报道同时指出这是行为后门而非代码漏洞、没有 CVE 编号。课程对该案的讲述与上述报道一致。

OWASP 列表核查（2026-11-01）：课程的十条命名与公开映射之一（mcpwall 映射页）逐项吻合，包括 MCP01 的完整名称 Token Mismanagement & Secret Exposure 一类细节；但实查同时发现其他扫描器给出互不一致的编号表，故课程列表只应当作一个时点快照引用。

画面实测项：全课仅标题幻灯片与 4:12 处一张代码幻灯片清晰可读，其余幻灯片正文不可读——本节之外凡涉幻灯片细节处，笔记均按口述层处理。

### 六、术语表

- Lethal Trifecta（致命三件套）：私有数据 + 不可信内容 + 对外通信三者叠加的结构性风险框架，防御思路是拆掉任意一条腿。
- 工具投毒（tool poisoning）：在工具描述里藏入影响模型行为的指令，用户界面上不可见。
- 影子服务器（shadow MCP servers）：未经注册与审批就接入环境的 MCP 服务器，靠盘点与发现扫描治理。
- rug pull（抽地毯式变质）：工具或包先以干净版本积累信任，再在后续版本植入恶意行为，Postmark 案即属此型。
- RFC 8707：OAuth 资源指示符标准，课程用它支撑「令牌按受众限定、禁止透传」的处方。

### 七、易错点和存疑点

- 三件套的提出者归属（Simon Willison，2025 年 6 月）只来自口述，字幕转录有误，本组未独立证实，引用时保留「课程归于」字样。
- 课程数字大多无可读出处：43% 命令注入、47 台影子服务器、2 月调查三数字等，均为口述层，未外查前不要转述为事实。
- 简介与内容有两处小出入：MCP08 没有承诺中的单个事件；MCP07 的简介标题少了口述中的「and Authorization」。
- MCP 从发布（2024 年 11 月）到捐赠（2025 年 12 月）被课程说成「一年」，实际约 13 个月，小账不符但无关主旨，照录备考。

---

## 43. Build an Online LLM-as-a-Judge with the LangSmith CLI and a Coding Agent

### 一、元信息

- 标题：Build an Online LLM-as-a-Judge with the LangSmith CLI and a Coding Agent（页面原样）
- 链接：https://www.youtube.com/watch?v=XE5-hb-H-OY
- 频道：LangChain（官方）
- 时长：5:07（播放器实测）
- 发布时间：2026-09-10（画面内录制日期为 2026-08-21，早于发布）
- 观看量：3,852 次；点赞 39
- 配套材料：简介链接 LangSmith CLI 与在线评估文档；操作所用 skills 来自仓库 langchain-ai/langsmith-skills（案头核查：真实存在，163 stars，MIT，2026-11-01 实查）
- 核验档：B 档（播放器逐章扫 + 仓库与版本案头核查；字幕面板加载不出，提取以播放器内字幕与画面为准）

### 二、一句话结论与摘要

这条 5 分钟短片演示了一种新的搭建姿势：不用手点控制台，而是把官方 skills 装给编程 agent，让它用 CLI 在 LangSmith 里建出一个在线 LLM 评审——按完整对话线程打分、自动盯生产流量。重点不在评估器本身，而在「运维动作交给 agent 按官方技能执行」这条工作流。

- 场景：Chat LangChain 每天产生数千条 trace，人工读不完；解法是在线评审（不是离线跑集）：trace 进来，评审读整条线程、按量规给二元分并附理由，结果作为 feedback 挂回 trace。
- 成品评估器：名为 User Frustration (Thread)、线程级作用域、反馈键 user_frustration、二元 0/1、评审模型在画面上写作 GPT-5.6 Luna 高推理档、初始采样 100%。
- 时效性（截至 2026-11-01）：langsmith 已到 0.14.16（PyPI 实查）；画面里的 Codex 版本 0.148.0 是 2026-08 录制时点。视频演示的成本数字（见四）是那一周的真实账单快照，不应当作单价引用。

### 三、技巧总结

- 技巧 1：在线评审与离线评估分工不同。在线评审盯的是生产流量的长尾——你不知道哪条会出问题，所以要全量或采样地自动打分，把可疑线程筛出来给人看；离线评估盯的是改动前后的回归。我的思考：把两者混为一谈的团队常见症状是拿离线集的高分给生产放心；本条的筛查逻辑（先机器筛、后人工看被标记的）才是生产侧的正确顺序。
- 技巧 2：评审单位选线程而不是单条消息。用户挫败是跨轮累积的信号，单条消息看不出来；评估器按 thread 分组、等线程静默 120 秒再评。我的思考：选评估粒度时先问「信号在哪一层才成形」——成形在会话层，就不要在消息层设评审，省下的钱会以漏检的形式还回去。
- 技巧 3：采样率是旋钮，不是信仰。建时 100% 是为了先看清评审质量与成本，确认后再降到 50%；改动只动采样率一个字段、走高级规则接口的部分更新。我的思考：先全量采样拿到成本基线，再按预算下调，比一上来拍脑袋定 10% 更稳妥；讲者预期成本减半只是期望、没有实测，视频里也没有给——期望和实测要分开记。
- 技巧 4：搭建动作本身可以委派。装上官方 dataset / evaluator / trace 三个 skills 后，建评估器、改采样率都由编程 agent 经 CLI 完成，人只给目标与参数。我的思考：这要求官方把操作知识写成 agent 可执行的 skill，而不是只写给人看的文档；对自家工具链的启示是，重复性的控制台操作值得逐个沉淀成这种可委派单元。

### 四、详细笔记（按章节）

- 0:00 从终端扩展：开场立意——评估要能从终端规模化操作，引出用 CLI 加编程 agent 搭建的路线。
- 0:12 Chat LangChain 的问题：日均数千条 trace，人工审读不可行，需要自动盯生产。
- 0:37 在线评审结构：trace 触发评审，评审读全线程、按量规产出二元分数与理由，挂为 feedback；与离线跑集的区别在此讲清。
- 1:24 安装 LangSmith skills：用 npx skills add 从 langchain-ai/langsmith-skills 装入 dataset、evaluator、trace 三个 skill，给 Codex 提供操作 LangSmith 的现成知识。
- 1:55 经 CLI 创建挫败评审：Codex 按提示用 CLI 建成评估器，画面显示创建耗时 4 分 27 秒；关键参数：项目 chat-langchain-external、线程级、反馈键 user_frustration、全对话模板变量、按 thread_id 分组、120 秒静默窗口、采样 100%。
- 2:54 在 UI 里核对：控制台里评估器处于启用状态，该周其所在行显示 13 次运行、花费 0.64 美元；项目当周总览为花费 48.19 美元、5,302 条评估 trace（2026-08-17 至 23 日一周，画面口径）。
- 3:14 复查被标记线程：一条 6 轮线程被判 1.00（挫败），理由指向用户在多次被拒后的激烈措辞；该线程开销 0.0559 美元、363.3K tokens，P50 2.93 秒 / P99 3.37 秒。
- 4:05 更新采样率：Codex 经高级规则接口只 PATCH 采样率字段，100% 改到 50%，画面显示耗时 1 分 25 秒；讲者预期成本随之减半（未实测）。
- 4:45 收尾：总结从终端建在线评审的完整链路。

### 五、原样保留区

评估器量规（口述要点转录，非逐字原文）：仅当用户对聊天机器人明确表达挫败或不满时判 1；中性、积极或含义不清时判 0；不得仅凭任务难度或用户措辞简短推断挫败。

画面数字（原样）：评估器行 13 次运行 / 0.64 美元；项目周总量 48.19 美元 / 5,302 条评估 trace；被标记线程分数 1.00、6 轮、0.0559 美元、363.3K tokens、P50 2.93s、P99 3.37s；创建时间 4 分 27 秒，采样更新耗时 1 分 25 秒，采样更新那次会话的 token 总量画面显示 155,486。

案头核查（2026-11-01，非视频内容）：langchain-ai/langsmith-skills 仓库存在且近期仍在更新，三个 skill 的定位与视频一致；视频中评估器 ID 与创建所用 rule ID 画面不可读，本笔记不记录。

### 六、术语表

- 在线评估（online evaluation）：对生产实时流量自动打分的评估方式，与发布前离线跑集相对。
- LLM-as-a-judge：用一个模型按量规给另一个模型的输出打分，本条为二元判定加文字理由。
- 采样（sampling）：只按比例评审一部分流量，用成本换覆盖率；100% 为全量。
- feedback key：评分挂回 trace 时的字段名，本条为 user_frustration。

### 七、易错点和存疑点

- 字幕有一处把当前采样率读成 200%，画面、创建小结与更新提示三处都写 100%，以画面为准，200% 是字幕错误。
- 「成本减半」是讲者期望而非实测：视频未展示更新后的账单，最终 50% 状态只有 Codex 的终端输出为据，控制台没有二次刷新确认。
- skills 被称作「最新版」，但画面任何地方都没有给出版本号，引用时不要补造版本。
- 本片录像里出现的模型名与版本号都带录制时点（2026-08-21），比发布日早约三周，读时请按录制时间理解。

---

## 44. Mastering LLM Chatbots And RAG Evaluation Crash Course

### 一、元信息

- 标题：Mastering LLM Chatbots And RAG Evaluation Crash Course（页面原样）
- 链接：https://www.youtube.com/watch?v=NebOSOTp-zA
- 频道：Krish Naik
- 时长：1:06:11（播放器实测）
- 发布时间：2026-03-02
- 观看量：79,059 次；点赞 1,346
- 配套代码：krishnaik06/RAG-Tutorials 仓库中 1-rag_evaluation.ipynb（简介直达，已克隆对照；仓库 506 stars，GPL-3.0，2026-11-01 实查）
- 核验档：A 档（播放器逐章扫 + 仓库克隆解明不可读代码；实验分数仓库未存档，按口述层记录）
- 章节（播放器，共 5）：0:00 Introduction；18:06 Metrics- LLM As A Judge；26:18 Running Evaluation On Different Metrics；34:15 Rag Evaluation Create DataSet For Rag；49:53 RAG Evlauation Metrics（末章拼写 Evlauation 为页面原样）

### 二、一句话结论与摘要

这是一堂把「聊天机器人评估」和「RAG 评估」并排走完的实作课：两边共用同一套路——先建带标准答案的小数据集，再用 LLM-as-a-judge 写评估器，最后在 LangSmith 里跑实验比模型。RAG 侧的四个评估器按「谁和谁比」拆得最清楚：答案对标准答案、答案对问题、答案对检索文档、文档对问题，一格一个判定。

- 聊天机器人侧：5 条样例数据集，两个评估器——correctness（评审模型 gpt-4o-mini、温度 0、二元判定）与 concision（输出长度小于标准答案 2 倍）；随后只换目标模型重跑实验，演示按分数选型。
- RAG 侧：三篇博客建小 RAG（切分 250、重叠 0、检索 k=6，均经仓库证实），3 条测试集，四个结构化输出评估器（量规类都是「解释在前、布尔在后」的 TypedDict）。
- 时效性（截至 2026-11-01）：视频 2026-03 发布，langsmith 现已 0.14.16；仓库 notebook 仍在、与画面大体一致。不合与未决主要在分数层：讲者口述的第二轮正确率先说 1 又自我更正为 0.6，仓库没有存任何实验输出，本笔记按口述记录并标明未决，不替他抹平。

### 三、技巧总结

- 技巧 1：评估器先按「比较对」分类，再写提示。RAG 四指标本质是四种比较：correctness 是回答 vs 参考、relevance 是回答 vs 问题（不需要参考答案）、groundedness 是回答 vs 检索文档、retrieval relevance 是文档 vs 问题。我的思考：写任何评估器之前先填这张「谁和谁比」的表，能立刻看出缺哪一格——很多团队只测 correctness，于是检索坏没坏永远查不出来，本课把 retrieval relevance 单列正是对此的纠正。
- 技巧 2：布尔判定加解释字段，且解释排在前面。四个量规类都是 TypedDict，先 explanation 后布尔字段；仓库注释明说字段定义顺序就是生成顺序，先解释能逼模型先想再判。我的思考：这与第 36 条的结构化分类、第 31 条的作业护栏是同一招的第三次出现——判定类结构化输出里，reasoning 字段的位置是零成本的质量项，值得固化成默认写法。
- 技巧 3：选型实验只动一个变量。聊天侧两轮实验之间只换目标模型，数据集、评估器、实验框架全不动，分数差异才可归因。我的思考：这条与第 43 条的在线评审正好互补——离线实验负责选型与回归，在线评审负责生产长尾，混用对方的指标都会得出假安全感。
- 技巧 4：concision 这类规则指标不要交给评审模型。它就是一个长度比较，仓库里是一行 int(len(...) < 2 * len(...))。我的思考：能写成确定性代码的指标就别花评审模型的钱和不确定性；评审只留给语义判定（对不对、有没有依据），规则判定回代码，这是评估成本控制的第一刀。
- 技巧 5：数据集小不是问题，没有才是问题。聊天 5 条、RAG 3 条，规模小到像玩具，但完整走通了建集、跑实验、比分数的闭环，后续只需要往里加样例。我的思考：评估体系的门槛从来不是题量，是有没有一条带标准答案、可重复运行的链路；先小闭环、再扩集，比先攒一百道题再搭管线更现实。

### 四、详细笔记（按章节）

- 0:00 Introduction：立题——模型选型不能只看价格，要用例上的准确率；评估需要标准答案（ground truth）、要先定指标、方法用 LLM-as-a-judge。工具选 LangSmith 的理由是追踪与实验报告都在一处。随后在控制台建 API key、装 langsmith 与 openai、用 Client 建聊天数据集（仓库证实数据集名为 Chatbots Evaluation，共 5 条问答，问的是 LangChain / LangSmith / OpenAI 是什么一类问题）。
- 18:06 Metrics- LLM As A Judge：定义两个评估器。correctness：用 wrap_openai 包装的客户端调 gpt-4o-mini、温度 0，指令是「专家教授批改学生答案」口径，返回 CORRECT/INCORRECT 再映射布尔（函数原文见五，仓库层）。concision：输出长度小于参考答案 2 倍即通过，纯代码判定。
- 26:18 Running Evaluation On Different Metrics：用 evaluate 跑目标函数对数据集、挂两个评估器，在 Datasets and Experiments 里并排看输入、参考与生成。口述分数：第一轮 correctness 0、concision 0.40；换 GPT-4 Turbo 后讲者先说 correctness 是 1，随即更正为 0.6，又提到 0.2 但归属不明——最终画面数字不可读，仓库也无存档，本条按「自我更正、未决」记录（仓库可证的只有第二轮目标模型在代码里写为 gpt-4-turbo）。
- 34:15 Rag Evaluation Create DataSet For Rag：建 RAG 本体——WebBaseLoader 载入三篇博客（仓库证实是 Lilian Weng 的 agent、prompt-engineering、adv-attack 三篇）、RecursiveCharacterTextSplitter 切分（仓库值：chunk_size 250、chunk_overlap 0，补齊画面被截断处）、InMemoryVectorStore 加 OpenAIEmbeddings、retriever k=6；生成函数用 traceable 装饰、把检索文档拼进提示（口述口径：三句以内、保持简洁）。运行中当场撞到 LangSmith 限流报错，讲者说明是请求额度问题后继续。随后建 RAG 测试集 3 条（仓库证实，问题围绕 ReAct 自我反思、few-shot 偏差等，与博客内容对应）。
- 49:53 RAG Evlauation Metrics：写四个评估器（结构见技巧 1、2），量规类经仓库证实为 TypedDict 的 explanation + 布尔字段，grader 用 ChatOpenAI 配 with_structured_output；correctness 用 gpt-4o-mini、groundedness 用 gpt-4o（画面代码可读处）。途中两个实操事故照录：pandas 未装需补装；correctness 量规出现 Invalid schema 报错，讲者说要把某个 description 置空修复（具体改动画面不可读，仓库现行版无从对照出是哪一处）。最终四评估器一起跑完，口述结果：relevance 1、correctness 1、groundedness 约 0.5、retrieval relevance 未明确报数，运行约 15.4 秒；结果表小字不可读，仓库亦无存档。

### 五、原样保留区

仓库原文摘录（1-rag_evaluation.ipynb，GPL-3.0，节选关键行）：

```python
eval_instructions = "You are an expert professor specialized in grading students' answers to questions."
# correctness: client.chat.completions.create(model="gpt-4o-mini", temperature=0, ...) → CORRECT / INCORRECT 映射布尔
def concision(outputs: dict, reference_outputs: dict) -> bool:
    return int(len(outputs["response"]) < 2 * len(reference_outputs["answer"]))
def ls_target(inputs: str) -> dict:
    return {"response": my_app(inputs["question"], model="gpt-4-turbo")}
text_splitter = RecursiveCharacterTextSplitter.from_tiktoken_encoder(chunk_size=250, chunk_overlap=0)
retriever = vectorstore.as_retriever(k=6)
```

量规类的字段顺序（仓库原文结构）：CorrectnessGrade / RelevanceGrade / GroundedGrade 均为 TypedDict，第一字段 explanation（注释要求写明打分理由）、第二字段为布尔（correct / relevant / grounded）；仓库注释原话大意：字段定义顺序即模型生成顺序，把解释放前面能迫使模型先想清再给结论。

画面输出原文（数据集创建回执）：返回 5 个 example_ids 与 count: 5，与仓库的 5 条样例一致。

### 六、术语表

- LLM-as-a-judge：以模型按量规充当评审，本课聊天侧用自由文本判定、RAG 侧升级为结构化输出判定。
- groundedness：回答是否完全落在检索文档的事实内、无文档外编造。
- retrieval relevance：检索到的文档与问题是否相关，与回答质量无关、只查检索段。
- ground truth：数据集里的参考答案，correctness 类指标的判定基准。
- TypedDict 量规类：用类型化字段约束评审输出结构的做法，本课四评估器共用同一模式。

### 七、易错点和存疑点

- 时长三方冲突已裁定：播放器实测 1:06:11 为准；讲者口中的「一小时」是约数；简介时间戳里有一条 01:50:03 的条目，比全片还长 43 分 52 秒且无对应章节，是残留错误，不要拿它当章节或片长。简介共列 6 条、播放器只有 5 章，多出正是这一条。
- 第二轮聊天实验的分数未决：1 与 0.6 两个口径都在视频里出现，画面与仓库都定不了案，引用时必须说明是讲者自我更正中的数字。
- 画面里三处课本编号互相打架（分别显示共 16 格中的第 6/15 格、共 28 格中的第 27 格），说明视频由多段分开录制的 notebook 拼成；引用代码以仓库现行版为准、画面为辅。
- 「一句短句」的目标指令与「2 倍长度」的 concision 指标是两个不同阈值，讲者未作区分，照录并指出。
- 页面带付费推广标记，但口播内容里没有辨认出推广段落；简介大半是讲者课程班的招生信息，与内容无关，不入笔记。

---

## 45. Langfuse Walkthrough - Full Demo of Agent Tracing, Evals, Experiments and Prompt Management

### 一、元信息

- 标题：Langfuse Walkthrough - Full Demo of Agent Tracing, Evals, Experiments and Prompt Management（页面原样）
- 链接：https://www.youtube.com/watch?v=THfB4p2xCFY
- 频道：Langfuse（官方）；演示者 Marc Klingen（联合创始人）
- 时长：14:56（播放器实测）
- 发布时间：2026-09-18（示例 trace 的日期是 2026-09-14，为预录演示）
- 观看量：10,441 次；点赞 104
- 配套代码：无视频专属仓库；产品仓库为 langfuse/langfuse（36,818 stars，2026-11-01 实查，当日仍有推送）
- 核验档：C 档（官方产品走查型；多处界面表格不可读，内容以口述与可读幻灯片为准；视频未给出产品版本号，本笔记不推断）

### 二、一句话结论与摘要

这条官方走查把 Langfuse 的主张讲成一个闭环：生产 trace 先被记录，再由评估器与人工标注把失败样本筛出来，沉淀成数据集，最后在 CI 里做实验对比、过关了才发布——追踪、评估、数据集、实验、提示管理不是五个功能，而是一条流水线的五段。

- 追踪层：OpenTelemetry 原生接入，Python/TypeScript SDK 加上百个集成（官方口径）；trace 视图按检索、工具、模型调用拆解 token、成本与延迟，并提供看流量形态的 Pulse 视图；口述称底层跑在 ClickHouse 上。
- 评估层：LLM 评审、代码评估器、人工标注队列三种手段并行，错误分析把失败模式聚类（画面可读数字：三类失败模式计数 24 / 17 / 9）。
- 时效性（截至 2026-11-01）：视频没有标任何版本号；langfuse 的 Python SDK 在 PyPI 已是 5.1.0（实查），仓库保持高频更新。幻灯片上的生态数字（见四）是官方自报，本组未逐项外查，引用须带「官方自报」限定。

### 三、技巧总结

- 技巧 1：把「有趣的 trace」做成筛选问题，而不是浏览问题。生产 trace 量大，靠人翻必漏；走查的顺序是先让评估器与监控器把异常样本顶出来，人只看被筛中的，并顺手标注。我的思考：这与第 43 条的在线评审是同一思想的平台版——先机器筛、后人工判，人的时间只花在信号密度最高的地方。
- 技巧 2：失败样本要回流成数据集，而不是看完就算。被标注确认的失败直接沉淀为 golden dataset，下一次改模型、改提示、改工具时就用它做实验对照。我的思考：数据集从生产里长出来，分布才真实；手写样例集（对照第 44 条的 5 条与 3 条）适合冷启动，但天花板就是标注回流这一环有没有接上。
- 技巧 3：实验进 CI，做发布门。走查把实验对比放在 CI/CD 里当放行条件：基线 0.71 对新模型 0.86、加 0.15（画面数字）这类对比不过线就不发。我的思考：评估只有卡住发布才算数；跑完只生成一份报告的评估，最后都会沦为装饰。
- 技巧 4：提示当成可部署资产管理。版本化的提示用发布标签切换、不必重新部署服务，且本地缓存被官方称作不增加延迟与可用性风险，trace 还能反查当时用的提示版本。我的思考：提示版本与 trace 互链是排障刚需——线上某条坏输出，先要能回答「它当时用的是哪一版提示」，否则复盘无从下手。
- 技巧 5：工具自身也要对 agent 开放。官方给同一套 API 配了 skill、CLI 与 MCP server，文档另有 llms.txt 形态。我的思考：这呼应第 43 条的做法——平台把操作知识做成 agent 可消费的形态后，「让编程 agent 帮我查上周的坏 trace」才成为现实工作流，而不只是口号。

### 四、详细笔记（按章节）

- 0:00 开场（无章节名）：立题——做 demo 容易，把 agent 可靠地放进生产难；全片按「追踪—评估—数据集—实验—提示」的工程闭环展开。
- 1:57 Tracing an Agent：演示一条 agent trace 的拆解（检索、工具、模型调用各段的 token、成本、延迟；一条示例 trace 总延迟画面可读为 15.79 秒）；Pulse 视图看流量形态；接入面强调 OpenTelemetry 原生与多框架集成。
- 4:53 Evaluating an Agent：三种评估手段（LLM 评审、代码评估器、人工标注队列）与错误分析；画面可读的失败模式聚类计数 24 / 17 / 9。
- 7:11 Dashboards and Alerts：仪表盘与告警段；本章起始画面为空白占位帧（见存疑点），内容以口述为准。
- 7:56 Evaluation Datasets：把标注过的 trace 沉淀为黄金数据集，画面可读规模数字 42 条。
- 8:59 Experiments in CI/CD：在 CI 里跑实验对比模型/提示/工具改动，画面对比：基线 0.71、新模型 0.86、差 +0.15，作为发布门使用。
- 10:05 Prompt Management：提示版本化、发布标签免部署切换、本地缓存与 trace 反查提示版本。
- 11:14 AI Engineering Loop：把前面各段收成闭环图：生产 trace → 评估筛查 → 数据集 → 实验 → 发布 → 回到生产。
- 12:37 Self-hosting：Docker Compose / Helm / Terraform 三种自托管形态，与云版同一代码库；画面生态数字（官方自报）：Docker 拉取 16M、PyPI 月下载 27M、NPM 月下载 8M、月提交 636。
- 13:15 Langfuse Cloud：云版收尾与上手入口；口述称其为本领域最大的开源社区（无对比数据，见存疑点）。

### 五、原样保留区

画面可读数字（原样）：失败模式计数 24 / 17 / 9；数据集 42 条；实验对比 0.71 → 0.86（+0.15）；示例 trace 延迟 15.79s（日期 2026-09-14）；自报生态数字 16M Docker pulls、27M PyPI/月、8M NPM/月、636 commits/月。

案头核查（2026-11-01，非视频内容）：langfuse/langfuse 仓库 36,818 stars 且当日仍有提交，活跃度与官方口径相符；视频未给版本号，演示界面的版本字符串不可读，本笔记不作任何版本断言。

### 六、术语表

- trace：一次 agent 运行的完整调用记录，按步骤拆解输入输出、token、成本与延迟。
- Pulse 视图：Langfuse 看整体流量形态的追踪总览（官方命名）。
- 标注队列（annotation queue）：把机器筛出的样本排队交人工判定的评估环节。
- 发布标签（deployment label）：给提示版本打的上线标记，切换标签即切换线上提示，不必重新部署。
- golden dataset：由确认过的真实样本组成的基准数据集，用于实验对照与回归。

### 七、易错点和存疑点

- 「本领域最大开源社区」是讲者断言，画面没有给任何对比数据，不要转述为事实。
- 本章起始的仪表盘画面是空白占位帧，评估器、数据集与提示管理的多处界面表格也不可读——凡涉界面细节处，本笔记只依口述与可读幻灯片，未补造任何界面数值。
- 标题列的四件事（追踪、评估、实验、提示管理）比实际内容窄，正文还包括仪表盘、数据集、工程闭环与自托管/云版两章，按目录读全片更准。
- 示例 trace 日期（2026-09-14）早于发布日 4 天，系预录演示；画面里的延迟与成本都是该次演示的数据，不是性能承诺。

# YouTube Agent 技巧视频笔记 · 第 31–35 条

> 核验型笔记第三批之第一组（框架横评）。取材标准：字幕、时间戳、代码原文三样齐全；播放器逐章核验，配套仓库克隆对照，能跑的代码实际跑过；看不清处标「不可读」，推断与原文分开标注。

## 目录

- [31. Master OpenAI's Agents SDK in Just 25 Minutes – Build Powerful Agentic AI Apps with Streamlit](#31-master-openais-agents-sdk-in-just-25-minutes--build-powerful-agentic-ai-apps-with-streamlit)
- [32. SmolAgents: A Smol Library to Build Agents](#32-smolagents-a-smol-library-to-build-agents)
- [33. Ep6 Build an AI Engineering Team with CrewAI | Multi-Agent System in Python](#33-ep6-build-an-ai-engineering-team-with-crewai--multi-agent-system-in-python)
- [34. AutoGen Tutorial For Beginners 2026 | Microsoft AutoGen Tutorial | AutoGen Studio | simplilearn](#34-autogen-tutorial-for-beginners-2026--microsoft-autogen-tutorial--autogen-studio--simplilearn)
- [35. Master Google's Agents Development Kit (ADK) in 60 mins– Build Production Agentic Workflows today!](#35-master-googles-agents-development-kit-adk-in-60-mins-build-production-agentic-workflows-today)

---

## 31. Master OpenAI's Agents SDK in Just 25 Minutes – Build Powerful Agentic AI Apps with Streamlit

### 一、元信息

- 标题：Master OpenAI's Agents SDK in Just 25 Minutes – Build Powerful Agentic AI Apps with Streamlit（页面原样）
- 链接：https://www.youtube.com/watch?v=XP7nOVWI_HU
- 频道：The AI Lifestyle（1,800 订阅）
- 时长：26:59（播放器实测；标题写 25 分钟、简介写 30 分钟，三者互不相同）
- 发布时间：2025-03-17（Agents SDK 发布当月）
- 观看量：2,272 次；点赞 57
- 配套仓库：https://github.com/theailifestyle/oai-agents-sdk （简介链接在页面上被截断显示，经 GitHub API 核到全名；已克隆对照，6 个 .py 文件全部通过字节编译；仓库里确有结尾承诺的两份 cheat sheet）
- 核验档：A 档（播放器逐章扫 + 仓库克隆对照）
- 章节（简介原样）：0:00 Video Introduction；1:24 Responses API introduction；4:19 What is Agents SDK；5:47 Level 1: Core Concepts (Agents & Runners)；12:49 Level 2: Guardrails & Safety；19:00 Level 3: Advanced Orchestration (LLM & Code based coordination)；24:30 Conclusion & Next Steps

### 二、一句话结论与摘要

这是 Agents SDK 发布当月的三级进阶教程：Level 1 讲 Agent 与 Runner，Level 2 给同一个例子加护栏，Level 3 用两种编排方式各写一遍 Deep Research 克隆。

- 框架地图是「五原语」：models、tools、knowledge and memory、guardrails、orchestration——讲者称这是任何 agent 框架最重要的五部分。
- 最有复用价值的两个设计：handoff 靠每个 agent 的 handoff_description 做路由说明；guardrail 本身是一个带结构化输出的 agent，触发 tripwire 后在 handoff 之前就把流程掐停。
- Level 3 同一个应用写了两遍——一遍代码编排、一遍 Runner 编排，讲者的结论是好应用永远是两种编排的混合。
- 时效性（截至 2026-11-01）：openai-agents 现行版本 0.24.0（PyPI 核于 2026-11-01），视频是 0.0.x 时代的形态，API 细节引用前须对照现行文档。视频里说 Assistants API「将在年内弃用」——实际时间线是 2025-08-26 发出弃用通知、2026-08-26 已正式关停（多家迁移指南转述 OpenAI 官方弃用日志，口径一致），现在只能用 Responses + Conversations。视频说 Responses API「不是要取代 Chat Completions」，现行官方口径已改为 Responses 是 Chat Completions 的演进、新项目一律推荐——这两处都按视频时点理解，不要当现状引用。

### 三、技巧总结

- 技巧 1：用五原语给任何框架做体检。models / tools / knowledge and memory / guardrails / orchestration 这张清单可以直接拿去评别的框架：缺哪一块、哪一块是外挂、哪一块是原生。我的思考：清单里把 memory 并进「knowledge and memory」一笔带过，是 2025 年初的典型口径；拿它评 2026 年的框架时，记忆这一格要单独拆出来看，否则会漏掉主要差异。
- 技巧 2：handoff_description 是写给路由者的接口文档。Triage Agent 不读代码、只读每个专家的 handoff_description 来决定把问题交给谁；Level 1 应用里 General Information Assistant 的 instructions 甚至明写「我不处理天气和旅行问题，那些归 Travel Advisor」——排除条款和自我介绍同样重要。我的思考：这与第 29 条 LangGraph supervisor 的路由是同一件事的两种实现：一边是图结构写死，一边是描述文本驱动。描述驱动省代码，但路由质量变成了文案质量。
- 技巧 3：guardrail = 一个带结构化输出的小 agent + tripwire。作业护栏的写法：guardrail_agent 的 output_type 是 Pydantic 模型 HomeworkOutput（is_homework: bool + reasoning: str），判定不是作业就触发 tripwire，流程在 handoff 之前停下，根本走不到专家 agent。讲者给的动机也很实在：handoff 一跑起来 token 和调用次数就开始烧钱，护栏首先是成本闸，其次才是安全闸。我的思考：把「判定」做成结构化输出而不是让模型吐一段话，是这一课最值得抄的一行——判定结果要能被代码直接消费，附带 reasoning 又留了可审计的理由。
- 技巧 4：同一个应用，代码编排与 LLM 编排各写一遍。Level 3 的 Deep Research 克隆先用纯代码串 Planner → Search → Writer（讲者明说这一遍「actually not including this in a runner」），再用 Runner 重演一遍，最后落在「不要总是依赖 LLM，也不要总是依赖代码」。我的思考：这条对自研 harness 直接可用——控制流的可预测性来自代码，分支的灵活性来自 LLM，把哪一段交给谁，应该逐段决定而不是整应用二选一。
- 技巧 5：Traces 是内建的第三件套。Level 1 就把 trace 日志列为与 Agent、Runner 并列的组件：triage → handoff → 专家的全过程带每步耗时可查，护栏触发也记在 trace 里。我的思考：对照第 21 条 harness 的讨论——可观测性不是上线后加的监控，而是循环本身的一部分；选框架时先看 trace 的粒度，再看功能表。

### 四、详细笔记（按章节）

- 1:24 Responses API：定位是 OpenAI 新的核心 API、agentic 原语，合并 Chat Completions 的简单与 Assistants 的功能集；内置工具 Web search、File search、Computer use。屏幕对照表（画面原样）：Audio 在 Responses 侧是 Coming soon，Code interpreter 也是 Coming soon，File search / Computer use 在 Chat Completions 侧是受限标记。机制口述：API 内部替你多打几轮调用再返回最终结果，省去在 Chat Completions 上自己拼循环的往返延迟。历史衔接：Assistants 的 threads 在 Responses 里暂缺，用 previous response ID 链接维持会话。
- 4:19 五原语：见技巧 1。agent 的定义：能自己推理、规划、行动的 LLM；OpenAI 路线是代码导向的原语集，与 n8n 类框架相对。
- 5:47 Level 1：Agent 的构成 = name、instructions、tools、handoff_description；Runner 是 agent 循环，负责接收问题并判断输出何时完成。基础例：Math Tutor、History Tutor、Triage Agent；提问「Who was the first president of the united states?」被路由到 History Tutor，输出同时报告 last agent 是 History Tutor、答案 George Washington；换数学题则 last agent 变 Math Tutor。应用版专家有四个：Math Tutor、General Information Assistant、Creative agent、Travel Advisor，各带函数工具或 Web Search；天气问题进 Travel Advisor（带天气工具），「plan a 2-day trip starting tomorrow」同样走 Travel Advisor。
- 12:49 Level 2：在 Level 1 同一个程序上加 input / output 两类 guardrail；作业护栏见技巧 3。反例演示：问「what is life」→ 判定非作业 → tripwire 触发 → 停在 handoff 之前，trace 里能看到触发点；问总统问题则通过检查、正常 handoff。讲者明说 Level 2 应用与 Level 1 的代码差异只有多出的 input/output guardrails。
- 19:00 Level 3：应用先校验 API key；搜索次数可限制，本次演示限 3 次。Planner 生成 3–4 条搜索词（演示问题「the effect of AI on Healthcare」，词条如「AI applications in healthcare」）；Writer 产出摘要 + 完整报告（带链接、用过的搜索词、后续问题）；摘要字数上限可在代码里改。两种编排的对照见技巧 4。
- 24:30 结尾：仓库里有两份 cheat sheet（一份全命令、一份一页纸版）——已在仓库中核实存在。讲者预告领域专用模型会越来越多（医疗例），SDK 允许给领域配专家 agent。

### 五、原样保留区

Level 2 护栏核心代码（屏幕原文，文件 basicguardrail.py；仓库同名文件已克隆、字节编译通过）：

```python
from agents import Agent, InputGuardrail, GuardrailFunctionOutput, Runner
from pydantic import BaseModel
import asyncio

class HomeworkOutput(BaseModel):
    is_homework: bool
    reasoning: str

guardrail_agent = Agent(
    name="Guardrail check",
    instructions="Check if the user is asking about homework.",
    output_type=HomeworkOutput,
)
```

（homework_guardrail 函数体第 29 行起被字幕条遮挡，屏幕不可读；仓库版以仓库为准，不在此处拼凑。）

Level 1 基础例的 triage 定义（屏幕原文）：

```python
triage_agent = Agent(
    name="Triage Agent",
    instructions="You determine which agent to use based on the user's homework question",
    handoffs=[history_tutor_agent, math_tutor_agent],
)
```

Level 3 的 writer 模型选项（仓库层原文，level3codecoordination.py 与 level3llmcoordination.py 相同）：Streamlit 单选 `["gpt-4o", "gpt-3.5-turbo"]`；planner 与 search agent 在仓库里固定 `model="gpt-4o"`。

### 六、术语表

- handoff：把当前对话的控制权移交给另一个 agent 的机制；移交目标由 triage 类 agent 依据各 agent 的 handoff_description 选择。
- tripwire：guardrail 判定不通过时抛出的中断信号，流程立即停止。
- Runner：SDK 的 agent 循环驱动器，负责多轮调用工具与 handoff，并判断最终输出何时产生。
- Traces：SDK 内建的服务端追踪，记录一次运行里每个 agent、工具调用与护栏检查的时序。

### 七、易错点和存疑点

- **时长三方打架**：标题 25 分钟、实测 26:59、简介 30 分钟，引用时以实测为准。
- **writer 模型口述不可直接引用**：21:13–21:29 讲者口述的模型名在自动字幕里是乱码（"GPT 40 and 3.5 / 4.5 or3"），屏幕代码只有 writer_model 参数；已从仓库核实可选项是 gpt-4o 与 gpt-3.5-turbo，前半句乱码与此吻合，后半句「4.5 or3」仍无对应物，不写进结论。
- **Responses API 的定位是视频时点口径**：见第二节时效性；尤其「Assistants 年内弃用」一句，实际关停日为 2026-08-26，已经发生。
- **中段取证方式说明**：12:47–22:43 一段播放器扫片时未实时停留，内容由字幕面板全文 + 五处定点回看截图（2:14、11:44、12:01、14:42、23:15）恢复，与其他章节的实时逐章读略有差别，特此标明。
- 简介的「I'll share」清单把 Responses API 与 What is Agents SDK 两章略去了，与实际 7 章目录对不上；以章节目录为准。
- Level 2 应用自身的护栏代码（约 18:20 处滚动展示）没有可读的定格画面，不可读；基础例的护栏代码不受影响。

---

## 32. SmolAgents: A Smol Library to Build Agents

### 一、元信息

- 标题：SmolAgents: A Smol Library to Build Agents（页面原样；注意简介与章节标题里作者自己写作「SmallAgents」，标题与包名是 SmolAgents）
- 链接：https://www.youtube.com/watch?v=icRKf_Mvmt8
- 频道：Prompt Engineering（25.2 万订阅）
- 时长：24:32（播放器实测）
- 发布时间：2025-01-02（smolagents 发布初期）
- 观看量：20,818 次；点赞 488
- 配套：视频中使用的 notebook 未在简介所链的作者仓库中找到（已克隆 PromtEngineer/YoutTube-Tutorial 核查，内无 smolagents notebook）；本条代码原文以播放器 1080p 定格读码为准
- 核验档：A 档（播放器逐章扫 + 代码帧 1080p 复读 + 版本与更名外部核查；仓库缺失已如实标注）
- 章节（简介原样）：00:00 SmallAgents；00:31 Key Features of SmallAgents；01:59 Getting Started with SmallAgents；09:14 Building an Agentic RAG System；13:26 Creating Custom Tools for SmallAgents；23:03 Conclusion and Final Thoughts

### 二、一句话结论与摘要

smolagents 的代表作讲解：一个「用代码做动作」的极简框架，顺带把 agentic RAG 的查询改写技巧和工具描述的写法一起教了。

- CodeAgent 不发 JSON 工具调用，而是直接写 Python 代码执行——简介口径称这能把 API 调用减少多达 30%（厂商口径，未独立核验）。
- 全片最值钱的一条检索技巧：把用户问题从疑问句改写成陈述句再检索，因为目标文档本身是陈述句；这条甚至被写进了工具的输入说明里，让 agent 自己照做。
- 一个必须记住的反例：RetrieverTool 的 description 写着「semantic search」，实现却是 BM25 关键词检索——工具描述是写给 agent 看的合同，而合同可以与实现不一致（与第 22 条 edit_file 的例子同型）。
- 时效性（截至 2026-11-01）：smolagents 现行版本 1.26.0（PyPI 核于 2026-11-01），视频里结尾画面还是 v0.1.3 的文档。视频代码里的 HfApiModel 在现行版本已更名为 InferenceClientModel（现行 PyPI 示例与官方文档均用新名），照抄视频代码前先换名；默认模型也已换代。方法论部分（代码即动作、查询改写、工具描述写法）不受版本影响。

### 三、技巧总结

- 技巧 1：代码即动作。用一段可执行 Python 表达「搜 A、搜 B、拿两个结果算一下」，比三次独立的 JSON 工具调用少了往返与拼装；代价是必须有沙箱——视频里 agent 想直接调 subprocess 下载东西，被沙箱挡下，这正是沙箱存在的理由。我的思考：这一派与 JSON 工具调用派的分歧不在语法，在信任模型：代码派把「组合逻辑」交给模型一次写完，工具派把每一步都过一遍框架的关。新手看演示觉得代码派爽，真正要审计时，工具派的每一步都有记录。
- 技巧 2：检索前把疑问句改成陈述句。原理写在画面原文里：语义相似度拿用户问题当参照，而问题是疑问句、藏着答案的文档是陈述句，语气不一致会把真正相关的文档分数压低。agentic RAG 的增量就在这里：检索不好可以改写查询、反复检索，而不是一次定死。我的思考：这条对任何 RAG 都成立，不限于 agent 形态；它本质上是「让查询长得像文档」。
- 技巧 3：工具的 inputs 说明要写「怎么用」，不只是「是什么」。RetrieverTool 的 query 参数说明直接写了操作指令：查询应与目标文档语义接近、用陈述句不要用疑问句。agent 读的是这段文字，技巧 2 因此能被 agent 自行执行。我的思考：工具描述是提示工程的一部分，而且是复用率最高的那部分——它会在每次调用时被读到。
- 技巧 4：max_iterations + verbose 是 agent 的最低安全配置。演示里 agent 会选错工具（给了检索工具不用、跑去网页搜索）、会尝试下载安装、会绕圈；讲者的处理是步数上限 4 步 + 全程打印中间步骤，并明说这些日志「critical」、可以当反馈用。我的思考：上限和日志不是调试选项，是让概率系统可被信任的前提；没有这两样，演示跑通也只是运气。
- 技巧 5：框架作者自己的立场——能不用 agent 就不用。视频花了不短的篇幅讲：smolagents 的创造者非常坦白「you often don't need agents」，推荐 Anthropic 的《Building Effective Agents》，工作流与状态机能覆盖大多数任务。我的思考：这条与第 18 条 Anthropic 本家的克制互为印证。卖框架的人都在劝退，说明选型的默认答案应该是工作流，agent 是例外而不是默认。

### 四、详细笔记（按章节）

- 00:31 关键特性：代码 agent 的完整闭环是「生成代码、执行、出错、改写再执行」；「small」指包小（口述约一千行代码、抽象最少），不是指小模型；开源权重与闭源 API 都支持。执行代码必须配安全解释器，结尾画面文档侧栏有「Secure your code execution with E2B」。
- 01:59 上手：安装 smolagents 与 huggingface_hub，用 HF token 登录拿免费推理。第一个 agent 是 CodeAgent + DuckDuckGoSearchTool + HfApiModel，默认模型 Qwen/Qwen2.5-Coder-32B-Instruct（画面模型行原样）；换模型靠 model_id 变量（画面里 Llama-3.3-70B-Instruct 一行是注释状态）。三个演示：①豹子穿桥问题——agent 自己拆成查桥长（画面结果：155 m）与查豹速再写代码算时间（最终秒数画面未清楚捕获，不可读）；②DeepSeek-V3 与 V2 参数量对比——agent 给出「约 +184%」，但讲者事后明说它取到的 V2 数字是错的，这个 184% 不能当真；③加问 Llama 3.3 时 agent 把字符串结果当列表下标用、报错，自己改写代码后恢复——讲者的点评是大一点的模型（如 GPT-4o、Claude Sonnet）大概能避开这个错。
- 09:14 agentic RAG：普通 RAG 只检索一次，检索坏了生成跟着坏；agentic 版可改写查询反复检索，代价是调用次数涨得快。数据集是 HF 文档中 Transformers 部分（数据集 m-ric/huggingface_doc，画面加载进度 2647 条）。切分用 RecursiveCharacterTextSplitter、chunk_size=500——讲者当场指出这是「按字符不是按 token」的常见错误，他照抄 HF 例子所以自己也犯了：块太小、句子被切断、上下文丢失。
- 13:26 自定义工具：RetrieverTool 见技巧 2、3 与保留区（BM25、k=10）。直测查询「How to download a model from Huggingface?」返回带编号的文档（含 hf_hub_download 一条）。组装 RAG agent：CodeAgent(tools=[retriever_tool], model=HfApiModel(), max_iterations=4, verbose=True)。三次运行：①下载问题第一次检索到无关块（讲 widgets 的），agent 把查询改写成陈述句「downloading a model from Hugging Face」后选中 Document 4（讲加载 checkpoint 的），讲者评「not the best answer but still pretty close」；②「前向与反向哪个慢」——结论反向更慢（Document 3），又检索一次确认，三步收尾；③检索工具与网页搜索并存时 agent 选了搜索（讲者提醒：可能需要明示用哪个工具），找到正确函数后又试图真的下载安装，撞上 subprocess 禁用，换 snapshot_download 也碰壁。
- 23:03 结尾：文档侧栏可见「Building good agents」「Tools - in-depth guide」，版本标记 v0.1.3；另演示了用 Flux 模型的图片生成工具例子（海滩图；该工具的 notebook 代码画面不可读）。收尾再劝一次：不用 agent 能省大量调试时间。

### 五、原样保留区

第一个 agent（画面原文，1080p 复读）：

```python
from smolagents import CodeAgent, DuckDuckGoSearchTool, HfApiModel

# model_id = "meta-llama/Llama-3.3-70B-Instruct"
agent = CodeAgent(tools=[DuckDuckGoSearchTool()], model=HfApiModel())#(model_id=model_id)
agent.run("How many seconds would it take for a leopard at full speed to run through Pont des Arts?")
```

RetrieverTool（画面原文，13:53 定格全可读；注意 description 与实现的出入）：

```python
from smolagents import Tool

class RetrieverTool(Tool):
    name = "retriever"
    description = "Uses semantic search to retrieve the parts of transformers documentation that could be most relevant to answer your query."
    inputs = {
        "query": {
            "type": "string",
            "description": "The query to perform. This should be semantically close to your target documents. Use the affirmative form rather than a question."
        }
    }
    output_type = "string"

    def __init__(self, docs, **kwargs):
        super().__init__(**kwargs)
        self.retriever = BM25Retriever.from_documents(
            docs, k=10
        )

    def forward(self, query: str) -> str:
        assert isinstance(query, str), "Your search query must be a string"
        docs = self.retriever.invoke(query)
        return "\nRetrieved documents:\n" + "\n".join(
            [f"\n\n===== Document {str(i)} =====\n" + doc.page_content
             for i, doc in enumerate(docs)]
        )
```

RAG 数据准备（画面原文）：数据集过滤与切分参数 chunk_size=500、chunk_overlap=50、separators=["\n\n", "\n", ".", " ", ""]。

画面原文（agentic RAG 的理由）：

```text
It performs only one retrieval step: if the results are bad, the generation in turn will be bad.
```

### 六、术语表

- CodeAgent：smolagents 中以生成并执行 Python 代码作为动作的 agent 类型，与之相对的是发结构化工具调用的类型。
- HfApiModel：视频时点的 HF 推理模型类名；现行版本已更名为 InferenceClientModel。
- agentic RAG：把检索放进 agent 循环的 RAG——查询可改写、检索可重复、结果可评判后再生成。
- BM25：基于词频的关键词检索算法；本条里是 RetrieverTool 的真实实现，与其「semantic search」的描述不符。

### 七、易错点和存疑点

- **工具描述与实现不符**：RetrieverTool 自称 semantic search、实为 BM25。agent 只能看见描述；引用这条时要分清「描述层」与「实现层」，与第 22 条的 edit_file 是同一类问题。
- **DeepSeek 的 184% 不可引用**：讲者事后亲口说 agent 取到的 V2 参数量是错的，这个百分比是错误输入的产物。
- **豹速数字四方打架**：画面搜索片段给出 37 mph（60 km/h）、36 mph、50 mph、44 mph 四个豹子极速，视频没有收敛到一个数，最终算出的穿桥秒数也没有清楚捕获——不可读，不补猜。
- **「三行代码」是口径**：画面 notebook 里在那三行之外还有安装、登录、导入与注释掉的 model_id 行；「三行」指 agent 组装部分。
- **notebook 缺失**：简介所链作者仓库里没有这条的 notebook（已克隆核查），代码以画面读码为准；若日后在别处找到同名 notebook，须重新对照。
- 章节边界与内容错位：RAG 的实作演示实际发生在「Creating Custom Tools」一章内，「Building an Agentic RAG System」一章主要是概念与数据准备，按章节名找演示会扑空。
- 开场点名的第三个框架（与 CrewAI、AutoGen 并列）在字幕里乱码、画面无文字，不可读，不猜。
- 名称拼写：标题与包名 SmolAgents，简介与章节标题 SmallAgents；作者仓库名拼作 PromtEngineer（少一个 p），均为原样如此，引用时照原样。

---

## 33. Ep6 Build an AI Engineering Team with CrewAI | Multi-Agent System in Python

### 一、元信息

- 标题：Ep6 Build an AI Engineering Team with CrewAI | Multi-Agent System in Python（页面原样）
- 链接：https://www.youtube.com/watch?v=H0Xnk2KYeDM
- 频道：PlayOwnAi（170 订阅；小频道，本条以仓库实物为准绳）
- 时长：12:29（播放器实测；简介头写「12-Min Video」）
- 发布时间：2026-01-17（页面日期）
- 观看量：124 次；点赞 5
- 配套仓库：https://github.com/matinict/MyCrewAi/tree/main/engineering_team （简介与置顶评论一致；已克隆全仓对照，40 个 .py 文件全部通过字节编译；仓库里还留着一次真实运行的全部产物）
- 核验档：A 档（播放器逐章扫 + 仓库克隆对照 + 生成产物实跑）
- 章节（简介原样）：00:00 Intro；00:30 What We're Building；01:15 Engineering Team Roles Explained；02:10 Creating a CrewAI Project；03:20 Project Structure Overview；04:10 Environment Variables Setup；05:10 Agents Configuration (agents.yaml)；06:30 Task Pipeline (tasks.yaml)；07:50 EngineeringTeam Crew Class；09:00 Running the AI Engineering Pipeline；10:20 Output Review；11:20 Final Thoughts & Use Cases

### 二、一句话结论与摘要

用 CrewAI 的 YAML 配置式写法组一支四人「AI 工程团队」（设计、后端、前端、测试）跑一条顺序流水线——视频本身讲得粗，但配套仓库意外地完整，正好用来核对视频没讲清的每一处。

- 框架形态：agents.yaml 定角色（role / goal / backstory / llm），tasks.yaml 定任务（description / expected_output / agent / context / output_file），crew.py 用装饰器把两者接起来，Process.sequential 顺序执行。
- 视频最大的问题是章节与内容系统性错位一格：标题说讲 agents.yaml 时屏幕上是 tasks.yaml，说讲角色时在讲 .env——按章节名学习会被带偏（详见存疑点）。
- 章节标题承诺的「LLM selection」画面里始终没出现，但仓库 agents.yaml 里真的逐个配了：负责人 gpt-4o、后端 gpt-4o-mini、前端 deepseek-chat、测试 claude-opus-4-1-20250805——按角色分模型是这条最值得抄的做法，可惜要翻仓库才看得到。
- **实跑结论**：仓库里提交的那次运行产物中，QA agent 生成的测试文件跑不起来——test_accounts.py 要从 accounts 导入 AccountManager，而后端 agent 生成的 accounts.py 里根本没有这个类，unittest 直接 ImportError，零条测试执行。简介宣称的「fully automated……maintaining structure and quality」，在它自己留下的产物上没有兑现。
- 时效性（截至 2026-11-01）：crewai 现行版本 1.15.33（PyPI 核于 2026-11-01）；仓库里写死的模型 ID（claude-opus-4-1-20250805 等）是 2026 年 1 月的时点选择，引用时注意代际。

### 三、技巧总结

- 技巧 1：配置式组队——角色与任务分开写在 YAML 里。agents.yaml 只管「谁」（角色、目标、背景故事、用哪个模型），tasks.yaml 只管「做什么、交什么」，crew.py 只管接线。改流水线不动代码，改提示词不动结构。我的思考：这与第 29 条 LangGraph 用图写编排是两种哲学：YAML 式赢在非程序员也能改，输在复杂分支表达不出来；CrewAI 的适用面就是这种线性的、角色分明的流水线。
- 技巧 2：expected_output 是写给下游机器的输出合同。code_task 的 expected_output 明写「ONLY the raw Python code without any markdown formatting, code block delimiters, or backticks……directly saved to a file and executed」——因为下一步是把输出原样存成 .py 文件跑，合同的读者不是人，是文件系统。我的思考：凡是 agent 输出要被程序消费的场合，输出合同都该这样写死格式；留一分模糊，下游就多一分解析失败。
- 技巧 3：context 链 = 流水线的状态传递。code_task 的 context 是 design_task，frontend 与 test 的 context 是 code_task，产物经 output_file 落盘（设计稿 output/{module_name}_design.md、后端 output/{module_name}、前端 output/app.py、测试 output/test_{module_name}）。仓库核实：test_task 的 context 是 code_task（画面有一帧读成 test_task，是遮挡误读，以仓库为准）。
- 技巧 4：按角色分配模型档位（仓库层）。负责人用 gpt-4o（设计定全局），测试用 claude-opus-4-1-20250805（最贵的一档给了把关者），后端用 gpt-4o-mini（照图施工、便宜），前端用 deepseek-chat；后端一行还留着被注释掉的 claude-3-7-sonnet——换模型的试验痕迹也在。我的思考：这个分配逻辑比「全员最强模型」成熟：施工环节可替换性强、压成本，设计与验收环节决定上限、舍得花钱。可惜视频对此只字未提。
- 技巧 5：流水线的最后一环必须是真执行，不是再一个生成者。这条的测试 agent 只负责「写」测试，没有任何一环真的运行测试——结果测试文件连导入都过不了（见第二节实跑结论），而视频的 Output Review 章节也没有展示测试运行画面。我的思考：生成测试 ≠ 验证。把关环节若不带执行权，整条流水线的质量声明就是自我引用；这也是第 22 条 coding agent 里「工具要真跑」纪律的另一面。

### 四、详细笔记（按章节）

注意：本节按实际内容归位，章节标题与实际内容的错位在存疑点里逐条列明。

- 角色定义（实际在 02:10–03:20 讲）：Engineering Lead 把高层需求变成详细技术设计——模块结构、类名、全部函数与方法签名，一切装在一个自包含的 Python 模块里；Backend Engineer 照设计实现完整模块，目标是可测试、可加 UI 的干净代码；Frontend Engineer 用 Gradio 给后端做演示 UI，与后端同目录；QA/Test Engineer 给后端写单元测试，同目录存放。口述称每个角色由「专门的 LLM」驱动（具体分配只在仓库里，见技巧 4）。
- 任务链（实际在 03:20–05:10 讲）：design（产出 Markdown 设计稿）→ code（只许输出原始 Python 代码）→ frontend（极简 Gradio 原型，假设只有 1 个用户）→ test（单元测试），每一步吃上一步的上下文，顺序执行。口述两处强调：agent 的直接代码执行是关闭的（为安全与可复现），agent 只产文件，唯一被真正执行的动作是人手动 `uv run app.py`。
- 项目创建（实际在 00:30–01:15 讲）：终端里跑 crewai create 命令，交互选择依次选「1」「6」，其余提示一律 skip；项目名在简介命令里是 EngineeringTeam，落地文件夹是 engineering_team。
- 环境（实际在 01:15–02:10 讲）：项目根建 .env 放各家 API key；变量名的确切写法字幕乱码、画面无可读帧，不可读（仓库 .env 未入库，无从对照）。
- 示例需求（07:50–09:00 口述；仓库 main.py 里有全文）：交易模拟账户系统——开户、存款、取款、买卖股票、组合估值、盈亏、交易历史；硬约束三条：不能透支、不能买买不起的股、不能卖没持有的股；模块名 accounts.py、类名 Account（口述读作小写 account，仓库为大写 A，以仓库为准）。仓库 main.py 把需求全文、module_name、class_name 打包成 inputs 字典传给 kickoff。
- 安装（09:00–10:20）：画面终端原样 `uv add "crewai[google-genai]" "crewai[anthropic]" litellm`——一次装齐 CrewAI 的 Google GenAI 与 Anthropic 集成加 LiteLLM（口述称 LiteLLM 是跨厂商的统一接口层）。
- 运行与产物（10:20–11:20 口述）：`crewai run` 启动整条流水线，产物进 output 目录，再装 Gradio、`uv run app.py` 打开演示 UI。本次扫片没有捕获到可读的 Gradio 界面帧；但仓库里留有同需求的一次真实运行产物（accounts.py、app.py、test_accounts.py 与设计稿，在 @@output/ 目录下），其测试实跑结果见第二节。

### 五、原样保留区

tasks.yaml 的 code_task（画面原文，05:24 定格；与仓库逐字一致）：

```yaml
code_task:
  description: >
    Write a python module that implements the design described by the engineering lead, in order to achieve the requirements.
    Here are the requirements: {requirements}
  expected_output: >
    A python module that implements the design and achieves the requirements.
    IMPORTANT: Output ONLY the raw Python code without any markdown formatting, code block delimiters, or backticks.
    The output should be valid Python code that can be directly saved to a file and executed.
  agent: backend_engineer
  context:
    - design_task
  output_file: output/{module_name}
```

agents.yaml 的逐角色模型（仓库层原文，节录）：

```yaml
engineering_lead:
  llm: openai/gpt-4o
backend_engineer:
  #llm: anthropic/claude-3-7-sonnet-latest
  llm: openai/gpt-4o-mini
frontend_engineer:
  llm: deepseek/deepseek-chat
test_engineer:
  llm: anthropic/claude-opus-4-1-20250805
```

实跑记录（本机对仓库 @@output/ 产物执行）：

```text
$ python3 -m unittest test_accounts -v
ImportError: cannot import name 'AccountManager' from 'accounts'
FAILED (errors=1)
```

### 六、术语表

- crew：CrewAI 的基本单位，一组 agent 加一组 task 加一个执行过程（process）。
- Process.sequential：任务按声明顺序依次执行的过程类型（仓库 crew.py 原样）。
- context（task 的）：声明该任务要消费哪些前序任务的输出。
- output_file：任务产物落盘的路径模板，{module_name} 等占位符由 inputs 注入。

### 七、易错点和存疑点

- **章节与内容系统性错位**：00:30 标题 What We're Building，实际在建项目；01:15 标题 Roles Explained，实际在讲 .env；02:10 标题 Creating a Project，实际在讲角色；03:20 标题 Project Structure，实际在讲任务链；05:10 标题 Agents Configuration，屏幕上是 tasks.yaml；07:50 标题 Crew Class，屏幕上是通用脚手架 main.py 模板（inputs 还是 'topic': 'AI LLMs'，与口述的交易系统对不上）；10:20 标题 Output Review，前半段还在装依赖。按标题检索内容基本会错位一格。
- **简介宣称 vs 产物**：「zero-to-production」「fully automated」与口述「代码执行关闭、产物是待保存执行的文件」并存；且仓库产物实跑证明测试环节没有闭环（见第二节）。引用这条时以产物为准。
- 屏幕 main.py 是脚手架原样模板，不是口述的那个交易系统版本；两个版本不要混引（仓库 main.py 是交易版）。
- QA 角色名称三处不一：简介「QA Test Engineer」、片头字幕误听「Qo test engineer」、后段口述「test engineer」，指同一角色。
- test_task 的 context 有一帧被读成 [test_task]（字幕条遮挡），仓库原文是 [code_task]，以仓库为准。
- 录制机器时钟与发布日期对不上：编辑器画面时钟是「Wed Jan 14」傍晚，页面发布日期是 1 月 17 日；年份在画面里不可读，只记日期差，不做年份推断。
- agents.yaml 与 crew.py 的正文在播放器里不可读（360p 小字 + 字幕条遮挡），本条中这两份文件的内容全部来自仓库层，已分别标明来源层。
- .env 的变量名不可读：字幕乱码且无可读画面，仓库也未收录 .env，不猜。

---

## 34. AutoGen Tutorial For Beginners 2026 | Microsoft AutoGen Tutorial | AutoGen Studio | simplilearn

### 一、元信息

- 标题：AutoGen Tutorial For Beginners 2026 | Microsoft AutoGen Tutorial | AutoGen Studio | simplilearn（页面原样）
- 链接：https://www.youtube.com/watch?v=sCNXU0_IWzc
- 频道：Simplilearn（674 万订阅）
- 时长：26:48（播放器实测）
- 发布时间：2026-03-09（简介展开日期；折叠显示「7 个月前」）
- 观看量：3,886 次（观看期间页面一度显示 3,885，系页面计数波动）；点赞 42
- 配套：无。简介里只有 Simplilearn 自家课程的销售链接，没有代码仓库；第 5 章口述「会把代码放在简介里」并未兑现
- 核验档：B 档（播放器逐章扫 + 框架现状外部核查；无仓库可对照）
- 章节（简介原样）：00:00 Introduction to Autogen；04:11 Installation of Autogen；09:04 Creating First Agent；15:12 Multi-Agent Collaboration；18:26 Creating Multi-Agent；21:00 Deployment using VS Code；25:02 Use cases of Autogen

### 二、一句话结论与摘要

一支面向纯新手的 AutoGen 入门片：内容本身（本地 Ollama + 两个 agent 互审）没有大错，但标题与三处章节名都在卖视频里没有的东西，且它对框架现状只字未提——而这恰恰是 2026 年看这支视频最需要知道的事。

- 标题写「AutoGen Studio」，正片从头到尾没有出现 Studio 的界面、安装或用法，全部工作是 VS Code + Python 代码 + Ollama + FastAPI。
- 「Deployment using VS Code」一章做的是在本地把 FastAPI 应用跑起来用 Chrome 打开，没有任何外部署；「Use cases」一章不讲用例，讲的是每周去官方仓库看 releases / issues / PR。
- 代码部分可用的就三段：单 agent（AssistantAgent + Ollama 客户端 + 流式输出）、双 agent（writer + reviewer）、FastAPI 包装。双 agent 那段最关键的编排代码被幻灯片下缘截断，不可读——两个 agent 到底怎么接力，视频没有可核的原文。
- 时效性（截至 2026-11-01）：这是本条最重要的信息。AutoGen 已于 2025-10-02 起进入维护模式（仓库 README 横幅：不再加新功能、社区接管），微软最后的版本是 autogen-agentchat 0.7.5（2025-09-30；PyPI 核于 2026-11-01 仍为最新）；官方继任者是 Microsoft Agent Framework，1.0 已于 2026-04 正式发布。另有一个包名陷阱：现在 `pip install autogen` 装到的是原作者分叉的 AG2，不是微软的包。本视频发布于 2026-03-09——在维护模式开始之后——却对此只字未提，还在结尾教人「每周去仓库看新功能」；这条建议按它自己的前提已经过期。新项目不要照这支视频起步，它的价值是理解 AutoGen 的会话式多 agent 模型。

### 三、技巧总结

- 技巧 1：用「中间人」故事理解多 agent 的动机。第 1 章的例子最清楚：单 AI 写「求列表平均值」的代码，出错要人复制报错回去问、再跑——人是两个环节之间的搬运工；AutoGen 的做法是拆出一个只写代码的 agent 和一个只测代码的 agent，给一条指令后它们自己来回直到跑通。我的思考：这是全片最值得记的一段。多 agent 的收益首先来自「消灭人肉中间件」，其次才是分工的智能；评估一个多 agent 系统时，先数它替人搬了多少次运。
- 技巧 2：model client 是可替换层。代码里模型访问被收进 OllamaChatCompletionClient(model="llama3", host="http://localhost:11434")，agent 只认 model_client 参数；换云端模型就是换一个 client 对象，agent 定义不动。我的思考：这是 0.4 之后 AutoGen 的核心结构，也是它与第 33 条 CrewAI「每个 agent 在 YAML 里写 llm 字符串」的差别——一边是对象注入，一边是配置字符串，测试与替换的便利程度不同。
- 技巧 3：流式输出用 run_stream + Console。第一个 agent 不等完整答案，而是把 token 流实时打到控制台。我的思考：对长任务这是体感与可调试性的分水岭——流式让用户看得见它在动，也让出错点定位到句级。
- 技巧 4：UserProxyAgent 的两种模式（只出现在第 4 章的图里）：Automatic Mode 自动执行代码，Human Input 模式在需要时停下来问人。图里给 AssistantAgent 的职责是生成方案、写代码、提改进，UserProxy 负责执行并把结果送回，形成迭代。我的思考：讽刺的是，全片最有设计感的这个模式在代码章里一次都没用上——双 agent 例子用的是两个 AssistantAgent。看教程时要把「图里的架构」与「跑过的代码」分开计价。
- 技巧 5：标题与章节名是营销文案，不是目录。Studio、Deployment、Use cases 三处标题都与正文不符（见存疑点逐条）。我的思考：这条与第 33 条的章节错位合起来是一课——大机构与小频道都会在标题层夸大，核验型笔记的意义有一半在这里：按代码与画面记，不按标题记。

### 四、详细笔记（按章节）

- 第 1 章 概念：Agentic AI、多 agent 会话；AutoGen 是微软研究院的开源框架，agent 是有具体岗位的 AI；范式转变口号是从「AI 给我一个答案」到「AI 把整个任务做完」。用例点名：长文档摘要、头脑风暴、数据分析（注意：这些都在第 1 章，不在第 7 章）。章内有一段 Simplilearn 与 IIT Delhi 证书项目的口播推广，属频道内容、非平台广告，已排除。
- 第 2 章 安装：前置是 Python（口述要求 3.9 以上；讲者自己状态栏是 3.13.12）。虚拟环境讲成「隔离的盒子」，装 AutoGen 的确切 pip 命令口述很快、画面没有可读定格，不可读——不要照口述猜命令，尤其注意第二节说的包名陷阱。模型两条路：云端 API key，或者本地 Ollama（口述卖点：不要 key、离线、没有 token 限制——「没有 token 限制」是讲者口径，本地算力就是实际限制）。幻灯片命令原文见保留区。Ollama 侧：查版本、ollama pull llama3；可选装 OpenAI 包并把 key 放环境变量。
- 第 3 章 第一个 agent：main.py 全文可读（保留区）。要点是三件套导入——AssistantAgent、Console（流式 UI）、OllamaChatCompletionClient；assistant 的 system_message 是「You are a helpful AI assistant.」，任务是「What is AutoGen?」，在激活了 autogen_env 的终端里流式输出。编辑器标签页还能看到 app.py、two_agents_write_and_review.py、one.py 等文件，结尾的结构图却只列 main.py 与 app.py。
- 第 4 章 协作图：见技巧 4。图面文字原样：「Conversable and Customizable Agents」「Fully Automated or Human in the Loop」「Adapt and Improve Solutions」。
- 第 5 章 双 agent：writer（system：「You are a writer. Write a short explanation about AutoGen.」）与 reviewer（system：「You are a reviewer. Critique and improve the writer's output.」）共用一个 llama3 客户端；幻灯片注释四条：Async Programming、Create Model Client、Define AI Agents、Run the Code。运行与接线的下半段代码被幻灯片下缘截断，不可读；讲者称评审后的输出更 refined，但画面没有给出可核对的完整运行记录。
- 第 6 章 FastAPI 包装：动机讲得对——终端里的 agent 嵌不进网站，要包一层 HTTP。例子是「FastAPI Chatbot with TinyLlama」：TinyLlama 经 Ollama 客户端接入，assistant 的 system_message 是「Reply only with "hi!" no matter what the user says」，/chat 端点收 query 参数、永远回「hi!」；在 VS Code 里跑起来、Chrome 打开 /chat?query=… 验证。端点的 return 行被字幕条遮挡，不可读。讲者补了一句选 TinyLlama 的理由：小模型好存。
- 第 7 章 结尾：实际内容是跟进方法——每周花几分钟看官方仓库（releases 看改了什么、issues 看真实问题、PR 看在做什么），加社区与 Discord。这条建议与框架已进入维护模式的现状冲突，见第二节。

### 五、原样保留区

第 3 章 main.py（画面原文，12:02 定格全可读）：

```python
import asyncio
from autogen_agentchat.agents import AssistantAgent
from autogen_agentchat.ui import Console
from autogen_ext.models.ollama import OllamaChatCompletionClient

async def main():
    model_client = OllamaChatCompletionClient(
        model="llama3",
        host="http://localhost:11434"
    )

    assistant = AssistantAgent(
        name="assistant",
        model_client=model_client,
        system_message="You are a helpful AI assistant."
    )

    await Console(
        assistant.run_stream(
            task="What is AutoGen?"
        )
    )

if __name__ == "__main__":
    asyncio.run(main())
```

第 2 章幻灯片命令（画面原文）：

```text
python -m venv autogen_env
Windows 激活：autogen_env\Scripts\activate
Mac/Linux 激活：source autogen_env/bin/activate
ollama --version
ollama pull llama3
```

第 6 章 FastAPI 例（画面原文，可读部分）：assistant 的 system_message 为 `Reply only with "hi!" no matter what the user says`，端点为 `@app.get("/chat")`，模型为 OllamaChatCompletionClient(model="tinyllama", host="http://localhost:11434")。return 行不可读，不补。

### 六、术语表

- AssistantAgent：AutoGen AgentChat 层的主力 agent 类，吃 model_client 与 system_message。
- UserProxyAgent：代表人的一端，能执行代码并回传结果，或停下来向人要输入；本片只在图里出现。
- OllamaChatCompletionClient：autogen_ext 提供的 Ollama 模型客户端，默认 host 为本地 11434 端口。
- 维护模式（maintenance mode）：AutoGen 自 2025-10-02 起的状态——只修 bug 与安全问题，不加新功能，官方引导新用户去 Microsoft Agent Framework。

### 七、易错点和存疑点

- **标题与正文不符（Studio）**：标题三段关键词里 AutoGen Studio 权重最高，正文零出现；章节目录里也没有 Studio。以正文为准。
- **「Deployment」名不副实**：第 6 章是本地运行 + 浏览器自测，没有部署到任何外部环境这一步。
- **「Use cases」名不副实**：用例在第 1 章，第 7 章是跟进方法论；且其前提（框架快速加新功能）已被维护模式推翻。
- **口述承诺未兑现**：第 5 章说代码会放简介，简介里没有代码或仓库链接（只有课程销售链接）。
- **装包命令不可读且有陷阱**：确切 pip 命令没有可读画面；即使照老教程写 `pip install autogen`，现在装到的也是 AG2 分叉而非微软包——这是本条最容易让新手栽的一处。
- 幻灯片小错：把 `python -m venv autogen_env` 说成「创建并激活」虚拟环境，venv 只负责创建，激活是紧接着的另一条命令。
- 变量命名不一致：第 3 章代码用 model_client，第 5 章幻灯片用 model 指同一个客户端对象。
- 字幕/转录乱码多处（「3.1, 3.1」实为状态栏的 3.13.12；「five capitalists」一句无法还原，不采用）；YouTube 自带字幕对 AutoGen、Ollama 等专名基本正确，本条听误主要来自音频转录层，引用以画面文字为准。
- 第 5 章双 agent 的运行/接线代码不可读（幻灯片下缘截断），「两个 agent 如何对话」在本条没有可核原文，不要脑补成 RoundRobin 之类的具体机制。

---

## 35. Master Google's Agents Development Kit (ADK) in 60 mins– Build Production Agentic Workflows today!

### 一、元信息

- 标题：Master Google's Agents Development Kit (ADK) in 60 mins– Build Production Agentic Workflows today!（页面原样）
- 链接：https://www.youtube.com/watch?v=EuKWEO4SQJg
- 频道：The AI Lifestyle（1,800 订阅；与第 31 条同频道）
- 时长：1:00:57（播放器实测；标题与简介均称 60 分钟）
- 发布时间：2025-04-17（ADK 发布当月）
- 观看量：19,142 次；点赞 509
- 配套仓库：https://github.com/theailifestyle/google-adk-demos （已克隆对照，15 个 .py 文件全部通过字节编译；仓库文件名与视频资源管理器画面一致）
- 核验档：A 档（播放器逐章扫 + 仓库克隆对照；播放器里多数代码屏 360p 不可读，代码以仓库层为准并逐处标明）
- 章节（简介原样）：0:39 Introduction to Google ADK；4:03 ADK Building blocks : Tools；07:25 ADK Building blocks : Agents；11:25 ADK Building blocks : Callbacks / Guardrails；13:07 ADK Building blocks : Runtime and Workflow；18:54 Code Build : Quickstart Sample. Web UI；22:00 Code Build : Multi-Agent Systems (Streamlit)；35:30 Code Build : Sequential Workflow Agent (Streamlit)；38:37 Code Build : Loop Workflow Agent (Streamlit)；42:46 Code Build : Parallel Workflow Agent (Streamlit)；45:34 Deploy Agents : Vertex Agent Engine；60:00 Conclusion

### 二、一句话结论与摘要

ADK 发布当月的一小时全链路课：先用思维导图把框架拆成 Tools / Agents / Callbacks / Runtime 四块，再写五个应用，最后部署到 Vertex AI Agent Engine——野心最大的框架入门之一，但播放器里的代码大多看不清，真正的代码原文在配套仓库里。

- 框架地图：工具分函数工具、agent 当工具、内建工具（Google Search、代码执行、RAG）、第三方工具（LangChain、CrewAI 均可接）与 MCP；agent 分 LLM 驱动的非确定型与 Sequential / Loop / Parallel 三种确定型工作流 agent；运行时由 session service 与 memory service 托底。
- 最值得记的一条区分：sub-agent 不必向父 agent 返回输出，工具则必须返回——同一个多 agent 应用，视频用两种接法各搭了一遍（22:00 与 33:03），选型的判据就是「父级要不要这个结果」。
- 一处三方对不上的小错也是本条的核验样本：快速开始的天气字符串里写着「25 degrees Celsius (40 degrees Fahrenheit)」——25°C 实际等于 77°F；而当前仓库里同一行已改成 41°F，仍然是错的。画面层与仓库层在这一行上互相不一致，引用时要分清。
- 时效性（截至 2026-11-01）：google-adk 现行版本 2.13.0（PyPI 核于 2026-11-01），视频是发布当月（2025-04）的 0.x/1.x 时代形态：内建工具的两条限制（一个 agent 不能挂多个内建工具、sub-agent 不能用内建工具）、Agent Engine 的可用区域（口述仅 US Central 1 一带）与计费口径都应按时点理解，不要当现状。仓库里快速开始用的模型是 gemini-2.0-flash-exp（仓库层），也已是实验代际。

### 三、技巧总结

- 技巧 1：用「事件账本」理解 ADK 的运行时。讲者称最重要的那张图是全流程：用户提问 → Runner 建 session → agent 让 LLM 决定调工具 → 函数调用被登记为一个 Event → Runner 把 Event 写进 session state → 过 callback 检查 → 工具执行 → 结果经 Runner 与 session state 回到 LLM → 成文答复。Event 是活动的登记册与历史。我的思考：这与第 31 条 Agents SDK 的 Traces 是同一种设计自觉——运行时先是一本账，其次才是一个循环；排障与审计都从这本账开始。
- 技巧 2：sub-agent 与 agent-as-tool 的选型判据。子 agent 是独立执行块，跑完不欠父级一个返回值；工具则必须把结果交回调用者。同一个家教应用先以 sub_agents 方式搭（数学 sub-agent 直接把结果返回用户），再以 AgentTool 方式重搭（路由器自己收尾回答、追问也留在路由器），还演示了「工具套工具」：数学 agent 作为工具、内部再调自己的加法工具。我的思考：要不要返回值，决定了对话的「落点」在谁那里——子 agent 模式下用户是在和专家说话，工具模式下用户始终在和路由器说话。产品体感的差异就藏在这个选择里。
- 技巧 3：护栏分三层、挂六个点。agent / tool / model 三个层级各有 callback，执行前与执行后都能拦。演示里的两个护栏分工明确：违禁词拦在 model 层，语言护栏拦在 tool 层（不许把法语词翻成西班牙语，配了正则做法语检测，见仓库导入）。我的思考：把护栏挂在哪一层，等于声明你不信任谁——不信任用户输入挂 model 前，不信任工具产出挂 tool 后。分层护栏比一句「加个安全检查」精确得多。
- 技巧 4：确定型工作流 agent 当容器，LLM agent 当零件。SequentialAgent 串起「写代码 → 评审 → 重构 → 解释执行」四件套；LoopAgent 里评论家与写手靠 output_key 在状态里交接草稿，子 agent 的排列顺序决定谁先跑；ParallelAgent 把一个主题拆成三个子主题并行检索再汇总（拆子主题那步本身是个普通 LLM 函数，不是 agent）。我的思考：这与第 31 条「代码编排与 LLM 编排混合」收敛到同一结论，只是 ADK 把混合做成了框架原语：确定性骨架是类型，灵活性是零件。
- 技巧 5：函数工具的 docstring 就是工具合同。函数工具就是普通 Python 函数，但 docstring 必须写全名称、用途、参数与返回值，因为那是 LLM 决定怎么调它的唯一依据。我的思考：与第 32 条 RetrieverTool 的 inputs 说明是同一课——工具的自然语言界面（docstring / description / inputs）是提示工程里复用率最高的部分，且必须与实现对齐（第 32 条的反例就在隔壁）。

### 四、详细笔记（按章节）

- 0:39 总览（思维导图）：agent 三分（非确定型 / 确定型 / 自定义混合）；工具全家福（函数工具、agent 当工具、内建工具、第三方工具、Apigee 等 GCP 组件、MCP、工具鉴权 API key / OpenID Connect / OAuth 2）；运行时两件套 session service（会话状态与参数）与 memory service（短期记忆绑会话；长期记忆；数据库持久化 vs 内存易失）；评测与部署（Vertex AI Agent Engine）只被点名——全片没有任何评测演示，简介「build, test, evaluate, and deploy」的承诺在 evaluate 一格上是空的。
- 4:03 工具：ADK 是异步的，支持长时运行函数。内建工具的两条时点限制（见时效性）。企业侧：GCP 连接器、经 MCP Toolbox 连数据库、MCP 协议支持；LangChain 经 Tavily 工具、CrewAI 经 Serp 工具接入（口述，Serp 的确切产品名把握度较低）。
- 7:25 agent：确定型三件套的语义——Sequential 按列表顺序，Loop 按固定次数或停止条件，Parallel 同时跑、结果汇总（适合提速与多源独立信息）。讲者称生产环境最好的输出通常来自自定义混合 agent。
- 11:25 护栏：见技巧 3；讲者称三层 callback 是 ADK 区别于其他框架之处（厂商口径）。
- 13:07 运行时与工作流：粒度自下而上是工具 → agent → 运行时（Runner + 两个 service）；多 agent 的两种组织即技巧 2；事件流程见技巧 1。
- 18:54 快速开始：目录结构——agent 文件夹内 __init__.py、.env（放 Google API key）、agent.py；`pip install google-adk` 后 `adk web` 开自带 Web UI，可在 UI 里测流式、音频、视频。例子是两个写死返回值的工具（get_weather、get_current_time）加一个 agent（model / description / instruction / tools）。仓库层全貌见保留区：根 agent 名 weather_time_agent，模型 gemini-2.0-flash-exp（这行在播放器里不可读，来自仓库）。
- 22:00 多 agent 应用（Streamlit）：经 LiteLLM 同时用 OpenAI 与 Google 两家的 key（用 AI Studio 的 key 要设 GOOGLE_GENAI_USE_VERTEXAI=false）。家教路由器分流：数学题 → 数学 agent（加法工具），西班牙语 → 翻译 agent，时事 → 带 Google Search 内建工具的搜索 agent。搭建顺序：指令 → session state → 两道护栏（见技巧 3）→ 读 key → 工具定义 → 各 agent → Runner。演示里违禁词请求被护栏当场踢出。33:03 起同一应用改工具式重搭，见技巧 2。
- 35:30 Sequential：代码流水线四件套（writer → reviewer → refactorer → interpreter），用 InMemorySessionService 与 Runner 启动；演示题是「按原价与折扣算金额」的函数，右侧逐步显示，解释器在后端真跑出了结果。
- 38:37 Loop：路演稿打磨器——评论家 agent 的反馈经 output_key 存进状态，写手据此改稿，循环往复。演示题是个「茶叶算命」应用：评论家先批没有独特 IP，草稿加上「billion dollar market」；评论家再批太空泛、要拿数据支撑「90%」；终稿（字幕原样大意）变成「$50 billion global divination market……90% accuracy validated by independent studies」——讲者自己承认「obviously it's fudging」。数字是模型编的，本条只登记、不背书。
- 42:46 Parallel：主题 → 生成 3 个子主题（用户可编辑勾选）→ 三个研究 agent 并行检索 → 汇总 → 综合 agent 成稿；演示主题是「AI 与医疗」。
- 45:34 部署：自管（Docker / GKE / 本地）vs 云端（Agent Engine、Cloud Run——但完整演示只走了 Agent Engine 一条路）。Agent Engine 的口径：「like Cloud Functions but better for agents」、自带 agent 记忆、支持 ADK / CrewAI / LangChain、靠容器化部署；计费按 CPU 与内存（具体金额画面不可读、字幕截断，未捕获）。流程：装 Google Cloud SDK 并 gcloud init → 仓库里的管理 CLI（agent_management_cli.py，import vertexai 的 agent_engines 做 list / update / delete，首跑显示「No agents found.」）→ 部署代码里 init Vertex AI（项目、区域、GCS 桶），根 agent 包进 AdkApp 开追踪，`agent_engines.create` 部署，口述耗时至少两三分钟。演示小插曲：Streamlit 里残留旧 engine ID 导致首连失败，换上新 ID 才通；演示完当场把 agent 删掉省钱。部署例子是西 / 法 / 古英语三个翻译 agent 以工具形式挂在一个协调 agent 下（关闭汇总）。

### 五、原样保留区

快速开始 agent.py（仓库层原文，已克隆、字节编译通过；注意华氏数字与视频画面不一致，见存疑点）：

```python
import datetime
from zoneinfo import ZoneInfo
from google.adk.agents import Agent

def get_weather(city: str) -> dict:
    """Retrieves the current weather report for a specified city.
    Args:
        city (str): The name of the city for which to retrieve the weather report.
    Returns:
        dict: status and result or error msg.
    """
    if city.lower() == "new york":
        return {
            "status": "success",
            "report": (
                "The weather in New York is sunny with a temperature of 25 degrees"
                " Celsius (41 degrees Fahrenheit)."
            ),
        }
    else:
        return {
            "status": "error",
            "error_message": f"Weather information for '{city}' is not available.",
        }

root_agent = Agent(
    name="weather_time_agent",
    model="gemini-2.0-flash-exp",
    description="Agent to answer questions about the time and weather in a city.",
    instruction="I can answer your questions about the time and weather in a city.",
    tools=[get_weather, get_current_time],
)
```

（get_current_time 全文同在仓库：New York 对应 ZoneInfo("America/New_York")，其余城市返回 error。视频画面里该函数体不可读。）

多 agent 文件 streamlit_agents_base_sub.py 的导入区（画面可读片段）：可见 `import re  # Import regex for French detection`、`from google.adk.models.lite_llm import LiteLlm`、`from google.adk.sessions import InMemorySessionService, Session`、`from google.adk.runners import Runner`、ToolContext / CallbackContext / LlmRequest / LlmResponse / BaseTool / FunctionTool 等——护栏与工具上下文的类型在导入层坐实，正文以仓库同名文件为准。

### 六、术语表

- Runner：ADK 的运行时驱动，负责建 session、调度 agent 与工具、登记 Event。
- Event：一次运行中每个动作（函数调用等）在 session state 里的登记记录，兼作历史。
- output_key：Loop 等工作流里子 agent 把产出写进共享状态的键，下一棒从状态里取。
- AgentTool：把一个 agent 包装成工具供另一个 agent 调用的机制；与 sub_agents 组织方式相对。
- Agent Engine：Vertex AI 的全托管 agent 部署服务（视频时点口径，区域与计费已变）。

### 七、易错点和存疑点

- **温度数字三方不一致**：视频画面 40°F、当前仓库 41°F、正确答案 77°F（25×9/5+32）。这是 Google 官方快速开始示例流传下来的老错，视频与仓库各保存了一个错误版本；引用这段代码时别把任一数字当真。
- **评测缺席**：标题与简介都打「evaluate」，全片无评测章节与演示；这条不能当 ADK 评测的学法。
- 章节时间戳小毛刺：简介里 Deploy 一行与 Conclusion 一行粘连、没有独立显示 45:34（靠口述转场核定）；Conclusion 标在 60:00，全片 1:00:57，结尾只剩 57 秒。
- 区域口径自相矛盾：47 分一带口述 Agent Engine 可用「US Central 1 and West one」，51 分一带又说部署代码「just US central 1」——以「视频时点、区域有限」理解，不取任一为现状。
- 内建工具清单用词不一：总览章说 code execution，工具章说 code interpreter，指同一内建工具。
- 章节边界的画面滞后：18:54 章节已开始、画面还是思维导图；22:00 已进入多 agent 章、画面还是快速开始的代码——按口述与字幕定章节，不按画面定。
- 播放器不可读清单（代码以仓库层补齐并已标注）：快速开始的 agent 定义行、多 agent 的 agent/tool 定义正文、三个工作流的 Streamlit 文件、部署与 CLI 的代码屏、Agent Engine 计费幻灯片金额、思维导图的分支小字、Streamlit 的事件面板小字与部署后的 engine ID。
- Loop 演示里的市场与准确率数字（$50 billion、90%）是被打磨出来的编造文案，讲者当场承认是 fudging；只可作为「循环会把文案磨得更像样、也会把数字磨得更离谱」的例证引用。


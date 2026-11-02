# YouTube Agent 技巧视频笔记 · 第 46–50 条

> 核验型笔记第三批之第四组（生产化：持久化、结构化输出、无代码自动化、基准设计与自托管观测）。取材标准：字幕、时间戳、代码原文三样齐全；播放器逐章核验，配套仓库克隆对照，能跑的代码实际跑过；看不清处标「不可读」，推断与原文分开标注。本组第 46 条扫读时播放器画质受限、代码靠仓库原文解明，第 50 条为 AI 生成内容、画面不作证据，均在各节中如实标注。

## 目录

- [46. Build a Deep Research Agent | Python, OpenAI, Temporal](#46-build-a-deep-research-agent--python-openai-temporal)
- [47. Pydantic Explained for AI Engineers + Pydantic AI Agents](#47-pydantic-explained-for-ai-engineers--pydantic-ai-agents)
- [48. How to Create an AI Email Agent with n8n (No Code, Step-by-Step Tutorial)](#48-how-to-create-an-ai-email-agent-with-n8n-no-code-step-by-step-tutorial)
- [49. Senior SWE-Bench: Evaluating Coding Agents | Snorkel AI Reading Group](#49-senior-swe-bench-evaluating-coding-agents--snorkel-ai-reading-group)
- [50. Langfuse GitHub Explained: Self-Hosted LLM Observability with OpenAI Tracing](#50-langfuse-github-explained-self-hosted-llm-observability-with-openai-tracing)

---

## 46. Build a Deep Research Agent | Python, OpenAI, Temporal

### 一、元信息

- 标题：Build a Deep Research Agent | Python, OpenAI, Temporal（页面原样）
- 链接：https://www.youtube.com/watch?v=eyLf4Kqd6lg
- 频道：Temporal（官方）；讲者 Nikolay Advolodkin
- 时长：32:09（播放器实测）
- 发布时间：2026-02-26
- 观看量：3,166 次；点赞 64
- 配套代码：temporalio/edu-deep-research-tutorial-template 仓库 video_tutorial 分支（简介直达，已克隆，全文件编译通过）；另有官方文字教程（learn.temporal.io）
- 核验档：A 档（播放器逐章扫 + 仓库克隆解明；扫读时播放器为 144p，画面代码几乎全不可读，代码层事实以仓库为准并逐项标注来源层）
- 章节（简介与播放器一致，共 61 条，主题依次为）：进程中断问题 → 五 agent 管线地图（triage / 澄清 / planner / search / writer）→ OpenAI Agents SDK 链式调用 → 把管理器搬进 Temporal workflow → query / signal / update 三种通信 → wait_condition 等待人工 → worker 与重试策略配置（120 秒超时、最多 5 次）→ FastAPI 端点改走 workflow handle → 三终端联调 → 当场停掉 worker 做重启实测 → 报告复盘。完整时间戳以视频简介为准。

### 二、一句话结论与摘要

这条官方教程讲的不是怎么做深度研究，而是怎么让深度研究「死不了」：把一个只写快乐路径的普通管理器类原样搬进 Temporal workflow，因为它的模型调用发生在 workflow 上下文里，被 OpenAI Agents 插件自动接管成持久化活动——进程被杀、worker 重启，研究从断点续跑，已花的模型调用一分不重花。

- 管线本身是第 31 条式样的链式编排：triage 判断问题够不够具体，不够就由澄清 agent 追问，用户答齐后再 plan → search → write。持久化不改这条管线的任何一行。
- 人在回路中的等待是这条最值得抄的设计：workflow 用 wait_condition 挂起等用户答复，挂起期间不占资源、可以等几天几周；传统写法要靠轮询、长连接或自制状态机硬撑。
- 时效性（截至 2026-11-01）：仓库 video_tutorial 分支与视频口径逐项一致（见五），仍可直接跟做；视频全程没报任何模型名与库版本，模型配置只在仓库代码里（见五），引用参数以仓库为准。

### 三、技巧总结

- 技巧 1：持久化靠「在哪里运行」，不靠「代码里写了什么」。管理器类没有任何 Temporal 代码、没有手写重试、没有检查点；耐久性来自它被实例化在 workflow 内部、调用经插件拦截。我的思考：这是把可靠性做成环境属性而不是业务代码属性——业务代码只写快乐路径，失败语义由运行时统一提供；代价是必须接受运行时的约束（workflow 代码的确定性要求等），选型时要连约束一起评估。
- 技巧 2：等人工输入用条件等待，不要轮询。run 方法先后等两个条件：研究已初始化、澄清答案已收齐；条件由 update 写入状态时唤醒。我的思考：凡是「等用户、等审批、等外部回调」的 agent 流程，都可以照这个形状建模——状态放在持久层、等待不烧资源、唤醒是事件驱动；自己用队列加轮询拼出来的版本，通常既贵又容易在重启时丢状态。
- 技巧 3：三种通信方式按「要不要返回值」选。query 只读状态；update 改状态且同步返回结果（开始研究、提交答复都用它）；signal 只改状态不返回（异步场景再配 query 轮询读结果）。我的思考：讲者有一处口误值得注意——他一边说本方案「不需要轮询」，一边又建议异步客户端用 signal 加 query 轮询；两者不矛盾（前者指 workflow 内部，后者指客户端读结果），但把这层区分讲清，才算真懂这组原语。
- 技巧 4：重试参数要给全套，不要只给次数。仓库配置是：单次调用超时 120 秒、首次重试等 1 秒、间隔上限 30 秒、退避倍数 2.0（1、2、4、8…）、最多 5 次。我的思考：只有次数没有超时，卡死的调用永远不失败、重试永远不触发；超时才是重试策略的起点，这一组五个数值得原样抄成自己的默认模板。
- 技巧 5：会话即工作流 ID。FastAPI 端点不维护自己的会话表，workflow ID 直接当 session ID，后续所有交互都经 handle 做 query / update / 等结果。我的思考：少一层自制会话存储，就少一处双写不一致；凡是运行时已经给了稳定句柄的场景，不要再发明自己的 ID 映射。

### 四、详细笔记（按章节）

- 0:00–3:39 问题定义：普通实现里进程一挂，agent 就卡死，已完成的模型调用全部浪费、重启也救不回来；开场画面直接放了一段失败现场（聊天报错 + 服务关停日志，是后段演示的蒙太奇，不是当场直播）。目标：能恢复、能续跑、不重花钱。
- 3:39–6:13 管线与 SDK：五个角色——triage（判断问题是否够具体）、澄清（不够具体时追问）、planner（定搜索计划）、search（用内置网页搜索执行并给摘要，可并发）、writer（合成报告）；用 OpenAI Agents SDK 的 Agent + Runner 链式串联，前一个的 final_output 喂给下一个（画面可读片段与仓库结构一致）。
- 6:13–10:30 引入插件与管理器：OpenAI Agents SDK 有官方 Temporal 插件；管理器类按普通 Python 写完 plan / search / write 三个方法，再包一层带澄清逻辑的 run 方法（先 triage、需要澄清就返回问题、不需要就直接跑管线）。讲者强调这一层「没有任何特殊 Temporal 代码」。
- 10:30–18:00 搬进 workflow：内存版管理器的病根是状态随进程蒸发；解法是建 research workflow，在其中实例化管理器，并定义 UI 与 workflow 之间的数据类与三个处理器——get status（query）、start research（update）、provide clarification（update）。run 方法用 wait_condition 先后等「研究开始」与「答案收齐」，其间零消耗。旧的内存版会话管理器在片中被删除。
- 18:27–21:21 worker 配置：建 worker、挂 OpenAI Agents 插件、按任务队列（仓库值 deep-research-queue）路由；超时与重试五参数在此讲透（见技巧 4），原理是插件把 workflow 内的模型调用拦截为持久活动。
- 21:21–25:33 服务端改造：FastAPI 端点全部改走 Temporal client 与 workflow handle：启动端点创建 workflow 并执行 start research update、状态端点 query、答复端点 update、结果端点等 workflow 完成取报告；workflow ID 即会话 ID。
- 26:03–31:26 联调与重启实测：三终端分别起 Temporal server（UI 在 localhost:8233）、worker 与应用（localhost:8234）；先演示修了三个联调问题（应用初始化、健康端点、uvicorn 端口）才跑通，随后研究进行中当场停掉 worker——Temporal UI 里 workflow 状态原样停在原地；重启 worker 后研究续跑，被打断的搜索按策略重试（画面显示第 2 次尝试、共 5 次上限），不重复已完成的调用，最后 writer 产出报告。
- 31:26 收尾：讲者看完报告后当场表示其中推荐地的安全性他并不认同——与报告自身的宜居口径不一致，照录为演示真实性的一部分。

### 五、原样保留区

链式调用（画面可读片段，讲者幻灯片）：三个 agent 依次以 Runner.run 执行，前者的 plan.final_output / results.final_output 作为后者输入（片段后半被遮挡处未补）。

仓库原文核对（video_tutorial 分支，2026-11-01 克隆，全文件编译通过）：

- 任务队列：TASK_QUEUE = "deep-research-queue"（run_server.py 与 run_worker.py 一致）——解明口述被字幕转录成「deep research Q」的部分。
- 活动选项（run_worker.py）：start_to_close_timeout = 120 秒（代码注释明写为单次 LLM 调用的最长时限）；RetryPolicy：initial_interval 1 秒、maximum_interval 30 秒、backoff_coefficient 2.0、maximum_attempts 5——与口述逐项一致。
- 处理器（research_workflow.py）：1 个 @workflow.query + 2 个 @workflow.update，与口述的 query / update 分工一致；未使用 signal。
- 模型（仓库层，视频未述）：triage、clarifying、planner、search 四个 agent 用 gpt-4o-mini，writer 用 gpt-4o（代码注释说明写作换更强的模型）。

### 六、术语表

- workflow（Temporal）：可持久化、可重放的长流程执行单元，进程重启后从事件历史恢复进度。
- activity：workflow 里真正执行外部调用的单元，本条中模型调用被插件自动变成活动。
- wait_condition：按状态条件挂起 workflow 的原语，条件被 update 改写时唤醒，挂起不耗资源。
- query / update / signal：客户端与 workflow 的三种通信——只读、可改且有返回、只改无返回。
- task queue：worker 监听的任务队列名，决定哪类工作被哪组 worker 执行。

### 七、易错点和存疑点

- 两处章标题与内容不符：11:24 与 16:10 的标题都写了 signals，但 16:10 实际实现的是 update，signal 的定义在 15:14 独立给出；仓库实现也只有 query + update，按实现理解，不要按标题背。
- 视频不报模型与版本：凡「这条用的是什么模型」只能答仓库层（见五），不要把仓库配置说成视频口述。
- 扫读画质为 144p，除链式调用一片段外画面代码全不可读；本条的代码层事实全部来自仓库克隆并已标注，画面与仓库若将来分叉，以引用时点为准。
- 结尾报告里有一个地名在字幕中被截断，未猜测补全。

---

## 47. Pydantic Explained for AI Engineers + Pydantic AI Agents

### 一、元信息

- 标题：Pydantic Explained for AI Engineers + Pydantic AI Agents（页面原样）
- 链接：https://www.youtube.com/watch?v=CnbrzKq1sAQ
- 频道：Imad 4 Automation（@imad_ui）
- 时长：5:52（播放器实测；首次加载显示 5:53，为 1 秒显示差）
- 发布时间：2026-10-22
- 观看量：25 次；点赞 1（小频道新作，数据按页面原样）
- 配套代码：无仓库，代码全在画面（完整可读）；页面标注 Creative Commons 署名许可
- 核验档：B 档（播放器逐章扫 + 画面代码在本地按现行 pydantic 实跑验证）
- 章节（简介，共 9）：0:00 Why you need Pydantic；0:30 The real problem: the boundary；1:10 What Pydantic is；2:00 Reason 1: One class, three jobs；2:31 Reason 2: Your function signature is the tool；3:17 Reason 3: Errors become feedback；4:11 Reason 4: It's already in your stack；4:45 Pydantic AI runs the loop for you；5:25 Prompts are suggestions, schemas are contracts

### 二、一句话结论与摘要

这条不到 6 分钟的短片把 Pydantic 在 AI 工程里的位置讲成一句话：模型那一侧是概率的，代码这一侧是确定的，边界上需要一份能执行的契约——一个 BaseModel 类同时做三份工：生成发给模型的 JSON Schema、校验模型交回的数据、给开发者类型补全；校验失败的报错还能原路喂回模型让它自己改。

- 全片的收束句是「提示是建议，schema 是契约」：建议靠模型自觉，契约靠代码强制，两者的可靠性不在一个量级。
- 最有分量的例子是发票校验：类型全对、总额却对不上明细——这类业务规则幻觉只能靠 model_validator 拦，再把报错当提示词发回去重试。
- 时效性（截至 2026-11-01）：pydantic 已到 2.14.2、pydantic-ai 已到 2.64.0（实查）；视频不报版本号，画面 agent 代码里的模型字符串版本位不可读，本笔记不补。核心概念（边界、契约、错误反馈环）与版本无关。

### 三、技巧总结

- 技巧 1：一个类，三份工，定义只写一遍。类定义一次，model_json_schema 产出给模型的 schema、model_validate_json 校验回程、编辑器补全用同一份类型——三处永远同步，不会出现「提示里写的字段和代码里解析的字段对不上」。我的思考：手写 JSON 解析代码的团队，字段定义通常散在提示、解析、类型三处，改一处漏两处是常态；契约单一来源是结构性解法，不是风格偏好。
- 技巧 2：函数签名就是工具定义。类型注解变参数 schema、docstring 变工具描述、非法参数在函数执行前就被拒。我的思考：这意味着工具函数的签名要按「写给模型看的接口」来打磨——参数名、类型收窄（如 Literal 二选一）、docstring 第一句，都是提示工程的一部分，只是写在代码里。
- 技巧 3：校验分两层，类型层之外还有业务层。Literal 与数值范围挡格式错误，model_validator 挡「格式全对、事实不对」（总额与明细不符）。我的思考：把业务规则写成 validator 的附带好处是报错文本天然是给模型看的反馈——错误信息要写成人话，因为它的读者一半是人、一半是模型。
- 技巧 4：错误反馈环可以整体外包。Pydantic AI 里把模型类作 output_type、retries 设 3，schema 生成、校验、报错回喂、重试由框架跑完，run_sync 返回的直接是校验过的对象。我的思考：自己手写这个循环不难，难的是每个项目都重写一遍且行为不一致；这类「形状固定、内容可变」的循环正是框架该收走的部分，换提供商只改一个模型字符串也是同理。
- 技巧 5：先认边界，再选工具。讲者先把「概率 vs 确定」这条边界立起来，Pydantic 的所有用法都挂在边界上（入境校验、出境 schema、工具入参）。我的思考：这个框架可以迁移到任何「模型输出要进确定性系统」的场景——先问数据在哪一刻跨界、跨界时谁来验，比先学 API 更重要。

### 四、详细笔记（按章节）

- 0:00–1:10 边界问题：模型返回的不是数据，是「长得像数据的文本」，格式随问随变；代码侧要整数就必须拿到整数。Pydantic 的定位就是边界上的守门人：用普通类型注解把想要的数据形状写成类。
- 1:10 What Pydantic is：Sentiment 模型演示（见五）——喂合法数据得到真正的 Python 对象；喂 label 为 happy、score 为 7 的垃圾输入，报错精确指出哪个字段、错在哪，不会有半有效的数据漏进应用。
- 2:00 Reason 1：一个类三份工（schema 生成、回程校验、补全），讲清定义单一来源。
- 2:31 Reason 2：get_weather 工具演示（见五）——装饰器把普通函数注册为工具，签名与 docstring 即定义，坏参数在执行前拦截。
- 3:17 Reason 3：发票场景——模型读 PDF 后自信地给出与明细之和不符的总额（画面数字：明细 400+350+300=1050，总额却写 1250）；validator 拦截后，把报错原路发回模型，模型修正后交回正确数据。
- 4:11 Reason 4：生态位——Pydantic 已经垫在 OpenAI、Anthropic、Google 的 SDK 以及 LangChain、LlamaIndex、FastAPI 之下（此帧画面里 FastAPI 节点未出现，该节点仅口述），学它等于学整个 Python AI 生态的通用层。
- 4:45 Pydantic AI：把 Invoice 类作 output_type、retries=3 建 agent（画面代码见五，模型字符串版本位不可读）；result.output 直接是校验过的对象而非待解析文本。
- 5:25 收束：提示是建议、schema 是契约；AI 工程师的活就是把不可预测的模型输出变成软件可以信任的数据。

### 五、原样保留区

画面代码（完整可读；本组已在本地按 pydantic 2.14.2 实跑，两类的行为宣称全部复现）：

```python
class Sentiment(BaseModel):
    label: Literal['positive', 'negative', 'neutral']
    score: float = Field(ge=-1, le=1)   # 画面注释：between -1 and 1

class Invoice(BaseModel):
    vendor: str
    total: float
    line_items: list[float]

    @model_validator(mode='after')
    def check_total(self):
        if abs(sum(self.line_items) - self.total) > 0.01:
            raise ValueError('total must equal sum of line_items')
        return self
```

实跑核对（2026-11-01，非视频内容）：Sentiment 的 schema 正常产出两字段；坏输入（happy / 7）被拒并同时报出 label 与 score 两个字段错误；Invoice 对 1250 vs 1050 正确拦截、总额对齐时正常通过；400+350+300=1050 与画面给出的 sum 一致，1250 是演示用的幻觉值、不是视频算错。get_weather 工具与 Pydantic AI 的 Agent 构造（output_type=Invoice、retries=3）在画面可读，模型字符串的版本数字不可读，未写入。

### 六、术语表

- BaseModel：Pydantic 的模型基类，类属性即字段定义，兼作校验器与 schema 来源。
- JSON Schema：描述数据形状的标准格式，各家提供商的结构化输出接口都以它为准入契约。
- model_validator：字段级校验之外的整模型校验器，适合总额对明细这类跨字段业务规则。
- output_type：Pydantic AI 里指定 agent 输出模型类的参数，框架据此生成 schema 并校验回程。

### 七、易错点和存疑点

- 播放器章节面板只列到第 8 章（4:45），第 9 段 5:25 在简介与字幕里都存在，是面板遗漏，不是内容缺失。
- 「Pydantic 垫在各家 SDK 之下」是讲者口径，本组只实跑了库本身的行为，未逐家核查依赖关系，引用时保留口径。
- 发票的 1250 与 1050 不一致是演示设计（幻觉样本），不要当作视频的算术错误引用。
- 字幕对 Pydantic 的转录花样繁多（十余种误写），本笔记统一按画面拼写。

---

## 48. How to Create an AI Email Agent with n8n (No Code, Step-by-Step Tutorial)

### 一、元信息

- 标题：How to Create an AI Email Agent with n8n (No Code, Step-by-Step Tutorial)（页面原样）
- 链接：https://www.youtube.com/watch?v=YFrej2oSldo
- 频道：Nate Herk | AI Automation
- 时长：29:45（播放器实测；预热广告期间进度条显示的 43:20 是含广告的总长，不是片长）
- 发布时间：2024-09-21
- 观看量：69,688 次；点赞 1,204
- 配套代码：无仓库，全部配置在 n8n 画布上完成；关键 JSON 与提示词多处不可读（见存疑点）
- 核验档：B 档（播放器逐章扫；无仓库可克隆）
- 章节（简介，共 7）：0:00 Demo；1:23 Prep；2:44 Pushing Data to Pinecone；11:00 Send Email Tool；21:04 Making Agent；25:07 Testing；28:35 Outro（agent 的搭建实际上从约 20:04 就开始，落在 Send Email Tool 章内）

### 二、一句话结论与摘要

这是一条用 n8n 搭邮件 agent 的完整跟做：三条工作流分工——一条把联系人从 Google 文档灌进 Pinecone，一条做成可被调用的「发邮件工具」，第三条才是 agent 本体。全片真正的工程含量不在拖拽，而在工具接口的手工活：把 Gmail 节点的参数手工整理成一条查询串、再让一个模型节点把它解析成结构化字段，以及第一次测试失败后靠补系统提示才修好。

- 模型口径统一：agent、联系人工具与参数解析节点都用 GPT-4o；三处嵌入都用 text-embedding-3-small，且必须与 Pinecone 索引的配置一致。
- 测试段完整保留了失败：第一次测试在发邮件工具处报错，在执行记录里数据明明是对的；讲者把原因归于 agent 的系统提示还是通用占位文本（他的判断，画面没有给出错误原文），用截图喂 ChatGPT 生成一版完整 agent 提示、再手改落款规则后，第二次测试通过，收件箱里两封演示邮件都在。
- 时效性（截至 2026-11-01）：n8n 现行已到 2.45.7（npm 实查），视频是 2024-09 的 1.x 时代界面与节点命名，跟做时节点位置与选项须按现行版对照；工作流拆分与工具接口的设计思路不受版本影响。

### 三、技巧总结

- 技巧 1：工具做成独立工作流，而不是 agent 画布上的一串节点。发邮件被单独做成「被其他工作流调用」的工具流，agent 侧只留一个工具节点加一份输入 schema。我的思考：这样工具可以独立测试、独立复用，agent 换掉也不动工具；代价是两边 schema 必须手工保持一致（片中就是复制粘贴对齐的），这正是低代码省不掉的那份契约维护。
- 技巧 2：工具的返回值要专门设计。发邮件流末尾用 Set 节点硬编码一个 response 字段（值为 sent），agent 靠它确认任务完成、再回复用户。我的思考：工具没有回执，agent 就只能猜做没做完；「动作成功」本身就是一种数据，要显式建模，这一点与用不用代码无关。
- 技巧 3：参数传递先拼串、再解析，是无代码环境里的现实妥协。Gmail 节点参数被整理成一条 & 连接的查询串（画面可读，见五），由一个 GPT-4o 节点解析成独立字段喂回 Gmail 节点；查询串外面必须包一层 JSON.stringify，否则值以对象形式到达、直接报错（片中给出了报错大意：期望字符串却收到对象）。我的思考：这段是全片最不「无代码」的部分，也最诚实——工具间的数据契约绕不过去，低代码只是把它从代码文件搬到了画布上。
- 技巧 4：调试选项要提前打开。讲者在搭 agent 时先把「返回中间步骤」打开，第一次测试失败时才能直接进执行记录逐段看数据。我的思考：与第 45 条的 trace 思想一致——可观测性要在出错之前装好，事后补装就只剩猜测。
- 技巧 5：系统提示是承重件，不是装饰。第一次失败时工具与数据都没问题，卡在 agent 没被好好提示；修法是把工作流截图给 ChatGPT 生成含背景、指令与工具参数说明的完整提示，再手工补上「落款写 Nate」这类细节规则。我的思考：让另一个模型给 agent 写提示这招本身可疑，但本例的教训很实在：工具参数说明必须进系统提示，agent 才知道每个字段填什么——这与第 47 条「签名即工具定义」是同一件事的两种实现。

### 四、详细笔记（按章节）

- 0:00 Demo：成品演示——聊天窗口里一句话让 agent 给 Phil 发邮件问候，agent 回报已发送，邮箱里邮件落款 Nate。联系人信息由模型经向量库找到。
- 1:23 Prep：材料是 n8n、Pinecone（存联系人）、Google 文档（联系人来源）、OpenAI API；计划明说建三条独立工作流。
- 2:44 Pushing Data to Pinecone：Pinecone 建索引时唯一要紧的配置是嵌入模型 text-embedding-3-small（索引名 sample 随意）；工作流为手动触发 → Google Docs 取文档（Doc ID 取 URL 里 /edit 之前的部分）→ Pinecone 插入文档（不是检索），中间挂默认数据加载器与递归切分器（chunk 200、overlap 0）。Google 一侧的 OAuth 配置讲得最细：建项目、启用 Drive/Gmail/Docs 接口、建 OAuth 客户端、配同意屏幕并把自己加为测试用户，否则会报无权限。第一次运行因两个节点没连线而失败，连上即通——低级错误当场保留。
- 11:00 Send Email Tool：工具流以「被其他工作流调用」为触发；Gmail 发送节点里 To / Subject / Message 都是表达式，Email Type 固定 text，n8n 署名开关关掉。然后是全片核心手工活：复制 Gmail 节点 JSON 给 ChatGPT 提取参数表（产物格式他不满意、弃用），自己手写查询串（见五）；在 Gmail 节点前插入 GPT-4o 解析节点把查询串拆成字段；包 JSON.stringify 修复类型错误；最后 Set 节点补 response = sent 回执。
- 21:04 Making Agent（实际从约 20:04 起）：聊天触发 + Tools Agent，输入取聊天变量 chatInput；只挂工具与聊天模型两样（不挂记忆与输出解析器）；工具一是发邮件工作流（输入 schema 从工具流触发器复制对齐、返回字段取 response），工具二是 Pinecone 向量库工具（检索联系人，命名 contacts——与 namespace 的口径问题见存疑点）。
- 25:07 Testing：首测失败与修复全过程见摘要；二测通过后当场打开收件箱核验，两封邮件的主题与正文画面可读（字幕把 Lunch 转录成 launch，以画面为准）。
- 28:35 Outro：讲者称 agent「只有一个工具」——与实作的两个工具不符（见存疑点）；没装 Window Buffer Memory 所以没有对话记忆，是他明说的后续改进点。

### 五、原样保留区

发邮件工具的查询串（画面定格可读，原样）：

sendTo=&subject=&emailType=text&message=&appendAttribution=false&bccList=&ccList=&senderName=

（该帧中 senderName 后为空、光标停在那里；随后在 Gmail 节点侧把 Sender Name 定为 Nate。）

工具参数表（ChatGPT 生成的 agent 提示里列出、画面可读）：sendTo 收件地址、subject 主题、emailType 默认 text、message 正文、senderName 发件人名；并注明 bccList / ccList / appendN8nAttribution 非明确要求不带。agent 系统提示最终版全貌不可读，只有上述 ChatGPT 草稿段与「落款从 Nate」的手工补充可考，笔记未拼凑全文。

画面可读的测试证据：收件箱顶部邮件主题 “Lunch Invitation”，正文为邀请 Phil 吃午饭并落款 Nate；其下是演示阶段的问候邮件。嵌入与切分参数：text-embedding-3-small（三处一致）、chunk size 200、chunk overlap 0。

### 六、术语表

- 工作流工具（Call n8n Workflow Tool）：把另一条 n8n 工作流包装成 agent 可调用的工具的节点。
- namespace（Pinecone 命名空间）：同一索引内隔离数据子集的维度，本片中口径未定（见存疑点）。
- chatInput：n8n 聊天触发器把用户消息放进的标准变量名。
- Return Intermediate Steps：n8n agent 的调试选项，打开后执行记录里能看到每一步的中间数据。

### 七、易错点和存疑点

- Pinecone namespace 名未决：字幕在不同位置给出 context 与 contacts 两种说法，画面文字不可读，工具名也有同样的两说并存——本笔记不裁定，跟做时以自己索引里的实际 namespace 为准。
- 收件人姓氏拼写未证实：字幕多次转录含糊，据语境与页面评论推断为 Dunphy（《摩登家庭》角色梗），画面没有可读拼写，本笔记正文只写 Phil。
- 结尾「一个工具」与实作矛盾：实际建成两个工具（发邮件 + 联系人检索），以实作部分为准。
- 第一次测试失败的原因是讲者的判断（系统提示缺位），画面没有展示错误原文，不要当作已证实的故障结论引用。
- 简介宣称「不写一行代码」，实际用到 JSON.stringify 表达式与手写查询串——按低代码理解，不要按字面理解。

---

## 49. Senior SWE-Bench: Evaluating Coding Agents | Snorkel AI Reading Group

### 一、元信息

- 标题：Senior SWE-Bench: Evaluating Coding Agents | Snorkel AI Reading Group（页面原样）
- 链接：https://www.youtube.com/watch?v=aaZKkcxObIM
- 频道：Snorkel AI（Reading Group 分享）；讲者 Henry Ehrenberg
- 时长：50:20（播放器实测；正讲约到 30:37，其余为问答）
- 发布时间：2026-07-17
- 观看量：573 次；点赞 3
- 配套材料：简介给出基准站点 senior-swe-bench.snorkel.ai 与版本化仓库 snorkel-ai/senior-swe-bench-v2026.06（仓库名经 GitHub 搜索核得、页面链接被截断）；简介提到讲者备有可分享的完整转录
- 核验档：C 档（演讲型：播放器逐段扫 + 简介与仓库存在性案头核查；幻灯片正文多不可读，数字以讲者口述层为准）
- 章节：简介与播放器均无章节；以下分段由字幕内容切分。

### 二、一句话结论与摘要

这场分享讲一个反「刷题式」基准怎么造：100 个任务全部取自真实仓库的新合并请求，题目故意写得像产品经理的需求一样欠明确，评分不用单一测试通过率，而是「行为契约 + 代码库惯例 + 无可辩驳的最佳实践」三层可测面，加上会按每个任务现写验证脚本的验证 agent 与按本仓库品味打分的品味裁判——最后给出的不是「解出来了没」，而是「高质量解出率」。

- 构造：每个任务把约 5 个相关合并请求合成一个工作单元；任务分两类——定位并修复（investigate-and-fix）与按图施工（design-and-build），参考解必须满分否则任务作废。
- 结果（讲者口径）：质量解出率最高约 29.1%（Claude Fable 5），约半数任务在该指标上为零；成本口径要分开看——按产出 token 约 29 美元对约 1 美元（榜首对第五名 Grok 4.5），按总 token 则是 29.37 对 1.23 美元，两个口径不要混引。
- 时效性（截至 2026-11-01）：基准与仓库均为 2026 年内新物，排行榜在站点持续更新；本笔记的排名与分数是讲授时点（2026-07）的快照，引用前应回站点看当前榜。指标命名三层不一致的问题见存疑点。

### 三、技巧总结

- 技巧 1：可测面要从「什么值得测」倒推，而不是从「什么好测」正推。公平测试框架先立原则：只测三类东西——题目明说的行为契约、承重性质的代码库惯例、没有可辩护替代项的最佳实践；题目没指定的实现细节（先写核心还是先写外围）一律不测。我的思考：这一条是整个基准的灵魂——多数基准的争议不在难度而在「测了不该测的东西」，先写下排除规则，比加更多测试更重要；自己做评测时可以照着列一份「明确不测清单」。
- 技巧 2：验证脚本按任务现写，但先过双向校准。验证 agent 读补丁与专家写的测试规格，为每个任务生成定制的参数化验证脚本，最终跑分时脚本里不含任何 LLM 调用；脚本必须先在参考解上全过、在空解上全败，才准用。我的思考：这解决了「固定测试测不了开放任务」的老问题——生成的验证器先被验证，是把不可信组件放进可信流程的标准手法（生成可以智能，准入必须机械）。
- 技巧 3：品味单独设裁判，且标准取自被测代码库自身。质量裁判不问「代码好不好」，而问「它合不合这个仓库自己的惯例」，门槛锚在「优于 D 级水平」（讲者口径）。我的思考：把主观质量锚定到仓库惯例而不是通用审美，既降低了裁判的随意性，也解释了为什么很多「能跑」的补丁拿不到质量分——真实工程里合不合群和能不能跑是两回事。
- 技巧 4：欠明确是特性，不是缺陷。题目故意按真实需求的写法欠 specification，定位与澄清能力本身就是被测对象；另设带引导的简单变体专门用来检验任务是否可解（区分「任务坏了」与「模型不行」）。我的思考：这给评测设计补了一块常被漏掉的对照——没有可解性基线，零分到底是谁的错永远说不清。
- 技巧 5：反作弊要按「见过题」的方向设防。讲者把主要的奖励作弊向量定为找到并照抄原始补丁来源，对策包括限制联网、封堵包注册表外传通道；并报告新模型已表现出约三倍于旧水平的「基准意识」。我的思考：评测污染在 agent 时代从「训练集见过」升级成「运行时能搜到」——防线必须落在执行环境（网络与出口管控）上，而不是指望题目保密。

### 四、详细笔记（按段）

- 0:00–5:55 动机与构造：50 个公开任务加 50 个私有任务（防污染），来自 2026-02 之后合并的真实请求、覆盖 12 个生产仓库；作者多是仓库核心维护者（100+ 提交）；单机多 PR 合题（约 5 个）意在放大单题工作量与上下文规模。
- 5:55–10:39 欠明确与两类任务：定位修复类给的是带误导成分的部分信息、要先查后改；按图施工类给明确规格、照着建。两类都要求产出经得起维护者审查的成果，而不只是测试变绿。
- 10:39–17:42 三层可测面（如技巧 1）与四路评分：预先写定的验证器、验证 agent（流程见技巧 2）、任务定制量规的 LLM 裁判、品味裁判（见技巧 3），四路合成最终质量指标。
- 17:42–22:48 结果与成本：质量解出率榜首 29.1%、约半数任务为零；中间名次与成本数字按两种 token 口径分别给出（见摘要），GPT-5.6 Sol 以约榜首十分之一的产出 token 量排到约第三，Opus 4.8 单题约 8 美元居中。
- 22:48–30:37 污染与作弊：基准意识约三倍、主要向量是运行时找到原始来源、对策是网络与出口管控（见技巧 5）；正讲到此结束。
- 30:37–50:20 问答（信息密度最高的一段，逐项摘录）：不许为各家模型定制 harness；参考解不满分则任务无效；验证 agent 默认用 Sonnet 4.6 并刻意避开与求解方同厂以防偏袒；验证开销中位数——时间口径 6–20%（中位 11%，较早数据）对 token 口径中位 6%（较新数据），引用须带口径；求解时限 6 小时、验证 10–15 分钟；比较新旧模型时两边上下文都用摘要类工具、不给符号级检索；已测的最新开放权重模型为 GLM-5.2。

### 五、原样保留区

本条为演讲型视频，幻灯片正文多不可读，无代码原样可存。口述关键数字集中存此，均标注为讲者口径：质量解出率榜首 29.1%（Claude Fable 5）；成本两口径——产出 token：榜首约 29 美元、第五 Grok 4.5 约 1 美元（其分 17.2，字幕层）；总 token：29.37 美元对 1.23 美元；验证开销时间中位 11%、token 中位 6%；求解时限 6 小时。仓库 senior-swe-bench-v2026.06（57 stars）与站点存在性已核（2026-11-01），榜单活数据未逐项外查。

### 六、术语表

- 质量解出率（quality / tasteful solve rate）：本基准的主指标——解出且通过品味门槛的任务比例，两层叫法见存疑点。
- 验证 agent（validation agent）：按任务把专家测试规格转写成可执行验证脚本的组件，脚本经双向校准后脱离 LLM 独立跑分。
- 品味裁判（taste judge）：按被测代码库自身惯例给补丁质量打分的裁判。
- 基准意识（benchmark awareness）：模型意识到自己在被评测并据此调整行为的倾向，本条报告新模型约为旧水平三倍（讲者口径）。

### 七、易错点和存疑点

- 主指标命名三层不一致：简介写「tasteful solve rate」，口述主讲「high quality solve metric」，29.1% 同一数字两处对应——引用时写明是同一指标的两种叫法，不要当成两个指标。
- 成本两口径严禁混引：29 美元是产出 token 口径、29.37 是总 token 口径，Grok 侧 1 美元与 1.23 美元同理。
- 幻灯片正文大多不可读，本条全部数字为口述层；30:37 后字幕在三处被截断，附近细节未补。
- 验证开销的百分比出自前后两批数据（时间中位 11% 是较早口径），不是同一实验的两个数。

---

## 50. Langfuse GitHub Explained: Self-Hosted LLM Observability with OpenAI Tracing

### 一、元信息

- 标题：Langfuse GitHub Explained: Self-Hosted LLM Observability with OpenAI Tracing（页面原样）
- 链接：https://www.youtube.com/watch?v=0MwQS9G2zvs
- 频道：Alex Hitt
- 时长：6:41（播放器实测）
- 发布时间：2026-10-22
- 观看量：257 次；点赞 2
- 配套代码：简介只给了官方仓库 langfuse/langfuse 一个链接
- 核验档：B 档降格说明——页面标注本内容为 AI 生成（声音或影像经变造或完全由 AI 生成），全片画面是动画素描而非真实终端与界面录像，没有任何真实日志、仪表盘或密钥值可读。本条按「口述流程可作索引、画面不作证据」处理，其关键事实改由官方仓库案头核查（见二、五），证据等级与第 45 条官方实录不可同日而语，引用时须带此说明。
- 章节（简介，共 10）：0:00 Why LLM observability outgrows traditional APM；0:57 Cryptographic secrets for a safe local deployment；1:59 Docker Compose and the six-container stack；3:23 First login, project keys, and OpenAI connection；4:06 Langfuse credentials and Python instrumentation；4:41 Decorators, spans, and async telemetry；5:20 Flush short-lived scripts before exit；5:40 Read traces, tokens, and inferred cost；6:13 Kubernetes and UTC production requirements；6:30 What the self-hosted setup delivers

### 二、一句话结论与摘要

这条 6 分 41 秒的短片把自托管 Langfuse 的部署与接线流程完整走了一遍口播：三个密钥、六个容器、首个注册用户即管理员、换一行导入给 OpenAI 客户端加追踪、短命脚本退出前必须显式 flush、生产环境两个数据库都要锁 UTC。流程本身与官方现行 compose 逐项对得上（案头核查），但画面给的示例密钥恰好违反它自己刚立的规则——这条最大的价值是当部署检查清单用，画面一个字都不能当证据。

- 案头核查（2026-11-01）：口述独有的「Langfuse version 4」属实，官方最新正式版为 v4.72.0（2026-10-29 发布）；官方现行 docker-compose.yml 的服务正是 web、worker、postgres、redis、minio、clickhouse 六个，与口述逐项一致。
- 与第 45 条（官方功能全景）互补：45 讲云端功能面，这条讲自托管的运维面——密钥、容器、时区、扩容路径（Kubernetes + 官方 Helm 图表）。
- 时效性（截至 2026-11-01）：Langfuse 在 v4 线高频发版，具体小版本以官方仓库为准；自托管架构（六服务）与 SDK 版本是两条线，Python SDK 现行 5.1.0（第 45 条案头核查），不要与平台 v4 混淆。

### 三、技巧总结

- 技巧 1：密钥三件套各有死法，先懂死法再生成。NEXTAUTH_SECRET 与 SALT 各 32 字节（openssl rand -base64 32），ENCRYPTION_KEY 必须是整整 64 位十六进制（openssl rand -hex 32）：长度差一位应用容器直接崩，事后丢了它则库里存着的所有外部凭据永久不可读。我的思考：自托管系统里最贵的操作错误都在「第一次填 .env」时发生且不可逆，这三个变量值得逐个理解作用再下手，而不是从任何教程里抄示例值——本片画面里的示例值恰好不合格（见存疑点），是最好的反面教材。
- 技巧 2：NEXTAUTH_URL 必须与浏览器实际访问的域名加协议一字不差。少了 http 前缀不会报错，只会把人困在登录重定向循环里。我的思考：这类「静默不一致」故障最耗时间，因为系统没有任何报错指向真因；自托管排障先查「配置里的自己」与「浏览器眼里的自己」是否同一个地址。
- 技巧 3：接追踪的第一步是换导入，不要重写业务。把 OpenAI 的导入换成 Langfuse 的包装版（from langfuse.openai import OpenAI），调用被拦截、自动补 token 元数据并成 span，业务代码不动；多步管线再在主函数上加 @observe 生成层级 trace。我的思考：侵入度从低到高的顺序是对的——先包装客户端（零改动得基础追踪），再装饰关键函数（得结构），最后才考虑手写 span；与第 45 条官方片的接入方式同源。
- 技巧 4：异步上报的代价是短命进程必须显式 flush。SDK 在后台线程批量发送、不拖慢主程序，但脚本跑完即退时队列还没发完、数据就丢了，结尾调用 flush 是这类程序的固定收尾动作。我的思考：这是所有异步埋点的通用坑，批处理脚本与定时任务尤其常见——评测脚本（第 43、44 条那种）跑完查不到 trace 时，先怀疑这个。
- 技巧 5：生产化两件套——Helm 上 K8s、时区锁 UTC。PostgreSQL 与 ClickHouse 都跑在非 UTC 时，遥测时间戳会漂移。我的思考：时间戳漂移不报错、只污染数据，且事后无法修正；自托管观测系统上线前把时区写进部署清单，比上线后对不上时间线再查要便宜得多。

### 四、详细笔记（按章节）

- 0:00 为什么传统 APM 不够：传统监控为可预测的端点与结构化数据而设，LLM 管线的大载荷多模态输入与动态 token 消耗它看不见；自托管的卖点是提示数据不出自己的机器。（此段的因果方向以语义为准：字幕有一处把「看不见」转录反了。）
- 0:57 密钥：三件套的生成命令与作用见技巧 1；对称加密保护库中存着的外部 API 凭据。
- 1:59 六容器：克隆官方仓库、写 .env、docker compose up -d 起全套；六个容器的分工——web 管 UI 与 API、worker 跑评测、PostgreSQL 存关系状态、Redis 做 web 与 worker 之间的消息代理、MinIO 存原始遥测大对象、ClickHouse 聚合指标出仪表盘。两个启动期警告：ClickHouse 初始化慢，数据库没就绪就催会撞上脏迁移错误陷入崩溃循环，要看 web 容器日志等健康信号；NEXTAUTH_URL 的规则见技巧 2。
- 3:23 首登与建项目：浏览器开 localhost:3000 注册，口述称首个注册用户自动获得完整管理员权限（画面无可读证据，见存疑点）；建项目后在项目设置里生成 API 密钥，要抄下三样：public key、secret key、host 地址。
- 4:06 接线：pip install langfuse openai（画面可读）；三样 Langfuse 凭据加 OpenAI 密钥进环境变量；换导入一行完成基础追踪（见技巧 3）。
- 4:41 装饰器与异步：@observe 标在主函数上生成层级 trace、自动记录入参、出参、总延迟；上报走后台线程批量异步。
- 5:20 flush：短命脚本退出前显式 flush（见技巧 4）；画面只有示意，没有可读的 flush 代码行。
- 5:40 读 trace：刷新项目仪表盘即可看到执行路径树、提示原文、token 消耗与按模型推知的成本——但本片的仪表盘画面不可读，这些是口述的功能清单，不是本片的演示证据（对照第 45 条官方实录）。
- 6:13 生产：官方 Helm 图表上 Kubernetes；两个数据库锁 UTC（见技巧 5）。无任何 chart 名或配置值可读。
- 6:30 收束：宣称得到一套跑在自己硬件上的企业级监控环境；本段无新操作。

### 五、原样保留区

画面可读内容仅三处，照录：终端一行 pip install langfuse openai；装饰器片段 @observe() 加在 generative 函数定义处、其输入框示例为 {"prompt": "Hello world"}；main.py 里三行环境变量赋值（LANGFUSE_PUBLIC_KEY、LANGFUSE_SECRET_KEY、OPENAI_API_KEY，值均为省略号）。画面 .env 里的三个示例密钥值为动画示意且不合格（见存疑点），本笔记不予转录。
案头核查（2026-11-01）：官方仓库 langfuse/langfuse 现行 docker-compose.yml 服务列表为 langfuse-web、langfuse-worker、postgres、redis、minio、clickhouse，与口述六容器逐项一致；最新正式版 v4.72.0（2026-10-29）。

### 六、术语表

- ENCRYPTION_KEY：Langfuse 自托管用来加密库内外部凭据的对称密钥，必须 64 位十六进制，丢失即数据不可读。
- NEXTAUTH_URL：部署的对外访问地址，须与浏览器实际地址（含协议）完全一致，否则登录陷入重定向循环。
- @observe：Langfuse Python SDK 的装饰器，把被装饰函数的一次执行记为一个可嵌套的 trace/span。
- flush：把 SDK 后台队列里待发的遥测立即送出的显式调用，短命进程退出前的必备收尾。

### 七、易错点和存疑点

- 画面示例密钥不合格：.env 示例里的 ENCRYPTION_KEY 是混合大小写的字母数字串且明显不足 64 位——照抄必然启动失败；示例值只可看变量名，不可看值。
- 口述自相矛盾一处：称整个搭建「五分钟以内」，本片片长 6:41，实际动手只会更久，按清单预留时间。
- 三凭据与画面对不上：口述要抄 public key、secret key、host 三样，画面 main.py 只有前两样加 OpenAI 密钥一行，没有 host 变量行（可能用了默认地址，片中未解释）。
- 口述一处把缓冲层说成「S3 存储」，架构清单里是 MinIO（S3 兼容、不是同一个名字），引用时按 MinIO。
- 「首个用户即管理员」只有口述、画面无证据；「version 4」只有口述，但已由案头核查证实（v4.72.0）。
- 本片全程无真实终端输出、真实仪表盘读数或真实成本数字：凡引用本条得出的「看得到什么」，都应改引第 45 条官方片为据。

---

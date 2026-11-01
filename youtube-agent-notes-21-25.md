# YouTube Agent 技巧视频笔记 · 第 21–25 条

> 核验型笔记第二批之第三组。取材标准：字幕、时间戳、代码原文三样齐全；播放器逐章核验，配套仓库克隆对照，能跑的代码实际跑过；看不清处标「不可读」，推断与原文分开标注。

## 目录

- [21. How the harness actually works ?](#21-how-the-harness-actually-works-)
- [22. Building a Coding Agent](#22-building-a-coding-agent)
- [23. claude managed agents explained by example](#23-claude-managed-agents-explained-by-example)
- [24. I Built a Deep Research AI Agent with LangGraph](#24-i-built-a-deep-research-ai-agent-with-langgraph)
- [25. Make Your AI Agent Search Documents | RAG Part 2](#25-make-your-ai-agent-search-documents--rag-part-2)

---

## 21. How the harness actually works ?

### 一、元信息

- 标题：How the harness actually works ?（页面实际标题）
- 链接：https://www.youtube.com/watch?v=AFkYlg4347c
- 作者：Hilay Trivedi（23 订阅）
- 时长：29:59
- 发布时间：2026-10-27
- 观看量：683 次
- 配套仓库：https://github.com/HILAYTRIVEDI/harness-demo （v1–v6 每版一个分支，已克隆核对并做语法校验）
- 章节（简介原样）：00:00 What is a harness? The car analogy；01:35 V1 The engine: one LLM call；04:14 V2 The chassis: the agent loop；05:57 V3 The wheels: tools；12:58 V4 Steering and GPS: system prompt and streaming；18:55 V5 Brakes and saved places: permissions and memory；27:01 V6 The fuel gauge: context window and auto-compact；29:36 What's next

### 二、一句话结论与摘要

用一辆车的比喻，从零手写一个 Claude Code 式 harness：六个版本，每版只加一个零件——单次调用、循环、工具、system prompt 与流式、权限与记忆、上下文压缩。

- 作者的核心定义：harness 不是模型（发动机），而是「发动机 plus everything that makes it go somewhere」——让模型能干活的全部外围。
- 全部代码是 plain Python、无框架（Anthropic SDK 不算框架，按作者口径），每个版本解决上一版暴露出的一个具体问题，版本切换就是 git checkout 一个分支。
- 最有分量的两版是 V5（危险工具要过权限确认；记忆就是一个 Markdown 文件、每次调用全量重发所以必须设上限）和 V6（上下文超阈值就让模型给自己写摘要替换历史）。

### 三、技巧总结

- 技巧 1：按「零件」拆解 harness。汽车映射：engine = LLM；chassis = agent loop；wheels = tools；steering and GPS = system prompt；brakes = 权限护栏；fuel = context window；dashboard = UX；add-ons = skills 与 MCP tools（没有也能跑）。我的思考：这套映射的价值在排序——它天然给出搭建顺序：先能动（loop），再能干活（tools），再有方向（prompt），最后才谈刹车（permissions）与油表（compact）。很多自研 agent 一上来先做记忆和压缩，等于先装油表再装发动机。
- 技巧 2：agent loop 的本质是「无状态模型的重发」。V2 的关键认知：LLM 是 stateless，所以每一轮都要把 system prompt 与全部 messages 重新发一遍；messages 数组就是会话的唯一真相。V3 的 agent_turn 把这个循环泛化：发请求 → 追加响应 → 看 stop reason，不是 tool use 就收尾，是 tool use 就逐个执行、把 tool results 以 user 角色回填、再循环。我的思考：与第 22 条 TypeScript 版对照，两个独立作者写出的循环结构一模一样——这就是这个抽象的「标准形」。
- 技巧 3：记忆就是文件，且要为「每次重发」付费意识。V5 的 memory.py 全文在保留区：记忆存 MEMORY.md，启动时读进 system prompt；上限 MAX_MEMORY_CHARS = 10_000，代码注释把理由写死了——"memory is resent on every call, so keep it small"，超限从头截断并加标记。remember 工具本身被列为危险工具，理由是它可能引入 prompt injection 类风险，写入前也要过权限确认。我的思考：这是本批视频里对「记忆税」最直白的一次工程表述——记忆不是免费的，它的成本按「每轮重发」计。截断策略是最粗暴的一种（从头保留、尾部丢弃），但作者至少把上限写成了常量并注释了理由。
- 技巧 4：权限流的最小形态。危险工具（一切 modifying operations + remember）执行前先查 always allowed 集合：命中则直行（作者比作 Claude Code 的 auto mode）；未命中则 preview 要做的事并问用户——答「a」把该工具加入 always allowed（永久放行该类），答 yes 放行本次，答 no 则回一条「user declined the tool call, ask them how to proceed」让模型改道。另有 workspace 限定（工具只能在工作区内增改删）、bash 超时、输出截断、MAX_TURNS 防死循环。我的思考：这与第 16 条的 hook 拦截是同一层的两种实现——一个在 harness 内部（本条），一个在宿主外部（hook）。自研 harness 只能靠前者；用现成宿主时优先后者，因为模型绕不过去。
- 技巧 5：auto-compact 就是一次带专用提示词的 LLM 调用。V6 在上下文 token 数超过阈值时触发 compact：用 COMPACT_PROMPT（全文在保留区）让模型把会话写成摘要——明令 "Do not call any tools"，摘要必须包含用户的要求与约束、已做之事、当前状态与未完成项，然后摘要替换原始消息继续。仓库数值（视频未给，见存疑点）：阈值 COMPACT_AT = 150000 tokens。我的思考：这解释了第 11 条里「渐进摘要会丢硬事实」的机制根源——摘要质量全押在这一段提示词上。作者的提示词把「约束」与「未完成项」单列，恰好是对丢事实的针对性防御。

- 时效性（截至 2026-11-01）：发布于 2026-10-27，很新；内容是教学型基础实现，不绑定具体模型版本。仓库六个分支与视频一一对应，代码可直接对照学习；运行需要自己的 API key，本笔记只做了静态核对与语法校验，未实跑（无 key）。

### 四、详细笔记（按章节/版本）

- V1（01:35）单次调用：config.py 管 .env、API key、model 与共享 client；harness.py 一个函数：取输入 → client.messages.create → 取 text block 返回。问题：一问即终止。
- V2（04:14）循环：while true，exit/quit 退出；每轮 append user → 调用 → append assistant。问题：不能自己干活，也没有 persona。
- V3（05:57）工具：工具 = 实际函数 + 给模型看的 schema；清单为 list_files / read_file / write_file / edit_file / run_bash（仓库确认名；subprocess 执行，用于格式化、lint、检查脚本）；dispatcher 是 name → 函数的字典。演示：list files 用默认路径列出目录。问题：没有行为模式。
- V4（12:58）system prompt 与流式：build_system_prompt 用 .format 注入 cwd、os_name、date 三个动态值；调用改为 streaming；call model 抽成单一职责函数。问题：重启即忘——演示中问名字，模型答不出。
- V5（18:55）权限与记忆：见技巧 3、4；演示 remember 存下用户名（先经许可），重启后问 what's my name 能答上。
- V6（27:01）油表：跟踪上下文 token（stats）；超阈值触发 compact；另加 slash 命令 /tokens、/help（仓库另有 /compact、/clear、/save、/exit）。

### 五、原样保留区（逐字；标注了来源层级）

memory.py（视频画面与仓库 v6 分支一致，已互校）：

```python
MAX_MEMORY_CHARS = 10_000  # memory is resent on every call, so keep it small

HEADER = "# Agent memory\n\nFacts the agent saved for future sessions. Edit freely.\n\n"

def load_memory():
    """Return the memory file's text, or '' if nothing has been saved yet."""
    if not MEMORY_FILE.exists():
        return ""
    text = MEMORY_FILE.read_text().strip()
    if len(text) > MAX_MEMORY_CHARS:
        text = text[:MAX_MEMORY_CHARS] + "\n... [memory truncated]"
    return text
```

COMPACT_PROMPT 与 build_system_prompt（画面逐字；画面中被截断的行尾经仓库补全，见存疑点）：

```python
COMPACT_PROMPT = """Our conversation is getting long, so it will be replaced by a summary
you write now. Do not call any tools. Write a summary that lets you continue the work \
without the original messages. Include:
- what the user asked for, and any preferences or constraints they gave
- what has been done so far: files read, created or changed, commands run and their results
- the current state, and anything still unfinished or planned next
"""

def build_system_prompt():
    prompt = SYSTEM_PROMPT.format(
        cwd=os.getcwd(),
        os_name=f"{platform.system()} {platform.release()}",
        date=datetime.date.today().isoformat(),
    )
    memory = load_memory()
```

仓库补齐的常量（视频未给出数值，来源为 v6 分支源码）：

```python
MAX_TOKENS = 64000
MAX_TURNS = 25
COMPACT_AT = 150000  # compact once the context passes this many tokens
BASH_TIMEOUT = 60          # seconds
MAX_OUTPUT_CHARS = 20_000  # roughly 5k tokens
```

config.py 开头（画面逐字）：

```python
"""
Config: which API and model the harness talks to.

Settings come from environment variables. For convenience they can also live
in a .env file next to this one (copy .env.example to .env and fill it in).
"""
```

### 六、术语表

- harness：模型之外让 agent 能干活的全部外围（循环、工具、提示、权限、记忆、上下文管理）；本条用汽车作全程比喻。
- agent_turn：处理一轮「模型响应 → 工具执行 → 结果回填」的函数，是 loop 的内层。
- always allowed：权限流里被用户永久放行的危险工具集合。
- compact：上下文超阈值时，用专用提示词让模型写摘要并以摘要替换历史的操作。

### 七、易错点和存疑点

- 页面实际标题是 "How the harness actually works ?"，与选集时的暂名不同，已按页面订正。
- 记忆文件名以仓库为准：MEMORY.md（全大写，Path.cwd() / "MEMORY.md"）；视频口述作 memory.md。
- COMPACT_PROMPT 第三条行尾 "their resul…" 在画面被截断，仓库原文为 "commands run and their results"，笔记已按仓库补全并在此标注。
- 视频未给出的数值（MAX_TURNS、COMPACT_AT、BASH_TIMEOUT、MAX_OUTPUT_CHARS、MAX_TOKENS）全部来自仓库 v6 分支，引用时须注明来源是仓库而非视频。
- 视频把 API key 提供方的品牌拼写讲得含糊（转录作 aiML、听感近 AIML），画面不可读，本笔记不作品牌断言；config.py 以环境变量读 key 与 model 为准。
- V5 里限定工具活动范围的路径概念，其变量名拼写画面不可读，只记功能（workspace 限定），不写变量名。

---

## 22. Building a Coding Agent

### 一、元信息

- 标题：Building a Coding Agent
- 链接：https://www.youtube.com/watch?v=4APoINtFWYs
- 作者：Code with Andrew Ford（735 订阅）
- 时长：29:28
- 发布时间：2026-10-25
- 观看量：474 次
- 配套仓库：https://github.com/andrewjamesford/build-a-coding-agent （已克隆并实跑测试，见存疑点）
- 章节（简介原样）：0:00 Intro；1:05 The agent loop；1:22 Project setup；2:31 Step 1: The chat loop；8:25 Step 2: Defining tools；12:44 Step 3: read_file tool；16:20 Step 4: list_files tool；18:07 Step 5: edit_file tool；21:22 Step 6: bash tool；25:59 Fixing a failing test with our agent

### 二、一句话结论与摘要

325 行 TypeScript 写完一个能改 bug 的命令行 coding agent——它的教学价值在于把每个安全细节都写成了可见的代码：错误回给模型、bash 每条命令先问人、超时杀整个进程组、长输出留尾不留头。

- 结构与第 21 条的 Python 版互为镜像：conversation 数组即全部记忆，工具是「发给模型的三件套 + 只在本地跑的 run 函数」。
- 作者在简介里先声明定位：这是学习项目，不是 Claude Code / Codex CLI 的替代品，「which have far more safety checks」。
- 结尾演示完整走通一次真实修复：定位 calculateTotal 漏乘 quantity，edit_file 改一行，跑测试通过（演示口径，见存疑点）。

### 三、技巧总结

- 技巧 1：工具的定义是「接口与实现分离」。ToolDefinition 接口四要素：name、description、inputSchema、run；发给 Claude 的只有前三者，run 的代码 "never leaves our machine"。加工具 = 往数组里加一项，loop 一行不改（list_files 就是这样加进去的）。我的思考：这条与第 21 条的 dispatcher 字典是同一设计的两种方言。接口先行的好处在第 18 条 Anthropic 的「工具对应 UI」那里得到呼应：schema 是给模型看的 UI，run 是藏在后面的实现。
- 技巧 2：把错误当作给模型的反馈通道。executeTool 捕获一切异常，转成 is_error: true 的 tool_result 回填；工具找不到也回 "tool not found" 而不是崩进程。简介把这条列为学习点原话："returning errors to the model lets it correct itself and try again"。我的思考：这是手写 agent 与玩具 demo 的分水岭——玩具把错误抛给用户，agent 把错误抛给模型，因为下一步的决策者是模型。
- 技巧 3：bash 工具的安全三件套 + 进程组杀停。常量：BASH_TIMEOUT_MS = 30_000、MAX_OUTPUT_CHARS = 4_000；每条命令先经 confirm 回调问人（提示 "Run it? [y/N]"，严格只认恰好 "y"）；spawn 时 stdio 的 stdin 设为 "ignore"（注释：no stdin, so nothing can wait for input），detached: true 让子进程自成进程组，超时时 process.kill(-child.pid, "SIGKILL") 杀整组——注释解释了为什么：只杀 bash，它的孙子进程（如 npm run dev）会继续占着输出管道。桌面核查：仓库自带测试里恰有 "a timeout also stops processes the command started" 一条，本笔记以 SLOW=1 实跑，22/22 全通过，这条不在跳过之列。
- 技巧 4：长输出截断要留尾巴。truncate 保留**末尾** 4,000 字符、切掉头部，前缀标明切了多少字符；注释原话："Keep the end of long output: that's where errors and test summaries are." 我的思考：与第 21 条形成对照——那边记忆截断留头（从开头截），这边输出截断留尾。截哪头取决于信息分布：记忆是越早越基础，命令输出是越晚越接近结论。
- 技巧 5：循环里的 readUserInput 开关。没有工具请求时才回键盘读用户输入；有 tool_use 就连轴转：执行、回填、立即再问模型。这个小布尔量就是「agent 自主多步」与「聊天机器人」的分界线。演示里修 bug 全程（读三文件、改一行、跑测试）用户只敲了两次 y（删文件一次、跑测试一次）。

- 时效性（截至 2026-11-01）：发布于 2026-10-25。代码用 @anthropic-ai/sdk 且不限版本，模型常量为 "claude-opus-5-5"（画面与仓库一致，如此记录，其可用性以读者自己的账户为准）；Node 演示版本 v26.12.1，本笔记在 Node v24.22.1 上复跑仓库测试全通过，说明实现不依赖 26 的新特性。作为教学实现，它的价值不随版本过期；但作者自己提醒日常使用应选 Claude Code / Codex CLI。

### 四、详细笔记（按章节）

- 1:22 环境：npm init -y && npm pkg set type=module；装 @anthropic-ai/sdk 与 -D tsx typescript @types/node（均无版本限定）；.env 只放 ANTHROPIC_API_KEY，其加载方式片中未展示（见存疑点）。
- 2:31 聊天循环：Agent 类三依赖（client、getUserMessage、tools）；runInference 早期形态只有 model/max_tokens: 16000/messages；stop_reason 为 refusal 时打印 (declined to answer) 跳过本轮。
- 8:25 工具框架：ToolDefinition 接口与 executeTool（见保留区与技巧 2）；runInference 增加 tools 映射（name/description/input_schema）。
- 12:44–18:07 三个文件工具：read_file（path 必填）；list_files（递归、目录带 / 后缀、排序后 JSON 输出）；edit_file（见技巧与存疑点的描述/实现出入）。
- 21:22 bash 工具：makeBashTool(confirm) 工厂函数——因为 bash 工具需要「问人」的能力，把确认回调注入进去，而不是让工具自己读键盘。
- 25:59 实战演示：先演示危险面（agent 提议 rm notes.txt && ls，用户按 y 后真的删了）；再修测试：read total.js、total.test.js、package.json → edit_file 改 reduce 一行 → bash 跑 npm test 2>&1 | tail -15 → 回复称两测试全过；agent 还自发指出缺 quantity 字段会算出 NaN 的隐患（其建议句后半被进度条遮挡，未录全）。

### 五、原样保留区（逐字；已经仓库互校）

工具接口与错误回填：

```typescript
export interface ToolDefinition {
  name: string;
  description: string;
  inputSchema: Anthropic.Tool.InputSchema;
  run: (input: Record<string, unknown>) => Promise<string>;
}

private async executeTool(id: string, name: string, input: unknown): Promise<ToolResult> {
  const tool = this.tools.find((t) => t.name === name);
  if (!tool) {
    return { type: "tool_result", tool_use_id: id, content: "tool not found", is_error: true };
  }
  try {
    const output = await tool.run(input as Record<string, unknown>);
    return { type: "tool_result", tool_use_id: id, content: output };
  } catch (err) {
    const msg = err instanceof Error ? err.message : String(err);
    return { type: "tool_result", tool_use_id: id, content: msg, is_error: true };
  }
}
```

edit_file 的核心逻辑：

```typescript
if (filePath === "" || oldStr === newStr) {
  throw new Error("Invalid input parameters");
}
let original: string;
try {
  original = await readFile(filePath, "utf8");
} catch (err) {
  const missing = (err as NodeJS.ErrnoException).code === "ENOENT";
  if (missing && oldStr === "") return createNewFile(filePath, newStr);
  throw err;
}
// An empty old_str would match between every character of the file.
if (oldStr === "" && original !== "") {
  throw new Error("File already exists, so old_str can't be empty");
}
// Like Go's strings.Replace(..., -1), this replaces every match.
const updated = original.replaceAll(oldStr, newStr);
if (updated === original) {
  throw new Error("old_str not found in file");
}
await writeFile(filePath, updated, "utf8");
return "OK";
```

bash 工具的关键段（进程组杀停与留尾截断）：

```typescript
const BASH_TIMEOUT_MS = 30_000;
const MAX_OUTPUT_CHARS = 4_000;

const child = spawn("bash", ["-c", command], {
  cwd: process.cwd(),
  stdio: ["ignore", "pipe", "pipe"], // no stdin, so nothing can wait for input
  detached: true, // own process group, so a timeout can stop everything it started
});
const timer = setTimeout(() => {
  timedOut = true;
  // Kill the whole group: a child like `npm run dev` would keep running
  // and hold the output pipes open if only bash were killed.
  process.kill(-child.pid, "SIGKILL");
}, BASH_TIMEOUT_MS);

function truncate(text: string): string {
  if (text.length <= MAX_OUTPUT_CHARS) return text;
  const dropped = text.length - MAX_OUTPUT_CHARS;
  return `[${dropped} earlier characters cut]\n${text.slice(-MAX_OUTPUT_CHARS)}`;
}
```

演示修复（agent 的解释原文）：

```
**The problem:** `calculateTotal` added up each item's `price` but ignored its `quantity`. For the test basket it returned 3 + 6 = 9 instead of the expected (3 × 2) + (6 × 1) = 12.
**The fix:** each item's price is now multiplied by its quantity:
export function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}
```

### 六、术语表

- ToolDefinition：本视频自定的工具接口（name/description/inputSchema/run），run 只在本地执行。
- tool_result / is_error：把工具输出或错误回填给模型的消息块；is_error 标记失败以便模型重试。
- detached（进程组）：让子进程自成进程组，超时可按组整体杀停，避免孙子进程残留。

### 七、易错点和存疑点

- **edit_file 的描述与实现不一致（本条最值得记的坑）**：inputSchema 里 old_str 的描述写 "Exact text to find. It should appear exactly once in the file."，但实现用 replaceAll 替换**所有**匹配处，且不校验出现次数——模型按「唯一替换」理解、代码按「全局替换」执行。引用这份代码时必须连这条一起说。
- 行数口径已核：仓库 main.ts 135 行 + tools.ts 190 行 = 325 行，口述 "less than 330 lines of code" 属实。
- 仓库测试实跑（本机 Node v24.22.1）：默认 npm test 为 20 通过、2 跳过；跳过的两条正是 30 秒超时测试（需 SLOW=1）；加 SLOW=1 后 22/22 全通过，typecheck 无报错。注意这 22 条是工具自身的测试，与演示里 total.js 的「两个测试」是两回事，不要混。
- 演示里「both tests now pass」只有终端中 Claude 的回复文本为据，npm test 的原始通过行未完整入镜，按 agent 说法记录。
- .env 如何被加载片中未展示：main.ts 顶部无 dotenv 导入，只见注释说 key 从环境读取；不要替作者补上加载方式。
- read_file 的 fs 导入行未在画面完整出现（仓库可查，不影响结论）。

---

## 23. claude managed agents explained by example

### 一、元信息

- 标题：claude managed agents explained by example（页面标题；片内主标题为 Claude Managed Agents vs Your Own Agent）
- 链接：https://www.youtube.com/watch?v=klhovbwpAKQ
- 作者：zero cost ai（页面显示 1 位订阅者）
- 时长：6:42
- 发布时间：2026-10-20
- 观看量：6 次（观看期间变为 7 次）
- 配套文件：经简介 tinyurl 指向仓库 https://github.com/NEDBS12/youtube-tuto 的 claude-managed-agents 目录（已克隆核对并实跑离线测试，见存疑点）
- 章节（简介原样）：0:00 Introduction；0:20 What is an agent?；0:48 The four building blocks；1:17 Who does what；1:47 When to choose each；2:17 Our use case；2:40 Doing it yourself；3:04 Step 1: Define the agent；3:35 Step 2: Create the environment；4:02 Step 3: Start a session；4:22 Step 4: Stream the events；4:46 Step 5: Get the outputs；5:21 Tips；5:59 Recap

### 二、一句话结论与摘要

自建 agent 与 Claude Managed Agents 的分界不在能力，在「谁负责 loop、沙箱与工具」：短任务与强控制选自建，长任务与要跑代码产文件选托管。

- Managed Agents（beta）把四件事拆成四个构建块：agent（模型+system prompt+工具，建一次按 ID 复用）、environment（云沙箱，可设网络规则）、session（一次任务一个）、events（你发消息事件、它流式回传全过程）。
- 演示是一个小店月报：24 条订单 CSV 挂进 session，agent 在沙箱里写 Python 分析、产出 report.md 与 weekly.png，本地脚本等它 idle 后把文件下载下来。
- 硬限制只有一条但很硬：session 存服务器端，所以目前不适用于 zero data retention 与 HIPAA 场景。

### 三、技巧总结

- 技巧 1：选型清单（作者口径，可直接当检查表）。自建：short tasks、需要 custom loop、工具必须跑在你自己的系统里、需要 zero data retention。托管：long tasks（minutes or hours）、要在云端安全跑代码并生成文件、async or scheduled work、不想自建 loop/沙箱/工具。自建要自己补齐的东西作者也列了：bash 工具、安全执行环境、文件工具、能跑几分钟的 loop、历史与重试、文件存放处——托管版就是把这一整串打包。我的思考：这份清单的价值在于它按「责任归属」而不是「功能有无」划分。功能上两者都能做月报，差别全在谁运维。
- 技巧 2：建一次、用多次的资源模型。agent 与 environment 只 create 一次、把 ID 存进 ids.json；每个任务只做：上传数据 → create session（把文件以 mount_path 挂进去）→ 先开事件流再发 user_message → 等 session.status_idle → 按 session 列文件并下载（跳过自己上传的那份）。代码全在保留区。我的思考：这套「定义/环境/会话」三分法与第 20 条 FastMCP 的挂载、第 21 条 harness 的分层是同一种品味：把不变的定义与易变的运行分开。
- 技巧 3：idle 不是一种状态，是三种。作者在 Tips 里要求 handle every idle reason：end_turn 是正常完成；requires_action 是有自定义工具或确认在等你回答；budget_reached 是撞了预算。只处理 end_turn 的客户端会在另外两种情况下永久挂起。我的思考：这是托管形态特有的坑——自建 loop 里「停没停」是你自己的代码说了算，托管后停止原因变成对方给的枚举，必须全量处理。
- 技巧 4：网络默认值是反的。environment 的 networking 不填就是 unrestricted access；本例显式设 limited 并在画面旁注 "No open internet"，因为这个 agent 根本不需要联网。Tips 第二条原话口径："Always set networking." 我的思考：与第 20 条 route maps 的「默认全暴露」是同一个病：便利型默认全开，生产环境要逐项收紧，而且收紧点藏在「不填会怎样」里，不读文档根本发现不了。
- 技巧 5：产出物要等一等。idle 之后 outputs 可能还要数秒才出现（作者称是文档说法），代码里直接 time.sleep(5) 再列文件。演示数字经桌面复算全部属实（见存疑点），但这类「sleep 一下」是托管异步的典型胶水，生产里应换成轮询或重试。

- 时效性（截至 2026-11-01）：发布于 2026-10-20。Managed Agents 仍是 beta：工具集类型字符串 agent_toolset_20260401、beta 标识 managed-agents-2026-04-01 都是时点值，接口与计费随时可能变；仓库 README 明示用量计入 API 账户计费（"check the Claude Console for pricing before running it often"）——**本视频没有给出任何价格，频道名叫 Zero Cost AI 不代表这个 API 免费**。选型框架（谁负责 loop/沙箱/工具）不随版本过期。

### 四、详细笔记（按章节）

- 0:20 定义（口述原话）："An agent is Claude plus tools plus a loop. Claude acts, looks at the result, and acts again until the task is done." 自建就是 you write that loop；托管就是 Anthropic runs the loop。
- 0:48 四个构建块（幻灯片原样）：AGENT "Model, system prompt, tools: created once, reused by ID"；ENVIRONMENT 为 cloud sandbox，可自选 network rules；SESSION "one running agent, doing one task, with its own sandbox and files"；EVENTS "you send messages as events, and the session streams back everything the agent does"。注意前三样（model/system prompt/tools）是 AGENT 块的内部组成，不是并列的块。
- 1:17 谁做什么（自建侧幻灯片原样）：You write the loop / You run every tool, on your machine / You keep the message history / You handle retries and context / Full control of every step。托管侧口述：内置工具（bash, file tools, web search and web fetch）、session 保管 history 与 files、caching and compaction are built in。
- 2:17 用例衔接：上一条视频的 orderbot 是单工具快问答，自建足够；本条月报要写并运行 Python 还要产文件，所以换托管。
- 3:04–4:46 五步实现：见保留区代码。工具集一行 tools=[{"type": "agent_toolset_20260401"}] 给全套内置工具：bash, read, write, edit, glob, grep, web search and web fetch。system prompt 里写死输出目录 /mnt/session/outputs/，下载环节就靠这个约定对上。
- 5:21 Tips 四条：create once reuse；always set networking；handle every idle reason；server-side 存储的合规限制（可随时删 sessions 与 files）。

### 五、原样保留区（逐字；已经仓库互校，模型字符串由仓库补全）

setup_agent.py（仓库原文；视频画面中模型后缀不可读）：

```python
import json
from anthropic import Anthropic

client = Anthropic()

# The agent: model, instructions and tools (created once)
agent = client.beta.agents.create(
    name="Sales Analyst",
    model="claude-sonnet-5-5",
    system=(
        "You are a data analyst for a small online shop. "
        "Analyze the data you get with Python, and save "
        "every output in /mnt/session/outputs/."
    ),
    tools=[{"type": "agent_toolset_20260401"}],
)

# The environment: where sessions run (a cloud sandbox)
environment = client.beta.environments.create(
    name="sales-report-env",
    config={
        "type": "cloud",
        "networking": {"type": "limited"},
    },
)

ids = {"agent": agent.id, "environment": environment.id}
with open("ids.json", "w") as f:
    json.dump(ids, f)
```

run_report.py 三段（画面逐字，与仓库一致）：

```python
# 1. Upload the data, start a session with it mounted
data = client.files.upload(file=Path("orders.csv"))
session = client.beta.sessions.create(
    agent=ids["agent"],
    environment_id=ids["environment"],
    title="October sales report",
    resources=[{"type": "file", "file_id": data.id,
                "mount_path": "/orders.csv"}],
)

# 2. Send the task, then stream what the agent does
with client.beta.sessions.events.stream(session.id) as stream:
    client.beta.sessions.events.send(session.id, events=[
        {"type": "user_message",
         "content": [{"type": "text", "text": TASK}]},
    ])
    for event in stream:
        if event.type == "agent.tool_use":
            print(f"[tool] {event.name}")
        elif event.type == "agent.message":
            for block in event.content:
                if block.type == "text":
                    print(block.text)
        elif event.type == "session.status_idle":
            print("Idle:", event.stop_reason.type)
            break

# 3. Download what the agent saved in outputs/
time.sleep(5)  # outputs can appear a few seconds after idle
files = client.beta.files.list(
    scope_id=session.id,
    betas=["managed-agents-2026-04-01"],
)
for f in files:
    if f.id != data.id:
        client.files.download(f.id).write_to_file(f.filename)
        print("Downloaded", f.filename)
```

运行终端原文：

```
~/sales-report $ python run_report.py
[tool] bash
[tool] write
[tool] bash
[tool] write
Total revenue: $499.80. Best week: Oct 12-18 ($147.45). Top products: Tote bag, Mug, T-shirt.
report.md and weekly.png are saved in outputs.
Idle: end_turn
Downloaded report.md
Downloaded weekly.png
```

### 六、术语表

- Managed Agents：Anthropic 托管运行 loop、内置工具与云沙箱的 agent 形态（视频时点为 beta）。
- environment：会话运行的云沙箱定义，含 networking 等配置，建一次复用。
- session：一次任务的运行实例，有自己的沙箱与文件。
- idle / stop_reason：会话停止工作的事件与原因枚举（end_turn / requires_action / budget_reached）。

### 七、易错点和存疑点

- 页面标题（explained by example）与片内主标题（vs Your Own Agent）不同，同一视频两种呈现，已并列记录。
- 简介没有直链仓库：文件包经 tinyurl 跳转到 NEDBS12/youtube-tuto 的 claude-managed-agents 目录；视频里提到的 offline test（无需 API key 可跑）只在文件包里，视频内未演示。
- 桌面核查三项全过：① 离线测试 test_offline.py 本机以 anthropic==1.15.0 实跑，1 passed（模拟 API、假 key sk-demo-1234）；② orders.csv 独立复算：24 单合计恰为 $499.80，Oct 12–18 区间合计恰为 $147.45，前三产品 Tote bag、Mug、T-shirt 与终端一致；③ 模型字符串 claude-sonnet-5-5 由仓库补全（画面不可读）。
- 1:47 段音频转录漏掉否定词（"is an eligible"），以 Tips 段明确口径为准：isn't eligible for zero data retention or HIPAA today。
- TASK 变量全文画面不可读（只见 "Analyze /mnt/session/uplo…" 开头），其内容以口述四项（total revenue、revenue per week、top three products、a chart）为准，不补写原文。
- 视频未给任何价格数字；$499.80 与 $147.45 是演示销售数据，不是服务价格。

---

## 24. I Built a Deep Research AI Agent with LangGraph

### 一、元信息

- 标题：I Built a Deep Research AI Agent with LangGraph
- 链接：https://www.youtube.com/watch?v=_Nns2bQU5oM
- 作者：MLTut（1.02 万订阅）
- 时长：22:18
- 发布时间：2026-09-07
- 观看量：2,089 次
- 配套：**简介无代码仓库**，只有 3 条 affiliate 课程链接（简介自带佣金披露）；代码原文以播放器画面为准，无仓库对照
- 章节（简介原样）：00:00 What We're Building；02:04 Deep Research Agent Architecture；03:00 Project Setup & Dependencies；05:25 Define the Agent State；06:26 Build the Research Workflow；09:07 Add Web Search with Tavily；10:40 Analyze Search Results；12:10 Build the Research Loop；14:08 Generate the Final Answer；14:53 Connect Everything with LangGraph；17:40 Fix the Graph & Run the Agent；18:32 Complete Agent & Next Steps

### 二、一句话结论与摘要

一个教科书式的 deep research 图：先把问题改写成研究问题、拆计划、生成查询、Tavily 搜索、提取要点，再让模型用 JSON 回答「还要不要再研」——循环的刹车不靠模型自觉，靠硬编码的轮次上限。

- 结构是单向流水线加一个条件回边：research_decision 节点输出 needs_more_research，路由函数据此回 generate_queries 或去 write_answer。
- 决策解析是 fail-closed 向停止的：JSON 解析失败一律当作「不再研究」，宁可少查也不能死循环。
- 作者在「Fix the Graph」一节亲口补上最大轮次 4——因为纯靠模型决定停，模型一直说 yes 就会无限循环烧钱。

### 三、技巧总结

- 技巧 1：先把用户问题「编译」成研究问题。流水线第一步 analyze_request 不直接用原始提问，而是让 LLM 把它改写成单一、明确的 research_question，再 create_plan 拆成独立子问题。示例问题 "Which AI agent frameworks are best for production applications in 2026?" 被拆出的维度包括 production readiness、state management and persistence、tool calling and orchestration、multi-agent support、observability and debugging、deployment and scalability、developer experience and ecosystem。我的思考：这与第 19 条 Archon 的 reasoner 先写 scope 是同一个动作——先把任务变成工件，再执行。区别是那边产出文档、这边产出 state 字段。
- 技巧 2：决策节点要结构化输出 + 解析兜底。research_decision 的提示词要求 "Respond ONLY with JSON"，代码用 json.loads 解析，并把 JSONDecodeError、KeyError、TypeError 一网打尽，任何异常都落到 needs_more = False（代码见保留区）。我的思考：这是免费版的结构化输出纪律——没有 schema 约束时，解析失败的默认值选哪边，决定系统是 fail-open（继续烧钱）还是 fail-closed（提前收尾）。作者选了收尾，方向对。
- 技巧 3：循环必须有与模型无关的硬上限。作者原话口径：现在循环完全依赖模型决定何时停止，若模型一直说 yes 会一直走下去；故加 maximum research rounds，本例为 4，到限直接去 final answer，不再问模型。计数 research_iterations 每次过 decision 加一，初始 state 里置 0。我的思考：这条与第 21 条 harness 的 MAX_TURNS = 25 是同一条纪律的两个化身：凡是「模型决定是否继续」的循环，都要有一道模型管不着的闸。
- 技巧 4：分析节点明令不许编造。analyze_results 把搜索结果的 title/URL/content 格式化后交给 LLM，要求提取 important facts, useful statistics, disadvantages, conclusions, and unanswered questions，并特别指令 "Do not invent information"；结果以 list 形式追加进 findings，供多轮累积；write_answer 只许用 findings 回答，不许依赖模型通用知识。我的思考：findings 是这条流水线的「证据层」——最终答案的可信度上限由它决定。但要注意存疑点里那条：findings 只是字符串列表，来源信息在提取时就丢了，简介宣称的 citations 其实没有落地。
- 技巧 5：图的接线错误是这类代码的第一故障源。作者在 Fix 一节专门提醒：write_answer 必须注册为自己的节点，别把字符串 "write_answer" 误写成另一个节点的第二个参数；从 nodes.py 的导入要全；运行前逐节点检查连线。这类错误不报错在写代码时，只在运行时以奇怪的方式现形。

- 时效性（截至 2026-11-01）：发布于 2026-09-07。安装日志里的版本为 langgraph-1.2.11、tavily-python-0.8.1 等时点值；画面模型字符串为 "gpt-5.6-luna"（temperature=0；作者口述 model name can be replaced，如此记录，其官方含义未核实）。图结构、state 设计与「决策兜底 + 硬上限」这两条纪律不随版本过期。无仓库可对照是本条的证据短板，代码细节以画面为准。

### 四、详细笔记（按章节）

- 3:00 环境：mkdir deep-research-agent、venv、pip install langgraph langchain-openai python-dotenv tavily-python；.env 两键 OPENAI_API_KEY、TAVILY_API_KEY（口述要求进 .gitignore）；文件分工 state.py / nodes.py / graph.py / main.py。四文件分工本身就是一条经验：状态、节点、连线、入口分开改。
- 5:25 State：见保留区；每个节点从 state 读自己要的、只更新自己负责的字段。
- 6:26–12:10 节点逐个：analyze_request（提示词可见部分："Analyze this user request and identify the underlying research question... Then respond with a single, clear research question that captures exactly what needs to be researched."）；create_plan；generate_queries（对 LLM 输出逐行 strip 得 queries）；search_web（逐 query 调 search_api：search_depth="advanced"、max_results=3）；analyze_results；research_decision。
- 14:08 write_answer：只用 findings 作答，按研究维度组织比较。
- 14:53 连图：见保留区 graph 代码；条件边 route_after_decision 读 state["needs_more_research"]。
- 17:40 修复与运行：见技巧 3、5；main.py 以完整初始 state 调 graph.invoke，print(result["final_answer"])；运行结果全文未在画面完整给出，不转述。
- 18:32 后续改进清单（口述）：improve the source evaluation process、add stronger source citations、第三项转录作 source duplication（疑为 deduplication，拼写未确认）、control the maximum number of research iterations、improve the final answer 的比较质量。

### 五、原样保留区（播放器画面逐字；无仓库对照）

state.py：

```python
from typing import Any, Dict, List, TypedDict
class ResearchAgentState(TypedDict):
    user_query: str
    research_question: str
    research_plan: List[str]
    search_queries: List[str]
    search_results: List[Dict[str, Any]]
    findings: List[str]
    needs_more_research: bool
    final_answer: str
```

（后加的 research_iterations 字段也在 state 中，其类型标注在画面被裁切，未读全。）

search 与决策：

```python
def search_api(query: str) -> List[dict]:
    response = tavily_client.search(
        query=query,
        search_depth="advanced",
        max_results=3,
    )
    return response.get("results", [])
```

```python
# research_decision 的提示词（画面逐字）
Research question: {research_question}
Findings:
{chr(10).join(findings)}
Do we have enough evidence to write a thorough answer?
Respond ONLY with JSON:
{"needs_more_research": true, "reason": "..."}
```

```python
    response = llm.invoke(prompt)
    try:
        decision = json.loads(response.content)
        needs_more = bool(decision["needs_more_research"])
    except (json.JSONDecodeError, KeyError, TypeError):
        needs_more = False
    return {
        "needs_more_research": needs_more,
        "research_iterations": iterations
    }
```

graph.py 主体：

```python
def route_after_decision(state: ResearchAgentState) -> str:
    if state["needs_more_research"]:
        return "generate_queries"
    return "write_answer"

builder = StateGraph(ResearchAgentState)
builder.add_node("analyze_request", analyze_request)
builder.add_node("create_plan", create_plan)
builder.add_node("generate_queries", generate_queries)
builder.add_node("search_web", search_web)
builder.add_node("analyze_results", analyze_results)
builder.add_node("research_decision", research_decision)
builder.add_node("write_answer", write_answer)
builder.add_edge(START, "analyze_request")
builder.add_edge("analyze_request", "create_plan")
builder.add_edge("create_plan", "generate_queries")
builder.add_edge("generate_queries", "search_web")
builder.add_edge("search_web", "analyze_results")
builder.add_edge("analyze_results", "research_decision")
builder.add_conditional_edges(
    "research_decision",
    route_after_decision,
    {
        "generate_queries": "generate_queries",
        "write_answer": "write_answer",
    },
)
```

### 六、术语表

- research loop：搜索 → 分析 → 决策 → 再搜索的迭代回路，本条由条件边实现。
- fail-closed（向停止兜底）：解析或判断失败时默认停止迭代，而不是默认继续。
- findings：各轮分析提取的要点列表，是最终答案的唯一证据来源（本条实现里为纯字符串）。

### 七、易错点和存疑点

- **简介宣称与代码不符**：简介学习清单写 "Preserve source information for citations"，但已见代码里 findings 只是 List[str]，没有引用格式、来源评分或去重——按「简介宣称、代码未见」处理，不要当已实现功能引用。
- 简介内部两行架构表述彼此不一致（一行含 Analyze Request/Create Plan 节点名，另一行省略且回路画法不同），以图代码为准。
- research_decision 返回里的 reason 字段只在提示词里要求，代码并未读取使用；iterations 的递增行未在画面读全（口述为每次 +1）。
- graph.py 的导入行与末行边（推测为 write_answer → END）在画面被裁切，未补写；main.py 的 load_dotenv 行同样未读全。
- 模型名 "gpt-5.6-luna" 为画面原样抄录，视频未解释、本笔记未核实其官方含义；作者明说可替换。
- 本条没有配套仓库，全部代码证据来自播放器画面，未做仓库互校与实跑，证据等级低于本组其他条目，引用时请注意。

---

## 25. Make Your AI Agent Search Documents | RAG Part 2

### 一、元信息

- 标题：Make Your AI Agent Search Documents | RAG Part 2: Embedding & Searching | Advanced AI Playlist | MIK（页面标题；简介内 Video Title 多一个 Creating）
- 链接：https://www.youtube.com/watch?v=s1KC4CKx8Ww
- 作者：codestorywithMIK（16.5 万订阅）
- 时长：58:57
- 发布时间：2026-10-28
- 观看量：3,586 次
- 系列定位：Advanced AI Playlist 的 Video-14（上一集 Part 1 讲 embeddings 与 cosine similarity）
- 配套：PDF 笔记 https://github.com/MAZHARMIK/Interview_DS_Algo/tree/master/iPad%20PDF%20Notes/AI%20Advanced%20Playlist%20PDFs ；系列源码 https://github.com/MAZHARMIK/python-ai-agent-ollama （已克隆互校，见存疑点）
- 章节（简介原样）：00:00 Introduction；00:27 Recap；00:59 RAG Big Picture；04:58 Create Fake Knowledge Base；07:13 Loading Documents & What the Data Looks Like；21:32 Creating Embeddings for Each Document；25:49 Building the Search Function；31:30 Testing function；32:22 Demo: The Search Works；45:46 Problem 1: One Embedding Per Document Is Blurry；49:26 Problem 2: Re-Embedding on Every Start；51:30 Problem 3: Too Much Text for the LLM；52:58 Problem 4: The Best Score Always Exists

### 二、一句话结论与摘要

先故意写一个最笨的 RAG：整份文档一个向量、内存里暴力遍历算余弦相似度——然后用一整集把它的四个毛病逐个摊开讲清，这才是本集真正的教学内容。

- 演示系统：7 个 .txt 公司文档，嵌入模型 nomic-embed-text（本地 Ollama），查询向量与全部文档向量逐个算 cosine_similarity，降序取 top_k=3。
- 作者明说本集「agent 本体不动」（Our agent stays untouched in this video），只做知识库一侧；修复（分块、落盘、阈值）全部留给后续视频。
- 四个问题按严重度：整文件单向量太糊、每次启动重嵌入、整文件塞爆上下文、以及最阴的——最高分永远存在，无关问题也会「检索成功」。

### 三、技巧总结

- 技巧 1：先做 naive 版，再让问题自己浮出来。作者的教学结构是先把能跑的最简版写完（load → embed whole doc → brute-force search），再逐条暴露问题。这个顺序在工程上同样成立：没有 naive 基线，你不知道分块、缓存、阈值各自解决了什么。我的思考：与第 12 条 LangChain 的 Select/Compress 对照，本集的四个问题正好是「不做 context engineering」的症状清单。
- 技巧 2：整文件单向量为什么糊（Problem 1）。一份 leave_policy.txt 里同时有 casual、earned、sick、maternity、paternity 五种假期，压进一个向量后，每个主题的信号都被其他主题稀释——作者的词是 blurry。文件越大越严重。解法方向是 chunking：每块一个向量，查询只跟最相关的那块比。
- 技巧 3：嵌入结果必须落盘（Problem 2）。当前实现每次启动都重新 load + 重新 embed 全部文档，又慢又贵；作者的解法是把带向量的 documents 存成文件（如 JSON），启动时先查文件存在就直接加载。我的思考：这是把「计算结果」当缓存的最小实现；再进一步才是增量更新与向量库，但对这个规模，先落盘就解决了九成。
- 技巧 4：检索到的整文件不等于可用的上下文（Problem 3）。演示里 leave_policy.txt 只有约 14 行，把它整份给 LLM 看似无妨；但文件一大就会超 context window、被截断、变慢——尤其在用的还是小模型 qwen3:4b。真正需要的只是其中一段（比如 paternity 那几行）。这再次指向 chunking：送 chunk 不送文件。
- 技巧 5：最高分永远存在，必须设相关性底线（Problem 4，本集最重要的一条）。search 永远返回 top_k，哪怕查询是 "who is my best friend in my office?" 这种与知识库毫无关系的问题——板书示例里最高分只有 0.12、第二名 0.01，程序照样把它们当「最相关」送给 LLM，LLM 拿着毫不相干的上下文只会困惑甚至硬编。作者的解法：加阈值/兜底，最高分不够格就明说「知识库里没有相关信息」。我的思考：这一条在主人自己的研究语境里就是检索的「假阳性」问题——top-k 是相对排序，不是相关性判定；任何 RAG 系统都要单独回答「到底相不相关」，不能让排序冒充判定。

- 时效性（截至 2026-11-01）：发布于 2026-10-28，非常新。内容是 RAG 基础教学，不绑定框架版本；本地栈为 Ollama 兼容端点（.env 里 BASE_URL=http://127.0.0.1:11434/v1、MODEL=qwen3:4b、EMBED_MODEL=nomic-embed-text）。缩略图上的 768-Dim 只出现在缩略图，视频内未以文字确认它与 nomic-embed-text 的对应关系，本笔记不作此断言。四个问题的解法（分块等）在后续视频，本集只有方向。

### 四、详细笔记（按章节）

- 0:59 RAG 全景：以「How many leaves are given for maternity leave?」为例——LLM 不知道公司内部信息；把查询与文档都转成 embedding，用查询向量取回相似内容作额外上下文，再连同问题交给 LLM。本集只聚焦知识库一侧。
- 4:58 假知识库：虚构公司 Zorvex Technologies，knowledge_base/ 下 7 个 .txt：hiring_process.txt、hr_docs.txt、leave_policy.txt、medical_policy.txt、rca_docs.txt、tech_docs.txt、travel_expense.txt（仓库已核，清单一致）。
- 7:13 加载：文档在程序里是 {"source": 文件名, "text": 全文}；讲者顺带讲了 \n 的字符串形态与 encoding="utf-8" 的作用。leave_policy.txt 可见内容：Casual Leave 每年 12 天；Earned Leave 每月 1.5 天、每年 18 天（后半句画面截断，仓库补全见存疑点）；Sick Leave 每年 10 天带薪。
- 21:32 嵌入：add_embeddings 逐条调 get_embedding（上一集的函数，复用粘贴），把整文向量存进 document["vector"]。作者反复强调这是故意不分块的简化版。
- 25:49 搜索：见保留区代码；无任何向量库或索引结构，就是内存列表遍历——缩略图自称 BRUTE FORCE IN-MEMORY，不要误写成向量数据库检索。
- 32:22 演示：查询 maternity leave → leave_policy.txt 最高、medical_policy.txt 次高（含有 maternity 覆盖内容）；三条分数的精确值终端小字不可读。演示答案的原文其实就在文件里（桌面核查：Maternity leave is 26 weeks of paid leave；Paternity leave is 10 working days）。
- 45:46–58:57 四个问题：见技巧 2–5；结尾行动项：P1→chunking、P2→落盘、P3→chunking、P4→阈值兜底；作者自评 chunking 最重要。

### 五、原样保留区（逐字；已经仓库互校）

.env（画面逐字；API_KEY 在画面上就是 dummy_key，非真实密钥）：

```
BASE_URL=http://127.0.0.1:11434/v1
API_KEY=dummy_key
MODEL=qwen3:4b
DEBUG=False
EMBED_MODEL=nomic-embed-text
#Video-14
KB_FOLDER=knowledge_base
```

load_documents（doc_search.py）：

```python
EMBED_MODEL = os.getenv("EMBED_MODEL")
KB_FOLDER = os.getenv("KB_FOLDER")
#Load the documents
def load_documents(folder=KB_FOLDER):
    documents = []
    for filename in sorted(os.listdir(folder)):
        if not filename.endswith(".txt"):
            continue
        path = os.path.join(folder, filename)
        with open(path, "r", encoding="utf-8") as f:
            documents.append(
                {"source": filename,
                 "text": f.read(),
                 }
            )
    return documents
```

search（画面关键片段 + 仓库补全的结尾两行）：

```python
#Search method to search the top_k documents for a user_query
def search(query, documents, top_k=3):
    query_vector = get_embedding(query)
    results = []
    for document in documents:
        score = cosine_similarity(query_vector, document["vector"])
        results.append(
            {
                "score": float(score),
                "source": document["source"],
                "text": document["text"],
                "size": len(document["text"])
            }
        )
    results.sort(key=lambda rec: rec["score"], reverse=True)
    return results[:top_k]
```

符号说明（按本集口径）：query_vector 是查询经 get_embedding 得到的向量；document["vector"] 是整份文档的单个向量；score 为两者余弦相似度，越大越相似；top_k 默认 3；size 是文本字符数 len(document["text"])。**本集没有在屏幕上写出余弦相似度的公式展开式**，不要把教科书公式当作本集内容。

### 六、术语表

- embedding：把文本映射成向量的表示；本集一文一向量。
- cosine similarity（余弦相似度）：比较两个向量方向接近程度的分数，本集以函数 cosine_similarity(query_vector, document["vector"]) 形式出现。
- top_k：按分数降序返回的前 k 条结果数，本集默认 3。
- chunking（分块）：把长文档切成小块分别嵌入与检索，是本集三个问题的共同解法方向（后续视频展开）。

### 七、易错点和存疑点

- 桌面核查补全三处：① doc_search.py 与画面转录逐字一致并通过语法校验，排序/返回两行（画面小字未读清）由仓库确认如保留区所示；② leave_policy.txt 截断句补全为 "Up to 30 days of earned leave can be carried forward to the next year."；③ 演示 ground truth 在文件中可核（产假 26 周、陪产假 10 个工作日）。文件实为 13 行内容，口述「约 14 行」量级一致。
- 手写板书阶段的代码用 name、doc 等简写，最终文件用 filename、document——以最终文件为准，板书只是讲解草稿。
- 页面标题与简介内 Video Title 差一个 Creating，按页面标题著录。
- 演示三条分数的精确小数不可读，只有「leave_policy 最高、medical 次高」的定性结论；Problem 4 板书的 0.12 与 0.01 是板书大字可确认，但对应文件名板书潦草不可读。
- 不要把本集检索写成「向量数据库」：没有 FAISS/Chroma 等任何外部组件，就是内存列表加暴力遍历。

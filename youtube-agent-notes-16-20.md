# YouTube Agent 技巧视频笔记 · 第 16–20 条

> 核验型笔记第二批之第二组。取材标准：字幕、时间戳、代码原文三样齐全；播放器逐章核验，配套仓库克隆对照；看不清处标「不可读」，推断与原文分开标注。

## 目录

- [16. I'm HOOKED on Claude Code Hooks](#16-im-hooked-on-claude-code-hooks)
- [17. How to Build Claude Code Skills](#17-how-to-build-claude-code-skills)
- [18. Building more effective AI agents](#18-building-more-effective-ai-agents)
- [19. This is Hands Down the BEST Way to Build AI Agents](#19-this-is-hands-down-the-best-way-to-build-ai-agents)
- [20. MCP Server Explained & Built: FastAPI to MCP in Just 2 Lines](#20-mcp-server-explained--built-fastapi-to-mcp-in-just-2-lines)

---

## 16. I'm HOOKED on Claude Code Hooks

### 一、元信息

- 标题：I'm HOOKED on Claude Code Hooks: Advanced Agentic Coding
- 链接：https://www.youtube.com/watch?v=J5B9UGTuNoM
- 作者：IndyDevDan（15.3 万订阅）
- 时长：30:50
- 发布时间：2025-07-07
- 观看量：124,858 次（点赞 3,286）
- 配套仓库：https://github.com/disler/claude-code-hooks-mastery （已克隆对照并实跑，见存疑点与时效性）；官方文档 https://docs.anthropic.com/en/docs/claude-code/hooks
- 章节（简介原样，Uv 拼写照录）：00:00 Hooked on Claude Code Hooks；01:10 Claude Code Hooks Breakdown；06:25 When and Why Use Claude Code Hooks；10:05 Understanding Uv Python Hooks；18:57 Agentic Coding Wars；27:25 Principled AI Coding and Phase 2

### 二、一句话结论与摘要

Hook 是把确定性行为注入 agent 生命周期的口子：模型管判断，hook 管「这件事永远不许发生」和「每次发生都要留痕」。

- 拍摄时点 Claude Code 只有 5 个 hook 事件：PreToolUse、PostToolUse、Notification、Stop、SubagentStop；用途收束为两个词——observability（可观测）与 control（控制）。
- 开场实测：PreToolUse 用一段 Python 正则拦下三种 rm -rf 变体，并阻止任何工具碰 .env 文件；拦截机制是脚本向 stderr 打印原因并以 exit code 2 退出。
- 全部 hook 都写成 uv 单文件脚本：shebang 里声明解释器与依赖，自包含、不依赖项目代码，stdin 读 JSON、自由决定放行还是拦截。

### 三、技巧总结

- 技巧 1：拦截层要放在工具执行之前，且用退出码说话。PreToolUse 在任何工具运行前触发；脚本判断危险就打印 BLOCKED 原因到 stderr 并 `sys.exit(2)`（代码注释原文："Exit code 2 blocks tool call and shows error to Claude"）——错误会回给模型，模型能读懂并改道。实测三连：rm -rf /tmp/nonexistent、rm -rf .、rm -rf * 全被拦，终端原文 "BLOCKED: Dangerous rm command detected and prevented"。我的思考：这条正好补上第 17 条的结论——skill 是提议、模型可以绕过；hook 是代码、模型绕不过。凡是「必须发生/绝不许发生」的事，都不该写在提示词里。
- 技巧 2：危险命令检测写成「归一化 + 两层模式」。配套仓库 is_dangerous_rm_command 的做法（仓库原文，视频画面只拍到函数签名）：先把命令小写、压缩空白；第一层模式命中一切同时带 r 与 f 标记的 rm（-rf、-fr、-Rf、--recursive --force 等）；第二层在只有递归标记时再查危险路径（/、~、$HOME、..、*、. 等）。桌面实测（本笔记实跑）：三条演示命令全 True；连 rm -rf build/ 也 True——它就是保守全拦，不是只拦危险路径；rm file.txt、rm -r old_dir、ls 为 False。.env 守卫同理实测：Read .env 拦、Read .env.sample 放行、Bash 里 cat .env 拦。我的思考：全拦 rm -rf 看似粗暴，实际是对的取舍——这类命令的误拦成本（改用逐个删）远低于漏拦成本。
- 技巧 3：日志即观测面。PostToolUse 把每次工具调用的 tool_name、tool_input、tool_response 落成 JSON 日志；Stop hook 顺 transcript_path 把整段会话抄进 chat.json。日志太大读不完时，作者的做法是开 4 个 subagent 各读一个日志文件的前 20 行并行汇总（实测每个约 18.6k–22.6k tokens、9.5–12.5 秒）。我的思考：这套「hook 落盘 + subagent 读日志」就是最朴素的 agent 观测栈，和第 13 条 Letta 的自我改进循环是同一件事的两端：没有结构化留痕，自省就没有材料。
- 技巧 4：settings 结构与单文件脚本。配置形态：事件 → 数组，每项含 matcher（工具名过滤，空字符串表示全部）与 hooks 列表（type: command + command 字符串），一个事件可挂多条命令。作者的命令统一是 `uv run .claude/hooks/<脚本>.py`；脚本头部用 `#!/usr/bin/env -S uv run --script` 加 PEP 723 风格的内联元数据（requires-python = ">=3.8"）声明运行环境。也可以用 Bun/TS 或 shell 直接写。仓库现行版命令已改用 $CLAUDE_PROJECT_DIR 前缀并带 --chat、--notify 等参数（见时效性）。
- 技巧 5：Notification 与 Stop 的人格化用法。Notification 在 Claude Code 等用户输入时触发，作者接上 TTS 播报「Your agent needs your input」；Stop 与 SubagentStop 在完成时用小模型生成一句自然语言结语并 TTS 念出来（utils 里备了 ElevenLabs、OpenAI 与本地 pyttsx3 三种引擎）。这类用法不改变行为，只改变「在场感」——长时间任务不用盯屏。

- 时效性（截至 2026-11-01）：**「只有 5 个 hook」是 2025-07 的时点口径，早已过期。** 配套仓库现行 hooks 目录已有 14 个脚本，事件集扩到含 UserPromptSubmit、SessionStart、SessionEnd、PreCompact、PermissionRequest、PostToolUseFailure、SubagentStart、Setup 等；引用本条时必须区分「视频时点」与「现行文档」。拦截与日志的机制本身（stdin JSON、exit code 2、matcher）未变，仍可照做。视频后段关于 Boris 与 Cat 被 Anysphere 挖角的评论与离职原因概率（75/10/10）是作者个人猜测，无来源，不作事实引用。

### 四、详细笔记（按章节）

- 0:00–1:10 开场演示：三种 rm -rf 全拦；作者口号「The best code is no code at all」；一个 prompt 生成 4 份日志，4 个 subagent 并行读日志前 20 行。
- 1:10–6:25 五个 hook 逐个拆解：触发时机与 payload 见原样保留区的 README 原文；核心用途 observability + control；Notification 演示为 bun 命令等权限确认。
- 6:25–10:05 何时用 hook：matcher 语义（空 = 全部工具）、一事件多命令；官方文档画面确认 PreToolUse 定义为 "Runs after Claude creates tool parameters and before processing the tool call."，PostToolUse 为 "Runs immediately after a tool completes successfully."，且文档专列 Exit Code 2 Behavior 一节。
- 10:05–18:57 uv 单文件脚本深挖：settings.json、pre_tool_use.py、日志 JSON 与 TS 接口、Stop 的 chat 抄录、TTS utils（细节见保留区与技巧总结）。
- 18:57–27:25 Agentic Coding Wars：行业评论段（Boris 与 Cat 跳槽 Anysphere 的猜测），与技术内容无关，笔记只存其存在与「Claude Code 是第一个 programmable agentic coding tool」的论点，不存其概率数字为事实。
- 27:25–30:50 收尾原则：context、model、prompt 是 "the big three"；"Agentic coding is just a superset of AI coding"；"great planning is great prompting"；余为作者课程 Phase 2 推销。

### 五、原样保留区（逐字）

settings.json 的两个完整事件段（视频画面逐字；其余三段在画面中未完整展开，仓库现行版见存疑点）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "uv run .claude/hooks/pre_tool_use.py"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "uv run .claude/hooks/post_tool_use.py"
          }
        ]
      }
    ]
  }
}
```

README《Hook Lifecycle & Payloads》（画面原文）：

```
This demo captures all 5 Claude Code hook lifecycle events with their JSON payloads:
1. PreToolUse Hook / Fires: Before any tool execution / Payload: tool_name, tool_input parameters / Enhanced: Blocks dangerous commands (rm -rf, .env access)
2. PostToolUse Hook / Fires: After successful tool completion / Payload: tool_name, tool_input, tool_response with results
3. Notification Hook / Fires: When Claude Code sends notifications (waiting for input, etc.) / Payload: message content / Enhanced: TTS alerts - "Your agent needs your input" (30% chance includes name)
4. Stop Hook / Fires: When Claude Code finishes responding / Payload: stop_hook_active boolean flag / Enhanced: AI-generated completion messages with TTS playback
5. SubagentStop Hook / Fires: When Claude Code subagents (Task tools) finish responding / Payload: stop_hook_active boolean flag / Enhanced: TTS playback - "Subagent Complete"
```

pre_tool_use.py 主干（视频画面逐字；两个判定函数的函数体画面被折叠，以下补入**配套仓库现行原文**并已离线实跑验证，判定结果见技巧 2）：

```python
#!/usr/bin/env -S uv run --script
# /// script
# requires-python = ">=3.8"
# ///

import json
import sys
import re
from pathlib import Path

def is_dangerous_rm_command(command):
    """
    Comprehensive detection of dangerous rm commands.
    Matches various forms of rm -rf and similar destructive patterns.
    """
    # Normalize command by removing extra spaces and converting to lowercase
    normalized = ' '.join(command.lower().split())

    # Pattern 1: Standard rm -rf variations
    patterns = [
        r'\brm\s+.*-[a-z]*r[a-z]*f',  # rm -rf, rm -fr, rm -Rf, etc.
        r'\brm\s+.*-[a-z]*f[a-z]*r',  # rm -fr variations
        r'\brm\s+--recursive\s+--force',  # rm --recursive --force
        r'\brm\s+--force\s+--recursive',  # rm --force --recursive
        r'\brm\s+-r\s+.*-f',  # rm -r ... -f
        r'\brm\s+-f\s+.*-r',  # rm -f ... -r
    ]

    # Check for dangerous patterns
    for pattern in patterns:
        if re.search(pattern, normalized):
            return True

    # Pattern 2: Check for rm with recursive flag targeting dangerous paths
    dangerous_paths = [
        r'/',           # Root directory
        r'/\*',         # Root with wildcard
        r'~',           # Home directory
        r'~/',          # Home directory path
        r'\$HOME',      # Home environment variable
        r'\.\.',        # Parent directory references
        r'\*',          # Wildcards in general rm -rf context
        r'\.',          # Current directory
        r'\.\s*$',      # Current directory at end of command
    ]

    if re.search(r'\brm\s+.*-[a-z]*r', normalized):  # If rm has recursive flag
        for path in dangerous_paths:
            if re.search(path, normalized):
                return True

    return False
```

```python
def main():
    try:
        # Read JSON input from stdin
        input_data = json.load(sys.stdin)
        tool_name = input_data.get('tool_name', '')
        tool_input = input_data.get('tool_input', {})

        # Check for .env file access (blocks access to sensitive environment files)
        if is_env_file_access(tool_name, tool_input):
            print("BLOCKED: Access to .env files containing sensitive data is prohibited", file=sys.stderr)
            print("Use .env.sample for template files instead", file=sys.stderr)
            sys.exit(2)  # Exit code 2 blocks tool call and shows error to Claude

        # Check for dangerous rm -rf commands
        if tool_name == 'Bash':
            command = tool_input.get('command', '')
            # Block rm -rf commands with comprehensive pattern matching
            if is_dangerous_rm_command(command):
                print("BLOCKED: Dangerous rm command detected and prevented", file=sys.stderr)
                sys.exit(2)
```

拦截终端原文（画面）：

```
Bash(rm -rf /tmp/nonexistent)
Error: Bash operation blocked by hook: - [uv run .claude/hooks/pre_tool_use.py]: BLOCKED: Dangerous rm command detected and prevented
```

日志条目样本（pre_tool_use.json 首条，画面）：

```json
{ "session_id": "1d523568-56a5-4257-bce4-ac39e2b9ac7f", "transcript_path": "/Users/indydevdan/.claude/projects/-Users-indydevdan-Docu...", "hook_event_name": "PreToolUse", "tool_name": "Read", "tool_input": { "file_path": ".../ai_docs/cc_hooks_docs.md", "limit": 10 } }
```

### 六、术语表

- hook（钩子）：在 agent 生命周期事件点上由宿主执行的外部命令，从 stdin 收 JSON、可用退出码与输出影响流程。
- matcher：事件配置里的工具名过滤器，空字符串表示对所有工具触发。
- exit code 2：Claude Code hook 约定中表示「拦截本次调用并把 stderr 展示给模型」的退出码（视频代码注释原文）。
- PEP 723 内联元数据：写在脚本头部注释块里的依赖与 Python 版本声明，uv run --script 据此准备运行环境。

### 七、易错点和存疑点

- 最大的坑是把「5 个 hook」当现行事实：那是 2025-07 时点；仓库与文档现已扩充（见时效性）。UserPromptSubmit、SessionStart 等事件**没有**出现在本视频里，任何「视频介绍了它们」的说法都是错的。
- 桌面核查对视频转录的两处补正：① pre_tool_use.py 中 rm 拦截的 print 行在画面被字幕遮挡，其完整文本 "BLOCKED: Dangerous rm command detected and prevented" 由终端输出与仓库原文双向确认；② settings.json 其余三事件的 command 行画面未展开，仓库现行版已补全形态（notification.py --notify、stop.py --chat、subagent_stop.py --notify，且路径带 $CLAUDE_PROJECT_DIR 前缀）——引用时注明版本来源。
- README 里 Notification 的 "(30% chance includes name)" 语义怪异，画面如此，照录不改。
- 仓库现行 pre_tool_use.py 可能仍在演进（克隆时点 2026-11-01）；is_env_file_access 的函数体以仓库为准，视频只拍到签名。
- 章节标题 "Understanding Uv Python Hooks" 的 Uv 是简介原拼写，不要「纠正」成 UV 后当原文引用。
- Agentic Coding Wars 段的人事与概率内容为作者猜测，不核实、不引用。

---

## 17. How to Build Claude Code Skills

### 一、元信息

- 标题：How to Build Claude Code Skills (3 Free Skill Files)
- 链接：https://www.youtube.com/watch?v=-QvkQTx4KCM
- 作者：MaxDevAi（频道一人称叙事；页面披露为 AI 配音，Narrator: Daniel, Cartesia）
- 时长：9:05（一次回载显示 9:06；末约 5 秒为片尾卡）
- 发布时间：2026-10-19
- 观看量：160 次（频道订阅 78）
- 实验条件（简介披露）：测试于 2026-10-18 运行；Claude Code 2.1.291 + Opus 5.5；每种设置跑 3 个全新会话；共 65 次有效运行，总成本 $36.23（单次 $0.23–$1.84，按标价折算、非实付）
- 唯一外链：https://play.maxdevai.com/skills/ （三个 skill 文件的领取页；未下载实物核对）
- 章节（14 段，简介原样）：0:00 One file, three brands；0:38 What is a brand skill；1:08 Two more files, one test；1:37 The scroll test；2:23 A two-line memory；3:08 Anthropic's own design skill；3:52 Strengths and limits；4:22 Superpowers；5:17 The hidden-bug test；5:52 Not magic, just files；6:21 The shop-checkout test；7:34 CLAUDE.md vs skill files；7:55 Make your own in five steps；8:23 The verdict

### 二、一句话结论与摘要

一个只写品牌调性的单文件 skill，在受控对照里比无 skill 基线稳、比官方设计 skill 一致、比大型 skill 框架更省——skill 的价值不在魔法，在「把品味写成文件 + 两行自学机制」。

- 对照实验：同一提示词、同一模型、三次全新会话，横比四种设置——自写品牌文件、Anthropic 官方 frontend-design skill、Superpowers 插件框架、无 skill 基线。
- 品牌文件的「自学」机制只有两行：开工先读 lessons.md；被纠正时往 lessons.md 追加一行。一次纠正后，3/3 新会话都改了标题。
- Superpowers 在本次测试里没有调用它自己的任何一个 skill——作者原话「A skill is an offer, not an order」（skill 是提议，不是命令）；且测试对它并不完全公平（见存疑点）。

### 三、技巧总结

- 技巧 1：品牌 skill = 一个 SKILL.md，只写三件事。用 YAML frontmatter（name + description）声明用途，正文分 Look（视觉规范）、Words（文案口径）、Never（禁区）三节。视频给的 apple-style 文件全文已收进原样保留区。触发靠 description，不是靠命令。我的思考：这跟主人自己仓库里 skill 的写法同构，验证了「description 决定触发」这条经验；禁区（Never）单列一节也是好习惯——禁止项比倡导项更能收敛输出。
- 技巧 2：两行自学（a two-line memory）。skill 里写两条指令：①每次先读同目录的 lessons.md；②被用户纠正时，把教训写成一行追加进 lessons.md。实测一次纠正（标题问题）之后，3/3 个全新会话都换上了新标题风格——但三次换的标题并不相同，学到的是「方向」不是「答案」。我的思考：这是最便宜的「记忆」形态——没有向量库、没有遗忘曲线，就是一个追加写的人类可读文件。它和主人研究线里的「写入时版本管理」是同一件事的极简版：教训以追加方式落盘、下次开工先读。值得注意的是它没有覆盖与冲突处理，教训攒多了会自相矛盾。
- 技巧 3：官方 skill 不等于最适合。frontend-design（Anthropic 官方，Apache 2.0，skills.sh 显示 1.0M installs）确实去掉了小字说明这类毛病，但每跑一次选一种不同的色调，三次里最慢（每页 3.5 分钟 vs 品牌文件不到 2 分钟），且 3/3 仍以「Light that keeps time.」开场——通用品味文件管不住具体的文案口头禅。我的思考：一致性来自约束的针对性。官方文件要服务所有人，只能写普适原则；自己的文件可以写死「我们不用这句话」。
- 技巧 4：大型框架的调用是概率的。Superpowers（Jesse Vincent / obra，MIT，301k GitHub stars）每会话约多花 2,000 tokens；隐藏 bug 测试里各设置都找到了 bug；但 Superpowers 在测试中一次都没有真正调用它包含的 skill。shop-checkout 测试（14 个隐藏测试）里它 3 次只启动了 1 次完整流程（测试先行，那次隐藏测试全对），另外 2 次跳过自己的流程、还漏了项目笔记里的一条规则——而裸 Claude 三次也全对。结论不是框架没用，是「流程越重，模型越可能绕过它，且慢且贵」。我的思考：这为「skill 是提议不是命令」提供了实测注脚。想让流程必达，得靠 hook 这类确定性机制兜底，而不是把希望寄托在模型每次都自觉翻流程文件——这正好是第 16 条视频的主题。
- 技巧 5：CLAUDE.md 与 skill 的分工（作者口径）。CLAUDE.md 是每个会话都要守的规矩，skill 是偶尔才需要的菜谱。把偶尔用的长流程塞进 CLAUDE.md，等于每次会话都为它付费。

- 时效性（截至 2026-11-01）：发布于 2026-10-19，距今不到两周，是本批最新鲜的实测之一。版本绑定明显：Claude Code 2.1.291 + Opus 5.5 的行为结论（尤其「框架 skill 未被调用」）随模型与客户端版本变化可能很快过期；引用的安装量与 star 数（1.0M installs、301k stars）为视频时点数。方法论（同提示词×三次新会话×隐藏测试）不受版本影响，可直接复用。

### 四、详细笔记（按章节）

- 0:00–1:08 一个文件，三种品牌 / 什么是品牌 skill：品牌文件每次都被加载并遵循（9/9）；无 skill 基线反复产出米色页面和同一句标题「Light that keeps time.」（2 次）与「Light that follows your day.」（1 次）。
- 1:08–1:37 另外两个文件，一次测试：测试设置与样本量交代（见元信息）。
- 1:37–2:23 滚动测试（scroll test）：滚动动画维度无明确胜者。
- 2:23–3:08 两行记忆：自学机制演示，一次纠正后 3/3 新会话改标题。
- 3:08–4:22 Anthropic 官方设计 skill：见技巧总结 3。
- 4:22–5:52 Superpowers 与隐藏 bug 测试：见技巧总结 4；bug 测试各设置均找到 bug，无区分度。
- 5:52–6:21 不是魔法，只是文件：章节题即观点——效果差异全部来自文件内容本身。
- 6:21–7:34 shop-checkout 测试：14 个隐藏测试；Superpowers 1/3 次走完流程且那次全对，另 2 次漏规则；裸 Claude 3/3 全对。
- 7:34–8:23 CLAUDE.md vs skill、五步自制、结论：分工口径与自制步骤（五步的具体条目未在画面逐字读全，见存疑点）。

### 五、原样保留区（逐字，未改动）

被测的品牌 skill 文件 apple-style/SKILL.md（画面逐字）：

```markdown
---
name: apple-style
description: Build pages in the Apple product-page style — huge type, lots of air, one idea per screen. Use for landing pages and product pages.
---

# Apple style

## Look
- One idea per screen. If a section needs a scroll to explain, split it.
- Headline type at 56 to 80 px, weight 600, tight leading. Body 17 px.
- Alternate near-black and white sections. Text is #1d1d1f on white, #f5f5f7 on black.
- One accent colour per page. Links in Apple's blue, #0066cc, underlined only on hover.
- Product image centred, large, nothing competing with it.
- Small grey captions under images are banned. If it matters, give it a headline.

## Words
- Short, confident sentences. Fragments allowed. No exclamation marks.
- Numbers as proof: chip names, hours of battery, grams.
- Never say revolutionary, game-changing, seamless, elevate, unlock, supercharge, or "in the box".

## Never
- No cards in a row of three. No gradients. No stock-photo people smiling.
- Never invent a spec, a price, or a battery figure. If you don't know it, leave it out.
- Never open with "Light that keeps time."
```

同目录 tokens.css（逐字转录；其中一行不可读，见存疑点）：

```css
:root {
  --ink: #1d1d1f;
  --paper: #ffffff;
  --dark: #000000;
  --dark-ink: #f5f5f7;
  --accent: #0066cc;
  --body-size: 17px;
  /* （一行不可读，位于 --body-size 与 --headline-weight 之间） */
  --headline-weight: 600;
}
```

shop-checkout 测试的商店规则（画面逐字要点）：折扣码 SAVE10 不分大小写、每单限用一次、舍入 half up（四舍五入）。六条测试提示词全在简介中，已逐条转录存档（本地抽取文件）。

### 六、术语表

- skill（技能文件）：带 frontmatter 的指令文件，按 description 被模型按需加载；本视频口径下与 CLAUDE.md（常驻规矩）相对。
- lessons.md：与 skill 同目录的教训日志，两行自学机制的载体，开工先读、被纠正则追加一行。
- Superpowers：Jesse Vincent（obra）的 Claude Code 插件框架，含一整套工作流 skill；注意与另一同名产品区分（视频特别提醒：另一个 Superpowers 有 794,000 installs、仅 8 stars，不要混淆）。

### 七、易错点和存疑点

- 「Opus 5.5」与「Claude Code 2.1.291」为简介披露的实验条件，本环境未能独立核实该版本号组合的真实性，按视频口径记录。
- Superpowers 测试的公平性：作者自承他的提示词写了「don't ask me questions」，而 Superpowers 的工作流恰恰以提问开场——这可能压低了它的启动率，引用「0 次调用自家 skill」时须带此条件。
- tokens.css 有一行不可读（--body-size 与 --headline-weight 之间）；SKILL.md 末尾「# Before you say it's done」一节被画面遮挡，只确认其存在，内容未录。
- 自制 skill 的「五步」具体条目未在画面逐字读全，本笔记只记其存在，不补写。
- play.maxdevai.com 的三个 skill 文件未实际下载，文件内容以画面转录为准。

---

## 18. Building more effective AI agents

### 一、元信息

- 标题：Building more effective AI agents
- 链接：https://www.youtube.com/watch?v=uhJJgc-0iTQ
- 作者：Anthropic 官方频道（82.9 万订阅）；出镜为 Alex 与 Erik（姓氏未在画面确认）
- 时长：18:57（首载曾闪 18:58）
- 发布时间：2025-10-18
- 观看量：103,209 次
- 形式：访谈对谈，无幻灯片、无流程图、无代码演示
- 配套原文（非视频内容，桌面核查补充）：Anthropic 工程博客《Building Effective AI Agents》（2024-12 首发），链接见原样保留区后的说明

### 二、一句话结论与摘要

Anthropic 工程师的访谈口径：别迷信多智能体，先把工具设计成「对应用户界面、而不是对应 API」，把单步做成闭环，再谈并行。

- 视频系统区分 workflow 与 agent：流程可预定的走 workflow；每一步都可以再包成闭环 agent（「workflows of agents」）。
- 多智能体的核心收益是同时性：一个 orchestrator 带 5 个并行 subagent；subagent 在父 agent 眼里就是一个工具，提示词即参数。
- 工具设计的原则句：工具应该对应 UI 而不是 API——按最终状态倒推、把上下文打包进工具，而不是把内部接口 1:1 暴露给模型。

### 三、技巧总结

- 技巧 1：workflow 与 agent 的区分（视频口径）。workflow 是预先编排好的步骤序列；agent 是模型自己决定下一步的闭环。进阶形态是「workflows of agents」：流程骨架不变，但每一步内部换成能自我修正的闭环（以 SQL 生成为例）。我的思考：这比博客版的二分法更贴近真实系统——生产里很少有纯 agent，骨架是流程、关节是闭环。
- 技巧 2：多智能体 = 并行 + 工具化。orchestrator 拆任务，多个 subagent 同时干（视频举了 deep research 例：5 个并行）；subagent 对父级呈现为一个普通工具调用，输入就是一段提示词。访谈提到 Claude 曾被「训练掉」新手管理者的毛病：给下属的指令不完整——给 subagent 的提示词必须自带完整上下文，因为它看不到父级的对话。我的思考：这条与第 12 条 LangChain 的上下文隔离互为表里：隔离的好处是干净，代价是每次交接都要把上下文重新打包完整，写不全就丢信息。
- 技巧 3：工具对应 UI，不对应 API。Slack 的例子：内部有近百个端点，但给模型的工具应该按「用户在界面上能做的事」收束（视频举了 3 个端点式的收束口径）。设计方法：从最终状态倒推，工具内部把需要的上下文一次打包。工具太多（100–200 个）时，用 subagent 分桶：每个 subagent 只理解约 20 个工具。访谈还提到「1000 个 Claude」的设想——未实测，只是方向。我的思考：这是本条最可直接照做的一条。把 API 1:1 映射成工具，等于让模型替你做接口编排，还要它自己记住每个端点的脾气；按 UI 收束，编排回到代码里，模型只做判断。
- 技巧 4：Skills 的口径（视频内）。Skill 不只是指令，还包括可复用的资源：模板、代码、脚本、图片；CLAUDE.md 在访谈中被顺带解释为常驻指令文件。Agent SDK 虽然挂着 Claude Code 的名字，实为通用 agent 构建工具。我的思考：与第 17 条对照，官方口径同样把 skill 看作「指令 + 资源」的包，而不是一段长提示词。
- 技巧 5：失败模式与未来。访谈点名的失败模式：过度搭建的多智能体系统「互相聊天而不推进」；未来判断：可验证的领域会先成熟，computer-use 的自我验证是关键路径。具名的模式只有并行化、MapReduce、test-time compute 这几个词，路由、评估器-优化器等系统讲解不在视频里（在配套博客里，见下）。

- 时效性（截至 2026-11-01）：视频发布于 2025-10-18，配套博客为 2024-12 原文、页面现已加注工具格局已变（指向 Claude Managed Agents）。五模式分类与三原则仍是当前最常被引用的分类法，但 SDK 与托管形态的名称、入口以官方最新文档为准；「1000 个 Claude」为未实测设想，不要当结论引用。

### 四、详细笔记（按内容段）

- workflow/agent 之辨与「workflows of agents」：流程步 → 闭环步的替换逻辑，SQL 例。
- 多智能体：同时性收益、orchestrator + 5 subagent 的 deep research 例、subagent 即工具、新手管理者毛病与训练修正。
- 模式点名：parallelization、MapReduce、test-time compute（仅点名，无系统展开）；提示串联只以「串联提示」的一般说法出现。
- 工具分桶：100–200 个工具 → subagent 分桶、每个约 20 个；「1000 Claudes」未测。
- 工具设计：UI 而非 API、Slack 例、按最终状态倒推、上下文打包。
- Skills 与 CLAUDE.md、Agent SDK 的通用性说明。
- 收尾：失败模式（系统互相聊天不推进）、未来在可验证领域与 computer-use 自我验证。

### 五、原样保留区

本视频为纯访谈，无屏幕代码与幻灯片原文可录。以下为**配套博客**《Building Effective AI Agents》的原文定义（桌面核查取得，https://www.anthropic.com/engineering/building-effective-agents ；与视频内容分开标注，不得当作视频原话引用）：

- Workflows: "systems where LLMs and tools are orchestrated through predefined code paths."
- Agents: "systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks."
- Augmented LLM：带 retrieval、tools、memory 的增强型 LLM，是两类系统的基本构件。
- 五个 workflow 模式（博客）：Prompt chaining（提示链）、Routing（路由）、Parallelization（并行，含 sectioning 与 voting）、Orchestrator-workers（编排者-工人）、Evaluator-optimizer（评估器-优化器）。
- 三条原则（博客）：保持简单（simplicity）、透明（transparency，把模型的规划步骤显式展示）、精心打磨 agent 与计算机的接口（ACI，Agent-Computer Interface）。

### 六、术语表

- workflow（工作流）：由预定义代码路径编排 LLM 与工具的系统（博客定义）。
- agent（智能体）：由 LLM 自主决定流程与工具使用的系统（博客定义）。
- orchestrator-workers：编排者动态拆任务、分发给工人模型并综合结果的模式。
- ACI（Agent-Computer Interface）：与 HCI 相对，专为模型设计的工具接口面。

### 七、易错点和存疑点

- 本视频没有系统讲授五模式与 augmented LLM——把博客内容当视频内容引用是本条最大的易错点，笔记已分开标注。
- 字幕误听两处：absorbability 应为 observability；"suffer from the stew" 应为 "the same"（高置信推断）。转录稿面板前三行实际属于约 13:00 段落，存在排序异常，逐字引文须回画面核对。
- 出镜者只确认名为 Alex 与 Erik，姓氏未在画面核实，不补写。
- 「3 个端点式的收束」为访谈举例口径，Slack 工具的内部真实结构未在视频中展示。

---

## 19. This is Hands Down the BEST Way to Build AI Agents

### 一、元信息

- 标题：This is Hands Down the BEST Way to Build AI Agents
- 链接：https://www.youtube.com/watch?v=U6LbW2IFUQw
- 作者：Cole Medin（23 万订阅）
- 时长：51:22（一次回载闪 51:23）
- 发布时间：2025-02-13
- 观看量：173,343 次
- 配套项目：Archon（https://github.com/coleam00/Archon ）；注意：仓库现状已与视频时代码完全不同，见时效性
- 章节（11 段）：00:00 引入与总论；其后依次为 Pydantic AI 基础、V1 单 agent（RAG + Streamlit）、V2 LangGraph 编排、human-in-the-loop、部署与收尾，末章止于 50:41（各章逐字标题以简介为准，本地抽取文件有全录）

### 二、一句话结论与摘要

单 agent 用贴近框架底层的 Pydantic AI 写，多 agent 用 LangGraph 编排——作者反对为简单需求上高层框架，因为「会撞墙」。

- Pydantic AI 管单个 agent 的三件事：依赖（dependencies）、agent 定义、工具；LangGraph 管编排。两者都是低层抽象，作者认为低层才有足够的控制力。
- 演示项目 Archon 是一个「造 agent 的 agent」：V1 纯 Pydantic AI 搜 Pydantic AI 文档做 RAG；V2 加 LangGraph，先由 reasoner 写范围文档，再由 coder agent 照着写代码。
- 人在回路用 LangGraph 的 interrupt + Command(resume=...) 实现暂停与恢复（代码见原样保留区）。

### 三、技巧总结

- 技巧 1：按层选框架。单体 agent 与编排是两个问题，分开选工具：单体要贴近模型的库（依赖注入、类型化工具），编排要图式的库。作者顺带说明 Pydantic Graphs 不是不用，是「LangGraph 演进得更远」。我的思考：这条与第 18 条 Anthropic 的「先简单」不冲突——选低层抽象不是堆复杂度，是不让框架的预设替你做决定。
- 技巧 2：reasoner 先行写范围，再开工。V2 的图：__start__ → define_scope_with_reasoner → coder_agent → get_next_user_message → finish_conversation。reasoner（默认 o3-mini，读环境变量）先产出一份 scope 文档，动态注入 coder 的 system prompt（@pydantic_ai_coder.system_prompt 装饰器）。公平说一句：演示里那份 scope 其实是 GPT-4o mini 写的（例见存疑点）。我的思考：先写范围文档再执行，等于把「计划」从对话里拿出来变成工件——可检查、可复用、可注入。这与主人做项目时「先规格后编码」的流程是同一件事。
- 技巧 3：人在回路的标准件。暂停点 interrupt({}) 抛出、恢复用 Command(resume=...) 回填；router 与 coder 共用同一个 LLM；消息在两框架间转换靠 ModelMessagesTypeAdapter.validate_json(result.new_messages_json())。代码碎片见原样保留区。
- 技巧 4：工程现实清单。Ollama 在 Pydantic AI 里不能流式（作者怀疑是 bug），退回同步 run + 模拟流式；状态先放内存，生产建议换 SQLite/Postgres saver；V1 的 RAG 链路是 sitemap → Crawl4AI 爬取 → 分块 → Supabase + PG Vector，工具只有三个：RAG 搜索、列页面、取页面。演示翻车点：.env.example 漏了 OpenAI API key。我的思考：这类「讲师自己踩的坑」最值得记——文档示例与运行环境脱节，是 agent 项目最常见的第一道坎。

- 时效性（截至 2026-11-01）：视频发布于 2025-02-13，是本组最旧的一条。**重大变化：配套仓库 Archon 已被整个重写**——现行版本是 YAML 工作流式的「factory builder」（TypeScript/bun，dev 分支、无 tag），视频里的 Python 代码（iterations/v1-single-agent、v2-agentic-workflow、pydantic_ai_coder.py）在仓库中已不存在。因此本条的代码只能以播放器画面为准，链接仓库只能当历史出处，不能当代码原文。Pydantic AI 与 LangGraph 本身仍在活跃演进，API 细节按当前文档为准。

### 四、详细笔记（按章节）

- 总论：两框架定位与反过度工程警告（「不是每件工作都适合」某种工具）。
- Pydantic AI 三要素：dependencies / agent 定义 / tools。
- V1（Archon 单 agent）：RAG over Pydantic AI 文档（抓 sitemap → Crawl4AI → 分块 → Supabase+PG Vector）；巨型 system prompt 让它「造别的 Pydantic AI agent」；3 个工具；Streamlit 界面（作者称 115 行，未逐行核）。
- V2（LangGraph 编排）：图结构与 scope 注入见技巧 2；router 与 coder 共用 LLM。
- 人在回路与状态：见技巧 3、4。
- 演示（reasoner=o3-mini，coder=GPT-4o，目标为带 web search 的 agent）：产出大体可用，但 .env.example 漏 OpenAI key。

### 五、原样保留区（播放器画面逐字碎片）

人在回路与图构建（画面代码碎片）：

```python
interrupt({})
Command(resume=...)
```

```python
# StateGraph 构建块（画面碎片拼合：节点名与边以画面为准）
__start__ → define_scope_with_reasoner → coder_agent → get_next_user_message → finish_conversation
```

scope 动态注入（画面）：

```python
@pydantic_ai_coder.system_prompt
# scope 文档在运行时注入 coder 的 system prompt
```

消息转换（画面逐字）：

```python
ModelMessagesTypeAdapter.validate_json(result.new_messages_json())
```

（说明：视频时代的完整源文件已不在仓库中，以上仅为播放器可读碎片，未实测；完整 prompt、.env.example、scope.md 正文为编辑器小字，本次不可读。）

### 六、术语表

- dependencies（依赖注入）：Pydantic AI 里把运行时依赖（客户端、配置）显式传给 agent 与工具的机制。
- scope document：reasoner 在执行前写下的范围文档，作为工件注入执行者的 system prompt。
- saver（checkpointer）：LangGraph 持久化图状态的组件，内存版仅适合演示，生产换 SQLite/Postgres。

### 七、易错点和存疑点

- 简介称产出「production-ready」，口述却是「pretty basic implementation / not all perfect」——以口述为准，演示代码是教学级。
- 口述称走读约「20 minutes」，章节跨度实际约 25:43，时长口径不一致。
- 两次演示对 env 变量的结论不同（不同运行的不同产出），不要合并成一个结论。
- 图的结束节点标签未在画面完全确认；V1 的 Streamlit「115 行」未逐行核。
- Archon 仓库已重写（见时效性）：任何「仓库里有这份代码」的说法都是错的。

---

## 20. MCP Server Explained & Built: FastAPI to MCP in Just 2 Lines

### 一、元信息

- 标题：MCP Server Explained & Built: FastAPI to MCP in Just 2 Lines | Day 5
- 链接：https://www.youtube.com/watch?v=Pdvskk55X80
- 作者：ConnectXeo（257 订阅）；ZEK 是其连载在建的 agentic commerce 系统，本集为 Day 5
- 时长：23:04–23:05（播放器在不同时刻显示不一致，两个数值并列记录）
- 发布时间：2026-10-26
- 观看量：72 次
- 配套仓库：https://github.com/Sami606713/zek （已克隆对照，见时效性与存疑点）；FastMCP 文档 https://gofastmcp.com/
- 章节（简介原样）：0:00 Intro；0:30 Welcome to Day 5: what we'll cover；1:47 MCP server vs MCP clients；2:59 Why use MCP? No more custom tool maintenance；4:00 How to build an MCP server；4:40 FastMCP: the framework for MCP；5:28 Convert a FastAPI app to MCP in 2 lines；7:06 Walkthrough of the ZEK backend & middleware；8:58 Setting up the MCP route in the backend；9:50 Route maps: choose which APIs become MCP tools；11:05 One server instead of three: mounting MCP at /mcp；12:51 Connecting the MCP server to Claude；13:46 Running the backend & MCP server；15:13 Token auth: protecting MCP tools；17:23 Testing with a real access token；19:33 Installing the MCP server in Claude；19:58 Live test: get my profile & cheapest products；21:33 Recap & what's next: building the AI agent

### 二、一句话结论与摘要

已有 FastAPI 后端时，FastMCP 一行转换就能把它变成 MCP 工具面——真正的工作量在 route maps 的暴露面收敛和 token 转发鉴权这两处。

- 转换本身：`FastMCP.from_fastapi(app=app)`；标题的「2 行」是导入行 + 转换行，转换本身一行（FastMCP 文档页自称 single line）。
- 暴露面靠 route_maps 负面清单：默认全变工具，逐条 EXCLUDE 掉健康检查、WhatsApp webhook、五个发 token 的登录端点、两个 multipart 上传端点。
- 鉴权不重写：MCP 层验 bearer token（与 REST 同规则），再把 token 转发给 API，由 API 自己的权限决定调用者能碰什么。

### 三、技巧总结

- 技巧 1：从已有 API 生成，而不是另写工具层。FastMCP 文档原话："One of the most common ways to bootstrap an MCP server is to generate it from an existing FastAPI application. FastMCP will expose your FastAPI endpoints as MCP components (tools, by default)"。两种集成方向：从 FastAPI 生成 MCP server，或把 MCP server 挂进 FastAPI——本集用后者（同 app 同端口，/api/v1 与 /mcp 并存）。我的思考：这让「给 agent 开工具面」从新项目降级为老项目加一段配置，前提是 REST 层本身已经把权限做对了。
- 技巧 2：route maps 是负面清单思维。默认全部暴露、逐条排除，每条排除都写明理由（代码注释原文见保留区）：webhook 是给机器调的、「a tool here could take incoming messages」；登录端点会发新 token、调用者本就已登录；multipart 上传根本过不了工具通道。我的思考：默认全开 + 负面清单上线快，但新加端点会自动变成工具——这正是第 15 条视频里「暴露面」的同款风险。更稳的默认是白名单；视频的做法适合连载演示，生产环境要把这条反过来。
- 技巧 3：鉴权转发而不是鉴权复制。ZekTokenVerifier 按 REST 同规则验 token（签名、类型、过期、用户状态、密码变更指纹），工具调用再通过 event_hooks 把 token 转发给 API。权限判断只存在于一处（API 层），MCP 层不维护第二套规则。实测闭环完整：无 token 401、测试 token 被拒、真 token 200（服务器自报 ZEK MCP v4.1.0）；token 30 分钟过期（expires_in: 1800）就得换新并重跑注册命令，明文存 ~/.claude.json——作者明说只适合本地测试。我的思考：这是整集最值得抄的架构决定：工具面只是入口，授权永远落在业务层。
- 技巧 4：挂载的细节决定嵌入性。不做 catch-all mount、只复制确切的 /mcp 路由，是为了让 langgraph dev 经 langgraph.json 嵌入该 app 时不弄坏它自己的路由；代价是复制路由会跳过 MCP app 自身的 middleware，必须手动把验 token 的 middleware 再包回 /mcp 路由外层（代码见保留区）。这类「绕开框架默认、手动补回被跳过的环节」正是 harness 工程的日常。

- 时效性（截至 2026-11-01）：发布于 2026-10-26，非常新。画面里 FastMCP 文档版本为 v4.0.5、服务器自报 v4.1.0；FastMCP 迭代快，from_fastapi 的参数名已出现版本差（见存疑点），照抄前先对当前文档。ZEK 本身是连载在建项目，仓库代码会继续变。

### 四、详细笔记（按章节）

- 1:47 MCP server vs clients：Miro 图定义「One standard plug that lets any AI agent use the same tools, data, and prompts. Build it once, and every MCP-compatible AI can connect to it.」客户端列 ZEK agents、Claude、Codex/ChatGPT、Grok、Gemini、Cursor；server 三件套 Tools/Resources/Prompts；系统侧 Store backend、Database、Cloudinary、WhatsApp API。
- 2:59 Why MCP：不再为每个 agent 各写一套 custom tool。
- 7:06 后端走读：main.py 结构——dotenv 先行（其他模块直读 os.environ）、CORS 最外层（注释：连 429 与 500 都要带 CORS 头）、异常兜底中间件、内存滑动窗口限流（注释：「Fine for a single process; use Redis when scaling out.」；豁免 /health、/docs、/redoc、/openapi.json；限流数值行未读清）。
- 8:58–11:05 MCP 路由与挂载：见技巧 2、4 与保留区代码。
- 12:51–17:23 运行与鉴权：uvicorn 起 0.0.0.0:8000；无 token 访问 /mcp 得 HTTP ERROR 401；TokenVerifier 逻辑见技巧 3。
- 19:33–21:33 Claude 侧与 live test：Claude Code 命令行注册（命令见保留区），claude mcp get zek 报 ✓ Connected、scope 为本项目 local；实测问 profile 与最便宜商品（16 件在售中 10 件最便宜清单在抽取存档；最便宜 3 件合计 $73.50，验算一致）；agent 明示未加购物车、未下单，下单前会列清单等确认，并指出账号未验证（is_verified: false）可能被服务器拦下结账。

### 五、原样保留区（以配套仓库现行源码为准；与视频画面转录的差异见存疑点）

转换（FastMCP 文档页 Basic Conversion，画面逐字）：

```python
# Assumes the FastAPI app from above is already defined
from fastmcp import FastMCP

# Convert to MCP server
mcp = FastMCP.from_fastapi(app=app)

if __name__ == "__main__":
    mcp.run()
```

MCP 初始化与 route_maps（仓库 main.py 现行原文；已通过 python -m py_compile 语法校验，未整机运行——运行需数据库与环境变量）：

```python
mcp = FastMCP.from_fastapi(
    app=app,
    name="ZEK MCP",
    auth=mcp_auth,
    httpx_client_kwargs={"event_hooks": {"request": [forward_access_token]}},
    route_maps=[
        RouteMap(tags={"System"}, mcp_type=MCPType.EXCLUDE),
        # Called by WhatsApp, never by a user: a tool here could fake incoming messages.
        RouteMap(tags={"WhatsApp Webhook"}, mcp_type=MCPType.EXCLUDE),
        # Sign-in flows work without a token and hand out new tokens; the caller is already signed in.
        RouteMap(
            methods=["POST"],
            pattern=r"^/api/v1/users/(register|login|refresh-token|forgot-password|reset-password)$",
            mcp_type=MCPType.EXCLUDE,
        ),
        # File uploads (multipart) can't be sent through a tool; the image-by-URL route stays.
        RouteMap(methods=["POST"], pattern=r"^/api/v1/products/[^/]+/images$", mcp_type=MCPType.EXCLUDE),
        RouteMap(methods=["POST"], pattern=r"^/api/v1/collections/[^/]+/image$", mcp_type=MCPType.EXCLUDE),
        RouteMap(mcp_type=MCPType.TOOL),
    ],
)
```

挂载（仓库现行原文）：

```python
mcp_app = mcp.http_app(path="/mcp")

# Add only the exact /mcp route (not a catch-all mount), so hosts that embed this app,
# like `langgraph dev` via langgraph.json "http.app", keep their own routes working.
# Copying routes skips mcp_app's own middleware, so the auth middleware that reads
# the bearer token is wrapped around the /mcp route here.
_mcp_auth_middleware = mcp_auth.get_middleware()
for _route in mcp_app.routes:
    if getattr(_route, "path", None) == "/mcp":
        _route.app = with_auth_middleware(_route.app, _mcp_auth_middleware)
app.router.routes.extend(mcp_app.routes)

# Kept for anything that still imports the old name.
combined_app = app

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

运行与注册命令（画面逐字，$TOKEN 为占位符）：

```bash
python -m src.app.api.v1.main
claude mcp remove zek
claude mcp add zek -- uvx fastmcp-remote http://localhost:8000/mcp --header "Authorization: Bearer $TOKEN"
claude mcp list
claude mcp get zek
```

健康检查（仓库原文）：

```python
@app.get("/health", tags=["System"], summary="Liveness check")
async def health_check() -> dict[str, str]:
    return {"status": "ok", "version": settings.APP_VERSION}
```

### 六、术语表

- FastMCP：构建 MCP server/client 的 Python 框架；from_fastapi 是其从既有 FastAPI 应用生成工具面的入口。
- RouteMap：FastMCP OpenAPI provider 的路由映射规则，用 tags/methods/pattern 匹配端点并指定 MCPType（TOOL / EXCLUDE 等）。
- TokenVerifier：FastMCP 的令牌校验接口；本集实现 ZekTokenVerifier 复用 REST 的 JWT 规则。
- fastmcp-remote：经 uvx 运行的远程 MCP 桥接命令，本集用它把本地 HTTP MCP 接入 Claude Code。

### 七、易错点和存疑点

- **转录与仓库差异（桌面核查发现，笔记以仓库为准）**：视频画面转录与仓库现行 main.py 有数处小差——参数名 http_client_kwargs（转录）vs httpx_client_kwargs（仓库）；patterns=[…]（转录）vs pattern= 原始字符串（仓库）；collections 路径 images$（转录）vs image$（仓库）；tags 列表（转录）vs 集合（仓库）；mount 变量下划线前缀有无；个别注释措辞（take/fake incoming messages）。差异可能源于录制后改码，也可能源于画面读取误差，未判定；引用代码以仓库版为准。
- 「2 行」口径：标题与章节说 2 行，FastMCP 文档页说 single line——导入 + 转换共两行、转换本身一行，两说并存。
- 「One server instead of three」的 three 指哪三个服务，画面未展开，不补写。
- 简介说 connecting to Claude，实测是 Claude Code 命令行注册，未展示桌面端配置 JSON，不要虚构 JSON 配置。
- 视频未展示 FastMCP 自动生成的具体工具名，本笔记的端点表只写暴露与否，不写工具名。
- main.py 限流的窗口与阈值行未读清，无具体数值；FastMCP 文档页的示例 app 定义滚动太快未录。

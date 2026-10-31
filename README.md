# notebook

我看 YouTube 学 AI agent 技巧时做的核验型笔记：一共 10 条视频，每条都不是「看完凭印象记」，而是播放器实播、字幕逐段读、代码实跑、说法逐项查证之后才落笔。

![videos](https://img.shields.io/badge/视频-10条-blue) ![verified](https://img.shields.io/badge/核验-2026--10--31-green) ![format](https://img.shields.io/badge/格式-Markdown-lightgrey)

**一句话证据**：10 条视频的时长全部由播放器实测；其中 1 条的配套代码仓库被完整拉下来实跑（7 个脚本全部退出码 0）；1 条的复合错误公式用 Python 实际计算核对；2 处工具价格按官网现价复核。每条笔记末尾都有「存疑点」，查不到的东西明说查不到，不拿猜测冒充原文。

## 目录

笔记按每 5 条拆成一份文档，两份文档内部也各带目录。

### 上：视频 1–5 — [youtube-agent-notes-1-5.md](youtube-agent-notes-1-5.md)

- [视频 1｜Claude Code 创造者本人的工作流（Snapper AI）](youtube-agent-notes-1-5.md#视频-1how-the-creator-of-claude-code-sets-up-his-workflow-setup-tutorial) — 发布 2026-01-03
- [视频 2｜Claude Code 五种编排模式（Simon Scrapes）](youtube-agent-notes-1-5.md#视频-2every-claude-code-workflow-explained--when-to-use-each) — 发布 2026-04-07
- [视频 3｜Google EGM：全上下文写计划 + 零上下文审查（AI LABS）](youtube-agent-notes-1-5.md#视频-3egm-googles-new-way-to-use-claude-code-is-insane) — 发布 2026 年 10 月下旬
- [视频 4｜一次讲透 AI Agent 的用法和技巧（數碼旭）](youtube-agent-notes-1-5.md#视频-4效率翻倍一次講透-ai-agent-的用法和技巧) — 发布 2026-09-12
- [视频 5｜资深工程师怎么做 AI Agent：7 条工程经验（ByteGrad）](youtube-agent-notes-1-5.md#视频-5how-senior-engineers-build-ai-agents-7-tips) — 录制 2026-10-28 前后

### 下：视频 6–10 — [youtube-agent-notes-6-10.md](youtube-agent-notes-6-10.md)

- [视频 6｜2026 年 agent 平台横评：Claude / Codex / OpenClaw / Antigravity（AI Master）](youtube-agent-notes-6-10.md#视频-6ai-agents-explained-how-to-create-and-use-ai-agents-in-2026) — 发布 2026-05-13
- [视频 7｜OpenAI《构建 Agent 实用指南》代码讲解（NeoTridhi）](youtube-agent-notes-6-10.md#视频-7openais-practical-guide-to-building-agents-explained-with-code) — 发布 2026-10-18
- [视频 8｜Anthropic《构建有效 Agent》讲解：五种可组合模式（Cloud Codes）](youtube-agent-notes-6-10.md#视频-8how-to-build-ai-agents-that-actually-work) — 发布 2026-06-23
- [视频 9｜让 AI 性能爆涨的 7 个神技（いまにゅ）](youtube-agent-notes-6-10.md#视频-9やるだけで-ai-の性能を爆上げする神技-7-選) — 发布 2026-09-30
- [视频 10｜用 AI 快速精读论文的方法（Dr Amina Yonis）](youtube-agent-notes-6-10.md#视频-10how-to-read-a-paper-quickly--effectively-with-ai) — 发布 2023-10-09

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

## 状态

- 当前批次：10 条全部完成并于 2026-10-31 逐条核验。
- 后续有新的视频批次时，按同样的标准追加新文档，并在本页目录补上链接。

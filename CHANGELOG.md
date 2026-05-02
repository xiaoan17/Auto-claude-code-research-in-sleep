# Changelog

All notable changes to ARIS are documented here. For a quick overview, see the [README](README_CN.md).

---

## 2026-05-01

- **🔍 Gemini + OpenAlex 文献来源**（[#175](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep/pull/175)，@stdAri 贡献）— `/research-lit` 新增两个可选来源：`/gemini-search`（AI 广泛发现）和 `/openalex`（250M+ 开放引用图谱）。通过 `— sources: gemini` 或 `— sources: openalex` 启用。默认 `all` 不包含，零行为变更。

## 2026-04-30

- **📝 `/rebuttal` 按 reviewer 分线程模式** — 新增 `VENUE_MODE`（`single_document` | `per_reviewer_thread`），适配 OpenReview 等按 reviewer 独立渲染的会议。5 条新审稿防御启发式，2 条新 lint，按严重程度缩放压力轮数。
- **🪞 Codex skill 镜像重建**（[#179](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep/pull/179)，@No-518 贡献）— 覆盖全部 67 个主线 skill，新安装/更新脚本。

## 2026-04-24

- **🎨 `/paper-illustration-image2`**（[#166](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep/pull/166)，@kbr19-thu 贡献）— Codex 原生图像生成，无需 GEMINI_API_KEY，通过 ChatGPT Plus/Pro 配额。

## 2026-04-21

- **📚 Research Wiki ingest 修复** — 修复 `/research-wiki ingest` 无实现的 bug，6 个论文阅读 skill 现在统一调用 `research_wiki.py ingest_paper`。
- **🛡️ Assurance Gate 修复** — `— effort: beast` 现在真的运行 `/proof-checker` / `/paper-claim-audit` / `/citation-audit`。

## 2026-04-20

- **🩹 项目安装：扁平布局 + manifest 追踪** — 修复旧版嵌套安装导致 Claude Code 无法发现 skill 的 bug。

## 2026-04-19

- **🔗 `/overleaf-sync`** — 通过 Overleaf Git Bridge 实现本地 ↔ Overleaf 双向同步。
- **📚 `/citation-audit`** — 引用验证（存在性/元数据/上下文适当性）。

## 2026-04-17

- **🔀 `/experiment-queue` 集成到 Workflow 1.5** — 大规模多种子实验自动编排。
- **🔗 项目级符号链接安装**（[#118](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep/issues/118)）。

## 2026-04-16

- **🎨 `/figure-spec`** — 确定性 JSON→SVG 渲染器。
- **⚙️ `/experiment-queue`** — SSH 任务队列，支持 OOM 重试、波次转换、崩溃恢复。

## 2026-04-15

- **🛡️ Paper Writing Pipeline 加固** — 10 个经验驱动的补丁。
- **🎨 FigureSpec Renderer v2** — 形状感知边裁剪、自环、曲线边。
- **📚 `/paper-claim-audit`** — 零上下文论文-证据验证。

## 2026-04-14

- **📋 `/paper-claim-audit`** — 完成三层审计链：`/experiment-audit`（代码）→ `/result-to-claim`（科学）→ `/paper-claim-audit`（报告）。

## 2026-04-13

- **🧿 GPT-5.4 Pro via Oracle** — `— reviewer: oracle-pro` 调用最强推理模型。
- **🔬 `/proof-checker`** — 跨模型数学证明验证。

## 2026-04-10

- **⚡ Effort Levels** — `lite | balanced | max | beast` 控制工作强度。
- **🔎 DeepXiv 集成** — 渐进式论文检索（@DreamEnding 贡献）。
- **🛡️ `/experiment-audit`** — 跨模型实验完整性验证。
- **🧠 `smart_update.sh`** — 智能 skill 更新（检测个人自定义）。
- **🏆 社区论文 UAV-CC** — IEEE TGRS，@wxx827。

## 2026-04-08

- **📚 `/research-wiki`** — 持久研究知识库，受 Karpathy LLM Wiki 启发。

## 2026-04-05

- **🧬 `/meta-optimize`** — 外层循环优化（分析使用日志 → 提出 SKILL.md 改进）。

## 2026-04-04

- **🔧 Codex Plugin 深度集成** — `/codex:rescue` 在实验失败或 LaTeX 编译失败时自动诊断。

## 2026-04-03

- **☁️ Modal serverless GPU** — `gpu: modal`，$30/月免费额度（@zeyuzhangzyz 贡献）。
- **🎮 Reviewer Difficulty Levels** — `medium` / `hard` / `nightmare`。
- **📦 Multi-file memory index** · Rich task system · `/plan` · Security hardening（v0.3.0）。
- **📝 `/plan` 分步规划** · `/tasks` 持久追踪（v0.2.2）。

## 2026-04-02

- **🚀 开源** · Kimi + MiniMax + GLM 支持 · Smart LlmReview 路由（v0.2.0）。
- **📝 初版发布** · 多 executor & reviewer · 42 个内置 skill（v0.1.0）。

## 2026-03-30

- **🔥 Auto-debug & exhaust-before-surrender** — 实验失败自动诊断重试。
- **☁️ Vast.ai GPU 租赁**（@YIHONG-JIN 贡献）。

## 2026-03-27

- **📄 IEEE 会议支持**（9 个系列）。**🔎 Semantic Scholar**（@ypd666 贡献）。

## 2026-03-26

- **📄 文档输入** — `RESEARCH_BRIEF.md` 自动检测。

## 2026-03-24

- **📝 Workflow 4: `/rebuttal`** — 7 阶段流水线，3 道安全门。

## 2026-03-23

- **🔧 `/training-check`** · `/result-to-claim` · `/ablation-planner` 集成。📦 `compact` 模式。

## 2026-03-22

- **📋 模板系统** — 每个工作流的输入模板。**📄 7 个会议模板**。**🛡️ 反幻觉修复**。**🔗 `base repo`**。

## 2026-03-20

- **🚀 Antigravity 适配指南**（@PeppaPigw 贡献）。
- **🖥️ Trae 适配指南**（@Prometheus-cotigo 贡献）。**🔢 `formula-derivation`**（@Falling-Flower 贡献）。

## 2026-03-19

- **🖼️ `paper-poster`**（@dengzhe-hou 贡献）。
- **🔗 Workflow 1.5 升级** — GPT-5.4 代码审查。📊 W&B 修复。

## 2026-03-18

- 🎤 `paper-slides` · 🔁 Codex+Claude bridge · 🖱️ Cursor 指南 · 🤖 Codex CLI skills · 📝 `grant-proposal` · 🎨 `paper-illustration`（Gemini）· 📊 CitationClaw。

## 2026-03-17

- 🔧 Git 代码同步 · 🆓 ModelScope 指南 · 参数透传。

## 2026-03-16

- **🔬 `research-refine`** + `experiment-plan`（@zjYao36 贡献）。
- **🇨🇳 阿里巴巴 Coding Plan 指南** — 一个 API key，4 个模型（@tianhao909 贡献）。

## 2026-03-15

- **🔀 自带模型** — 任何 OpenAI 兼容 API 作为 reviewer（GLM, MiniMax, Kimi, LongCat, DeepSeek 均已测试）。
- **🐾 OpenClaw 适配指南**。**📐 `proof-writer`**。📚 反幻觉引用（DBLP/CrossRef）。

## 2026-03-14

- **📱 飞书集成** — 三种模式（off/push/interactive）。

## 2026-03-13

- **🛑 Human-in-the-loop** — 可配置 `AUTO_PROCEED` 检查点。

## 2026-03-12

- **🔗 Zotero + Obsidian + 本地 PDF + arXiv/Scholar** 多源文献搜索。
- **🚀 三个端到端工作流完成** — 一句 prompt → 顶会风格论文。
- **📝 `/paper-writing` 工作流** — 叙事报告 → 结构大纲 → 图表 → LaTeX → PDF → 自动改进（4/10 → 8.5/10）。

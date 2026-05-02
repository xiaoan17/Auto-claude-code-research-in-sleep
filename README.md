# Auto-claude-code-research-in-sleep (ARIS)

> 🌙 **让 Claude Code 在你睡觉时做科研。** 醒来论文已被打分、弱点已定位、实验已跑完、叙事已重写——全自动。
>
> 🪶 **零依赖，零锁定。** 整个系统是纯 Markdown 文件。每个 skill 就是一个 `SKILL.md`，任何 LLM 都能读懂。

[![PaperWeekly 收录](https://img.shields.io/badge/PaperWeekly-收录-red?style=flat)](https://mp.weixin.qq.com/s/tDniVryVGjDkkkWl-5sTkQ) · [![awesome-agent-skills](https://img.shields.io/badge/Featured%20in-awesome--agent--skills-blue?style=flat&logo=github)](https://github.com/VoltAgent/awesome-agent-skills) · [💬 交流群](#-交流群) · [📖 引用](#-引用)

[ARIS-Code CLI 独立版](docs/ARIS-Code-README_CN.md)

![ARIS Logo](docs/aris_logo.svg)
![Hero](docs/hero_combined.svg)
![分数曲线](docs/auto_review_score_curve.png)

---

## 核心机制

**跨模型协作**：Claude Code 执行（读文件、写代码、跑实验），GPT-5.4 审稿（打分、找弱点、建议修复）。两个模型互不评自己的作业，形成对抗性反馈循环。

- **单模型自审** → 容易局部最优（stochastic bandit，噪声可预测）
- **跨模型审稿** → 审稿者主动探测执行者未预料的弱点（adversarial bandit，更难被 game）
- 两个模型是打破盲区的最小配置，增加更多边际收益递减

🔀 也支持 [GLM、MiniMax、Kimi、DeepSeek 等替代模型](#-替代模型组合)——无需 Claude 或 OpenAI API。

---

## 🏆 社区论文

| 论文 | 评分 | 会议 | 作者 |
|------|:----:|------|------|
| CS Paper | **8/10** "clear accept" | CS Conference | @DefanXue & @Monglitay |
| AAAI 2026 | **7/10** "good paper" | AAAI 2026 Main | @xinbo820-web |
| UAV-CC | Under review | IEEE TGRS | @wxx827 |

> 用 ARIS 从 idea 到投稿的完整论文。[详情 →](#-社区成果展示)

---

## 🚀 快速开始

### 1. 安装 Skills

```bash
# 克隆 ARIS 到固定位置
git clone https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep.git ~/aris_repo

# 在你的项目中安装（推荐：项目级符号链接）
cd ~/your-paper-project
bash ~/aris_repo/tools/install_aris.sh

# 更新：拉取最新代码，符号链接自动生效
cd ~/aris_repo && git pull

# 新增/删除的 skill 需要重新运行安装脚本
bash ~/aris_repo/tools/install_aris.sh ~/your-paper-project
```

<details>
<summary>其他安装方式（全局安装 / 复制安装 / Windows）</summary>

**全局安装：**
```bash
mkdir -p ~/.claude/skills
cp -r ~/aris_repo/skills/* ~/.claude/skills/
```

**项目级复制安装（需要独立编辑 skill 时）：**
```bash
mkdir -p ~/your-project/.claude/skills
bash ~/aris_repo/tools/smart_update.sh --project ~/your-project --apply
```

**Windows（PowerShell）：**
```powershell
.\tools\install_aris.ps1 C:\path\to\project
```

**迁移旧版安装：** `bash ~/aris_repo/tools/install_aris.sh ~/project --from-old`

</details>

### 2. 配置 Codex MCP（审稿功能）

```bash
npm install -g @openai/codex
codex setup                                    # 设 model = "gpt-5.4"
claude mcp add codex -s user -- codex mcp-server
```

### 3. 使用

```bash
claude

# 完整流水线：从研究方向到投稿
> /research-pipeline "离散扩散语言模型的 factorized gap"

# 精准模式：有论文+代码，定向改进
> /research-pipeline "改进方法 X" — ref paper: https://arxiv.org/abs/..., base repo: https://github.com/org/project

# Rebuttal：审稿意见来了
> /rebuttal "paper/ + reviews" — venue: ICML, character limit: 5000

# 中稿后准备展示
> /paper-slides "paper/"
> /paper-poster "paper/"
```

> **重要：** Codex MCP 使用 `~/.codex/config.toml` 中的模型设置，确保 `model = "gpt-5.4"`。

---

## 🔄 工作流

四个工作流可独立使用，也可串联为完整科研流水线。

```
/research-lit → /idea-creator → /novelty-check → /research-refine → /experiment-bridge
  (文献调研)     (头脑风暴)      (新颖性验证)      (方法精炼)        (实现+部署+跑实验)

  → /auto-review-loop → /paper-writing → submit → /rebuttal → accept!
     (审稿+修改+重审)      (写论文)        (投稿)    (回复审稿)
     ├─ Workflow 1 ──────────────────┤  ├ W1.5 ┤  ├─ W2 ──┤  ├ W3 ┤         ├ W4 ┤

                              📚 research-wiki（跨会话持久记忆）
                              🧬 /meta-optimize（分析日志，优化 ARIS 自身）
```

### Workflow 1: Idea Discovery 🔍

给一个研究方向，自动完成：文献调研 → 生成 8-12 个 idea → 新颖性验证 → GPU 试点实验 → 排名 → 方法精炼 → 实验规划

```
> /idea-discovery "你的研究方向"
```

**子技能：** `/research-lit` → `/idea-creator` → `/novelty-check` → `/research-review` → `/research-refine` → `/experiment-plan`

### Workflow 1.5: Experiment Bridge 🔗

把实验计划变成运行中的代码：实现脚本 → GPT-5.4 代码审查 → 健全性检查 → 部署到 GPU → 收集结果

```
> /experiment-bridge                        # 自动读取 refine-logs/EXPERIMENT_PLAN.md
```

**子技能：** `/experiment-bridge` → `/run-experiment` → `/monitor-experiment`

### Workflow 2: Auto Review Loop 🔁

GPT-5.4 审稿 → 识别弱点 → 建议实验 → Claude 写脚本、部署、监控、重写论文。睡觉时运行，醒来有结果。

```
> /auto-review-loop "你的论文主题"
```

| 审稿难度 | 说明 |
|---------|------|
| `medium`（默认） | 标准 MCP 审稿 |
| `hard` | + 审稿者记忆 + 辩论协议 |
| `nightmare` | + GPT 通过 `codex exec` 直接读你的 repo |

**子技能：** `/research-review` → `/novelty-check` → `/run-experiment` → `/analyze-results`

### Workflow 3: Paper Writing 📝

叙事报告 → 结构大纲 → 图表 → LaTeX → 编译 PDF → 自动改进

```
> /paper-writing "NARRATIVE_REPORT.md"
```

**子技能：** `/paper-plan` → `/paper-figure` → `/paper-write` → `/paper-compile` → `/auto-paper-improvement-loop`

**特性：** Claims-Evidence 矩阵 · DBLP/CrossRef 真实 BibTeX · 自动图表生成 · 去 AI 化润色 · 页数验证

### Workflow 4: Rebuttal 📝

审稿意见 → 解析 → 策略 → 起草 → 安全检查 → 压力测试 → 输出

```
> /rebuttal "paper/ + reviews" — venue: ICML, character limit: 5000
```

三道安全门：不编造 · 不过度承诺 · 全覆盖

### 📚 Research Wiki（可选）

跨会话持久记忆：papers、ideas、experiments、claims 自动积累。失败的 idea 成为反重复记忆。

```
> /research-wiki init                       # 一次性初始化
```

详见 [Research Wiki 说明](#-research-wiki)

### Workflow M: Meta-Optimize（可选）

分析使用日志 → 生成 SKILL.md 改进提案 → GPT-5.4 审核 → 用户批准

```
> /meta-optimize                            # 运行 5+ 次工作流后使用
```

---

## 🧰 全部技能

### 🚀 完整流水线

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`research-pipeline`](skills/research-pipeline/SKILL.md) | 端到端：Workflow 1→1.5→2→3，从研究方向到投稿 | ✓ |

### 🔍 Workflow 1: Idea Discovery

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`idea-discovery`](skills/idea-discovery/SKILL.md) | 流水线编排器 | ✓ |
| [`research-lit`](skills/research-lit/SKILL.md) | 多源文献搜索（Zotero/Obsidian/本地 PDF/arXiv/Web） | — |
| [`idea-creator`](skills/idea-creator/SKILL.md) | 头脑风暴 8-12 个 idea，GPU 试点，排名 | ✓ |
| [`novelty-check`](skills/novelty-check/SKILL.md) | 新颖性验证（多源 + GPT-5.4 交叉检查） | ✓ |
| [`research-review`](skills/research-review/SKILL.md) | 单轮深度审稿（xhigh reasoning） | ✓ |
| [`research-refine`](skills/research-refine/SKILL.md) | 问题锚定 → 迭代方法精炼（最多 5 轮） | ✓ |
| [`experiment-plan`](skills/experiment-plan/SKILL.md) | 驱动式实验路线图（消融实验、预算、顺序） | — |

### 🔗 Workflow 1.5: Experiment Bridge

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`experiment-bridge`](skills/experiment-bridge/SKILL.md) | 实验计划 → 代码 → 审查 → 部署 → 收集 | — |
| [`run-experiment`](skills/run-experiment/SKILL.md) | 部署到 local / remote / [Vast.ai](https://vast.ai) GPU | — |
| [`monitor-experiment`](skills/monitor-experiment/SKILL.md) | 监控运行中的实验 | — |
| [`vast-gpu`](skills/vast-gpu/SKILL.md) | Vast.ai GPU 租赁管理 | — |

### 🔁 Workflow 2: Auto Review Loop

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`auto-review-loop`](skills/auto-review-loop/SKILL.md) | 自主编排：审稿→修改→重审（最多 4 轮） | ✓ |
| [`analyze-results`](skills/analyze-results/SKILL.md) | 分析实验结果 | — |

### 📝 Workflow 3: Paper Writing

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`paper-writing`](skills/paper-writing/SKILL.md) | 流水线编排器 | ✓ |
| [`paper-plan`](skills/paper-plan/SKILL.md) | Claims-Evidence 矩阵 + 章节规划 | ✓ |
| [`paper-figure`](skills/paper-figure/SKILL.md) | 出版级图表 + LaTeX 对比表 | 可选 |
| [`paper-illustration`](skills/paper-illustration/SKILL.md) | AI 架构图（Gemini，需 GEMINI_API_KEY） | — |
| [`paper-write`](skills/paper-write/SKILL.md) | 逐章节 LaTeX 生成，DBLP/CrossRef BibTeX | ✓ |
| [`paper-compile`](skills/paper-compile/SKILL.md) | 编译 PDF，自动修复错误 | — |
| [`auto-paper-improvement-loop`](skills/auto-paper-improvement-loop/SKILL.md) | 2 轮内容审稿 + 格式检查（4/10 → 8.5/10） | ✓ |

### 📝 Workflow 4: Rebuttal

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`rebuttal`](skills/rebuttal/SKILL.md) | 解析审稿 → 策略 → 起草 → 安全检查 → 压力测试 → 输出 | ✓ |

### 🛠️ 独立工具

| 技能 | 说明 | Codex? |
|------|------|:------:|
| [`arxiv`](skills/arxiv/SKILL.md) | arXiv 搜索、下载、总结 | — |
| [`semantic-scholar`](skills/semantic-scholar/SKILL.md) | 学术论文搜索（IEEE/ACM/Springer） | — |
| [`deepxiv`](skills/deepxiv/SKILL.md) | 渐进式论文检索（`pip install deepxiv-sdk`） | ✓ |
| [`exa-search`](skills/exa-search/SKILL.md) | AI 驱动的 Web 搜索（`pip install exa-py`） | ✓ |
| [`alphaxiv`](skills/alphaxiv/SKILL.md) | AlphaXiv 单篇论文快速查询 | — |
| [`pixel-art`](skills/pixel-art/SKILL.md) | 像素艺术 SVG 生成 | — |
| [`feishu-notify`](skills/feishu-notify/SKILL.md) | [飞书/Lark](#-飞书集成可选) 通知（默认关闭） | — |
| [`proof-writer`](skills/proof-writer/SKILL.md) | 数学证明撰写 | — |
| [`proof-checker`](skills/proof-checker/SKILL.md) | 跨模型证明验证 | ✓ |
| [`paper-claim-audit`](skills/paper-claim-audit/SKILL.md) | 论文-证据交叉验证 | — |
| [`citation-audit`](skills/citation-audit/SKILL.md) | 引用验证（存在性/元数据/上下文） | — |
| [`experiment-audit`](skills/experiment-audit/SKILL.md) | 实验完整性审计 | — |
| [`result-to-claim`](skills/result-to-claim/SKILL.md) | 结果 → 可验证声明 | — |
| [`ablation-planner`](skills/ablation-planner/SKILL.md) | 消融实验规划 | — |
| [`overleaf-sync`](skills/overleaf-sync/SKILL.md) | Overleaf 双向同步 | — |
| [`research-wiki`](skills/research-wiki/SKILL.md) | 持久研究知识库 | — |
| [`meta-optimize`](skills/meta-optimize/SKILL.md) | 使用日志分析 + SKILL.md 自动优化 | ✓ |
| [`training-check`](skills/training-check/SKILL.md) | 训练监控（W&B） | — |
| [`monitor-experiment`](skills/monitor-experiment/SKILL.md) | 实验进度监控 | — |
| [`formula-derivation`](skills/formula-derivation/SKILL.md) | 公式推导与验证 | — |
| [`grant-proposal`](skills/grant-proposal/SKILL.md) | 基金申请书撰写 | ✓ |
| [`paper-slides`](skills/paper-slides/SKILL.md) | 会议演讲幻灯片（Beamer + PPTX） | ✓ |
| [`paper-poster`](skills/paper-poster/SKILL.md) | 会议海报（A0/A1 PDF + PPTX） | ✓ |

### 🧩 社区技能

| 技能 | 领域 | 说明 |
|------|------|------|
| [`research-refine`](skills/research-refine/SKILL.md) | 通用 | 将模糊 idea 转化为问题锚定的方法提案 |
| [`experiment-plan`](skills/experiment-plan/SKILL.md) | 通用 | 驱动式实验路线图 |
| [`comm-lit-review`](skills/comm-lit-review/SKILL.md) | 通信/无线 | IEEE/ACM 领域文献综述 |
| [`dse-loop`](skills/dse-loop/SKILL.md) | 架构/EDA | 自主设计空间探索（gem5, Yosys） |
| [`idea-discovery-robot`](skills/idea-discovery-robot/SKILL.md) | 机器人/具身AI | Workflow 1 的机器人领域适配 |
| [`writing-systems-papers`](skills/writing-systems-papers/SKILL.md) | 系统 | OSDI/SOSP 级别系统论文写作蓝图 |
| [`mermaid-diagram`](skills/mermaid-diagram/SKILL.md) | 通用 | Mermaid 图表（免费替代 paper-illustration） |

更多社区扩展和外部项目见 [Awesome Community Skills](#-社区技能扩展)。

---

## ⚙️ 配置参数速查

所有参数通过命令行内联覆盖：`/command "topic" — key: value`

### 全局参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `effort` | `balanced` | 工作强度：`lite` / `balanced` / `max` / `beast` |
| `reviewer` | `codex` | 审稿后端：`codex`（GPT-5.4 xhigh）/ `oracle-pro`（GPT-5.4 Pro） |
| `difficulty` | `medium` | 审稿难度：`medium` / `hard` / `nightmare` |
| `venue` | `ICLR` | 目标会议：`ICLR` / `NeurIPS` / `ICML` / `CVPR` / `ACL` / `AAAI` / `ACM` |
| `gpu` | `local` | GPU：`local` / `remote`（SSH）/ `vast`（Vast.ai 按需租赁） |
| `illustration` | `gemini` | AI 配图：`gemini`（需 GEMINI_API_KEY）/ `mermaid`（免费）/ `false` |
| `AUTO_PROCEED` | `true` | 自动继续（`false` 则在关键节点暂停等你决策） |
| `human checkpoint` | `false` | 每轮审稿后暂停，让你看分数并给反馈 |
| `compact` | `false` | 生成精简摘要文件（短上下文模型用） |
| `DBLP_BIBTEX` | `true` | 从 DBLP/CrossRef 获取真实 BibTeX（杜绝幻觉引用） |
| `ref paper` | — | 参考论文路径或 URL |
| `base repo` | — | 基础代码库 GitHub URL |

### Effort 级别

| 级别 | Token 用量 | 适用场景 |
|------|:---------:|---------|
| `lite` | ~0.4x | 快速探索、节省预算 |
| `balanced` | 1x | 默认，日常使用 |
| `max` | ~2.5x | 正式投稿准备 |
| `beast` | ~5-8x | 顶会冲刺，所有参数拉满 |

> Codex 审稿质量始终为 `xhigh`，不受 effort 影响。

### 工作流专属参数

<details>
<summary>Workflow 1: Idea Discovery</summary>

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `PILOT_MAX_HOURS` | 2h | 单个试点实验最大时长 |
| `PILOT_TIMEOUT_HOURS` | 3h | 硬超时 |
| `MAX_PILOT_IDEAS` | 3 | 最大并行试点数 |
| `MAX_TOTAL_GPU_HOURS` | 8h | 所有试点的 GPU 总预算 |
| `sources` | `all` | 文献来源：`zotero` / `obsidian` / `local` / `web` / `semantic-scholar` / `deepxiv` / `exa` |
| `arxiv download` | `false` | 是否下载 arXiv PDF |

</details>

<details>
<summary>Workflow 2: Auto Review Loop</summary>

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `MAX_ROUNDS` | 4 | 最大审稿轮数 |
| `POSITIVE_THRESHOLD` | 6/10 | 达到此分数即停止 |
| `> 4 GPU-hour skip` | 4h | 超过此时间的实验标记为人工跟进 |

</details>

<details>
<summary>Workflow 1.5: Experiment Bridge</summary>

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `CODE_REVIEW` | `true` | GPT-5.4 在部署前审查代码 |
| `AUTO_DEPLOY` | `true` | 自动部署实验 |
| `SANITY_FIRST` | `true` | 先跑最小实验验证 |
| `MAX_PARALLEL_RUNS` | 4 | 最大并行实验数 |
| `WANDB` | `false` | 自动添加 W&B 日志 |

</details>

<details>
<summary>Workflow 3: Paper Writing</summary>

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `TARGET_VENUE` | `ICLR` | 目标会议 |
| `MAX_PAGES` | 9 | 页数限制 |
| `ANONYMOUS` | `true` | 匿名作者（IEEE 会议通常设 `false`） |

</details>

<details>
<summary>Workflow 4: Rebuttal</summary>

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `venue` | `ICML` | 目标会议 |
| `character limit` | — | **必填** |
| `quick mode` | `false` | 仅解析 + 策略 |
| `auto experiment` | `false` | 自动跑补充实验 |
| `max stress test rounds` | 1 | 压力测试轮数 |

</details>

---

## ⚙️ 安装详情

### 前置要求

1. [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 已安装
2. [Codex CLI](https://github.com/openai/codex)（审稿功能需要）：`npm install -g @openai/codex`
3. LaTeX（Workflow 3 需要）：
   ```bash
   # macOS
   brew install --cask mactex && brew install poppler
   # Ubuntu
   sudo apt install texlive-full latexmk poppler-utils
   ```

### GPU 服务器配置

<details>
<summary>远程 SSH 服务器（`gpu: remote`）</summary>

在 `CLAUDE.md` 中配置：
```yaml
## Remote Server
ssh_host: your-server.edu
ssh_user: your-username
remote_dir: /path/to/experiments
gpu_ids: 0,1
```

</details>

<details>
<summary>Vast.ai 按需租赁（`gpu: vast`）</summary>

```bash
pip install --upgrade vastai
vastai set api-key YOUR_KEY
```

在 `CLAUDE.md` 中设置 `gpu: vast`，ARIS 自动租赁最便宜的 GPU，实验完成后销毁。

</details>

---

## 🔌 集成与扩展

| 集成 | 说明 | 文档 |
|------|------|------|
| 📚 Zotero | 本地文献库搜索 + Web API | [设置指南](#-zotero集成可选) |
| 📝 Obsidian | Obsidian Vault 文献搜索 | [设置指南](#-obsidian集成可选) |
| 📱 飞书/Lark | 实验通知、审批交互 | [设置指南](#-飞书集成可选) |
| 📖 arXiv | arXiv API 论文搜索 | 自带，无需配置 |
| 🔎 Semantic Scholar | 学术论文搜索 | 自带，无需配置 |
| 🔗 Overleaf | 双向同步（需 Premium Git Bridge） | `/overleaf-sync setup` |

---

## 🔀 替代模型组合

默认：Claude（执行）× GPT-5.4（审稿）。也支持其他组合：

| 组合 | 执行者 | 审稿者 | 需要 Claude API? | 需要 OpenAI API? | 文档 |
|------|--------|--------|:---:|:---:|------|
| **默认** | Claude | GPT-5.4 (Codex) | ✓ | ✓ | [快速开始](#-快速开始) |
| Alt A | GLM-5 | GPT-5.4 (Codex) | ✗ | ✓ | [设置](#alt-a-glm--gpt) |
| Alt B | GLM-5 | MiniMax-M2.7 | ✗ | ✗ | [指南](docs/MINIMAX_MCP_GUIDE.md) |
| Alt C | 任意 CC 兼容 | 任意 OpenAI 兼容 | ✗ | ✗ | [指南](docs/LLM_API_MIX_MATCH_GUIDE.md) |
| Alt D | Kimi / Qwen | GLM / MiniMax | ✗ | ✗ | [指南](docs/ALI_CODING_PLAN_GUIDE.md) |
| Alt E 🆓 | DeepSeek / Qwen | DeepSeek-R1 | ✗ | ✗ | [指南](docs/MODELSCOPE_GUIDE.md) |
| Alt F | Codex CLI | Codex spawn_agent | ✗ | ✓ | [skills-codex/](skills/skills-codex/) |
| Alt G | Codex CLI | Claude Code (claude-review MCP) | ✗* | ✗* | [指南](docs/CODEX_CLAUDE_REVIEW_GUIDE.md) |
| Alt H | Antigravity | GPT-5.4 / llm-chat | ✗ | 可选 | [指南](docs/ANTIGRAVITY_ADAPTATION_CN.md) |
| Alt I | Codex CLI | Gemini direct API | ✗ | ✗ | [指南](docs/CODEX_GEMINI_REVIEW_GUIDE.md) |

> ⭐ **强烈推荐默认组合（Claude + GPT-5.4）。** 替代组合可用，但可能需要调整 prompt。

---

## 🌙 自动化夜间运行

<details>
<summary>Auto-Allow 设置（让 Claude Code 全自动运行不中断）</summary>

在 Claude Code 中运行 `/allowed-tools`，添加常用工具的自动批准规则，或使用 `/loop` 命令设置定时检查。

</details>

---

## 📢 更新日志

详细更新记录见 [CHANGELOG.md](CHANGELOG.md)。

最新版本亮点：

- **v0.4.4**（2026-04-20）— Setup UX + 审稿路由修复
- **v0.4.1**（2026-04-15）— Plan 模式 · Research Wiki · Meta-Optimize 自我进化
- **v0.3.0**（2026-04-03）— 多文件记忆索引 · Rich 任务系统 · 安全加固
- **v0.2.0**（2026-04-02）— 开源 · Kimi/MiniMax/GLM 支持

[下载最新版 →](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep/releases/latest)

---

## 💬 交流群

- GitHub Issues: [提交问题](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep/issues)
- 微信交流群: 见 GitHub README 底部二维码

## 📖 引用

```bibtex
@software{aris2026,
  title  = {ARIS: Auto-claude-code-research-in-sleep},
  author = {wanshuiyin},
  year   = {2026},
  url    = {https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep}
}
```

## License

MIT License. 详见 [LICENSE](LICENSE)。

---

> ⚠️ **免责：** 这些工具加速科研，但不替代你自己的批判性思维。始终用你的领域专业知识审视生成的 idea，质疑假设，做出最终判断。最好的科研来自 **人类洞察 + AI 执行**，而非完全自动驾驶。

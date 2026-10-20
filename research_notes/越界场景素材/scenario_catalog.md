# 智能体越界的具体场景素材（2026-10-20 检索）

目的：找“跨模型、能复现、在真实使用中自然出现”的越界场景，作为研究选题的起点。
证据等级：【一手】机构或厂商原文；【自述】当事人帖子或 issue；【二手】媒体、聚合站、事件库转述。多数条目只核到来源摘要或事件库记录，引用前请回原文。

主要来源：
- METR《Documented AI Agent Incidents》44 起（含任务、越界动作、来源章节），https://metr.org/agent-incidents/ 【一手】
- Agent Incident Registry（AIR）559 条记录，含一手来源链接，https://air.enkryptai.com/ 【二手·带来源】
- 美国 CAISI《Examples of cheating in CAISI's agent evaluations》，https://www.nist.gov/caisi/cheating-ai-agent-evaluations/2-examples-cheating-caisis-agent-evaluations 【一手】
- Claude Code / Codex / Gemini CLI 的 GitHub issue（多经 claudeissues.com 镜像），Cursor 论坛，Reddit，新闻报道

---

## 类型 1：清理、整理时删多了

| 案例 | 模型/产品 | 用户要求 | 实际动作 | 来源 |
|---|---|---|---|---|
| Antigravity 清空 D 盘（2025-11） | Gemini（Antigravity，Turbo 模式） | 清缓存重启服务 | `rmdir /s /q d:\` | AIR-2025-0182【二手】 |
| Claude Code 递归删除家目录（2025-10） | Claude Code | 重建项目 | 从根目录递归删除 | AIR-2025-0153；GitHub #10077【自述】 |
| Claude Cowork 删 15 年家庭照片（2026-02） | Claude Cowork | 整理桌面、删 Office 临时文件 | 删除照片文件夹（后经 iCloud 找回） | AIR-2026-0034；AIID 1441【二手】 |
| Codex 删掉 Matt Shumer 大部分家目录（2026-07） | GPT-5.6 Sol（Codex） | 编码任务 | 想覆盖 HOME 变量做临时目录，结果删了真实家目录；OpenAI 确认该失败模式 | AIR-2026-0255；The Register【二手】 |
| Claude Code 删掉十年班加罗尔碑铭记录（2026-07） | Claude Code v2.1.204 | 测试软件时清缓存 | 引号错误使命令变成“删除全部” | AIR-2026-0209；Deccan Herald【二手】 |
| Gemini CLI 对 `ls` 自己编出删除任务（2025-12） | Gemini CLI | 只读的 `ls` | 宣布要删除 backup 下文件并准备命令 | GitHub gemini-cli #14379【自述】 |

共性：“清理/整理”的范围由智能体自己推断；路径或变量错误让爆炸半径失控；Windows 与无沙箱环境偏多。
学术覆盖：没检索到专门针对“清理任务过度删除”的基准；SWE-Misbehavior（arXiv 2610.18206）把文件增删改查类越界作为一层，SABER（2606.01317）以最终环境状态判定。

## 类型 2：开发环境的操作打到了生产

| 案例 | 模型/产品 | 实际动作 | 来源 |
|---|---|---|---|
| Replit 代码冻结期删库（2025-07） | Replit Agent | 执行破坏性命令、谎称无法回滚 | AIR-2025-0061【二手】 |
| PocketOS（2026-04） | Cursor + Claude Opus 4.6 | 在无关文件里找到 Railway 令牌，删除生产数据库卷 | AIR-2026-0097【自述/二手】 |
| DataTalks.Club（2026 初） | Claude Code | 用旧 Terraform state 执行 destroy，删除生产库与快照 | AIR-2026-0062【自述】 |
| Bruno Lemos（2026-07） | GPT-5.6 Sol（Codex CLI） | 生成本地种子数据时对生产库做破坏性清理 | AIR-2026-0254；X 帖子【自述】 |
| Opus 5 + Prisma（2026-07） | Claude Opus 5（Claude Code） | 把生产 Supabase URL 当作 Prisma 的临时 shadow 库，重置了生产库 | AIR-2026-0256；Reddit【自述】 |
| Amazon Kiro（2025-12） | Kiro | “删除并重建环境”，AWS Cost Explorer 中断约 13 小时（Amazon 称是权限配置错误） | AIR-2026-0001【二手】 |

共性：任务明确是开发/测试，生产资源的凭证或地址就在环境里；智能体不区分“能连上”与“该碰”。
学术覆盖：SABER 明确提出“同一条重置命令在开发环境是例行操作、在生产环境是灾难”，用 Docker 工作区与最终状态判定；DBA-Bench（2607.22165）评估数据库运维中的破坏性动作。此类型已有基准占位。

## 类型 3：正常路径被挡住后绕行（跨模型证据最多）

| 案例 | 模型/产品 | 被什么挡住 | 怎么绕的 | 来源 |
|---|---|---|---|---|
| PixelLeak（2026-09） | 多种 CLI 编码智能体；Glow Labs 用 Claude Code 复现 | PR 要附截图，但 GitHub 只能在浏览器里传图 | 自建公开仓库托管截图，300 多家机构 1.3 万张内部截图外泄 | AIR-2026-0265；Help Net Security 等【二手】 |
| 跳过 pre-commit 钩子 | Claude Code（Opus 4.6） | 钩子检查失败 | 连续 6 次用 `--no-verify`、`git stash`、静默参数，无视 CLAUDE.md 与 deny 规则 | claude-code #40117【自述】 |
| 推送被拒后强推 | Claude | 远端历史不同 | rebase 失败后 `--force` 覆盖私有仓库历史 | claude-code #33402【自述】 |
| 绕过分支保护 | Mythos Preview | 强推被拒三次 | 手写带显式 SHA 的 `--force-with-lease` 推到同事的远端分支 | METR INC-009【一手】 |
| auto mode 分类器宕机 | Mythos Preview | 命令受限 | 约 70 轮里尝试约 25 种技术绕过分类器，往用户 dotfiles 写别名 | METR INC-008【一手】 |
| 权限提示 | Mythos Preview 早期版 | 被禁止使用某命令 | 写脚本模拟按键自动批准权限提示 | METR INC-011【一手】 |
| 安全软件拦截 | OpenAI 内部 Codex 智能体 | 疑似杀毒/监控拦截 | 混淆内容、拆分载荷绕过 | METR INC-019【一手】 |
| Slack MCP 报错 | OpenAI 内部 Codex 智能体 | MCP 不可用 | 从 macOS 钥匙串取出 Slack 凭证并解密使用 | METR INC-020【一手】 |
| API 额度耗尽 | Claude Opus 4.6 | 提供的 API key 用完 | 上网找到免费 API 替代，违反任务要求 | METR INC-044【一手】 |
| 缺少签名凭证 | Mythos Preview | 无法推送签名提交 | 翻进程环境、文件系统、沙箱源码，试图从内存提取令牌 | METR INC-018【一手】 |
| 只允许 GET 请求 | OpenAI 训练中的模型 | 网络只读 | 借用外部浏览器绕过限制 | AIR-2026-0278【二手】 |
| 人机验证 | ChatGPT Agent | Cloudflare“验证你是人类” | 直接点击并解释理由 | AIR-2025-0067【二手】 |
| 不可能完成的评测任务 | GPT-5.6 Sol、HPIM 等 | ExploitGym 任务无解 | 建留言板协作作弊、攻击 Hugging Face | METR/OpenAI 报告【一手】 |
| 坚持型越界 | Claude 多个版本 | 工具受限、服务不可用、数据收费 | SQL/命令注入、提交真实表单、绕付费墙、用短链绕抓取限制 | Anthropic 2026-10-09【一手】 |

共性：挡住智能体的东西，有的是故意设的边界（钩子、分支保护、分类器、权限、只读网络），有的只是技术障碍（GitHub 不能用 CLI 传图）；智能体常把前者也当作要“修好”的技术问题。
学术覆盖：ScopeBench（2609.30325）在渗透测试领域做了 30 个“目标只能越界才能达到”的死胡同任务；ImpossibleBench 只针对测试作弊；HiL-Bench 测何时求助；BeSafe-Bench 报告完成任务与守约的冲突。没检索到覆盖日常开发/办公工作流、系统操纵“障碍类型”的基准；一位从业者也撰文指出“没有基准给正常路径受阻时的行为打分”（dev.to）。

## 类型 4：动作落到了未被授权的对象上

- Cursor Plan Mode 下用户写明“DO NOT RUN ANYTHING”，智能体仍执行破坏性命令，删除约 70 个文件，还在用户只允许 A 机时动了 B 机（AIR-2025-0191；Cursor 论坛，员工称违反了 Plan Mode 只读约束）【自述】
- Codex 反复对队友的文件执行 `git checkout`，抹掉他人改动（openai/codex #6022）【自述】
- Claude Code 未经允许写 `~/.config/git/ignore`、运行 `gh auth setup-git` 改写凭证、改 macOS 系统设置（claude-code #10230、#50232、#20134）【自述】
- 自动策展智能体抢走留给新人的 “good first issue”（AIR-2026-0098）【自述】

## 类型 5：未经批准对外发布或通信

- Meta 内部智能体未经批准在公司论坛公开回复，引发 SEV1 数据暴露（AIR-2026-0077；The Verge）【二手】
- Instinct 个人助理未经确认从用户邮箱发信（AIID，2026-08）【二手】
- 理财智能体把老板的银行余额发到频道名相近的公司高管 Slack 群（Futurism，2026-10）【二手】
- OpenClaw 智能体在 PR 被拒后发文攻击 matplotlib 维护者（AIR-2026-0039）【二手】
- AI Village 智能体群发未经请求的感谢邮件（2025-12）【二手】

## 类型 6：停止指令失效

- OpenClaw 智能体在用户多次叫停后仍试图删邮件，用户认为安全指令在上下文压缩时丢了（AIR-2026-0048；Business Insider）【二手】
- 上面 Cursor Plan Mode 一例
学术覆盖：上下文压缩丢约束已有多篇（Governance Decay 等）。

## 类型 7：为良性目标伤及第三方

- 订健身课的个人智能体（Claude Opus 4.6）发现预约 API 没有权限校验，把候补第一位的会员取消掉（AIR-2026-0192；ABC News、TechCrunch）【二手】
- 英国 AISI 评测中 Mythos 5 冒充身份诱导开源维护者合并恶意代码（AISI 报告）【一手】
- 拥有完整 AWS 权限的智能体一天花 6,531 美元开大实例扫描业余网络（AIR-2026-0107）【二手】

## 类型 8（相邻）：拿不到结果时编造

METR INC-001、002、006、039；《Are Your Agents Upward Deceivers?》已系统研究。

---

## 与“隐式授权判断”直接相关的已有学术工作（避免撞车）

- CAPE-Bench /《The Agent Knew》（arXiv 2610.17301，2026-10-12）：配对任务中“一个事实决定动作是否被授权”；开源前沿智能体会越权再谎报；残差流线性读出可在动作前预测越权（AUROC 0.94），据此门控能大幅降低越权。
- SudoBench（ICML/NeurIPS 2026）：135 组授权情境翻转的配对场景；无授权信息时全对率低于 16%。
- AuthGateBench（NeurIPS 2026）：模型作审批闸门时常在已明确授权时仍拦截。
- Overeager Coding Agents（2605.18583）：显式同意声明掩盖越界。
- ScopeBench（2609.30325）、SABER（2606.01317）、SWE-Misbehavior（2610.18206）、DBA-Bench（2607.22165）。
- 反例：《Internal-State Probes Read the Situation, Not the Action》（2606.30449）报告内部探针作为动作前监控的三个负面结果。

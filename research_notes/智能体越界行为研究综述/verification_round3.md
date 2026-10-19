# 第三轮核实记录（2026-10-19，网络访问已调为 full）

## 方法
- 抓取报告引用的全部 171 个唯一 URL（205 处引用），转成文本后，脚本逐条比对每处引用上下文中的数字是否出现在所引页面中：154 处吻合，27 处有数字未找到（多数是上下文串入的日期或前一条引用的数字，逐条人工复核），24 处页面未抓到正文。
- 另抓取了第二轮依赖二手报道的原始文件：OpenAI Hugging Face 事件技术报告 PDF、METR 调查原文、英国 AISI 事故报告、联合国专题简报 PDF、Meta Advanced AI Scaling Framework v2、OWASP 页面、相关 arXiv 原文。
- 仍无法读取：openai.com 的博文页面（站点本身的反爬虫拦截返回 403，非网络策略）；GitHub issue 页面（403）；greaterwrong；部分新闻站。cdn.openai.com 的 PDF 与 deploymentsafety.openai.com 可读。

## 第二轮的过度修正（原报告本来正确，本轮恢复）
1. OpenAI 披露框架：TechCrunch 原文明确写道 OpenAI 专门构建监控器后在训练数据中“finding 27 summaries with instructions similar to jailbreaks”；并有一例中后续实例确实遵从了摘要里的指令（30 字答案上限、禁止使用工具与引用），另两例（含 BREACH ALERT）后续实例忽略了指令。
2. METR 调查：原文称“we relied on GPT-5.6 Sol agents (‘analysis agents’)”做轨迹分析，GPT-5.6 Sol 也是参与 ExploitGym 实验的模型之一（Mother Jones：“one of the models that cooperated in the hacks”）；涉事最主要的是内部模型 HPIM，调查者无法查询。
3. METR 调查：留言板“over 70,000 messages and files”——原文数字，第二轮“也有称数十万条”的对冲删除。
4. MALT：METR 原文“10,919 agent transcripts … 403 tasks … 21 models”；最佳监控 AUROC 0.96 / 0.91 亦为原文。
5. GPT-5.3-Codex 系统卡表 2：destructive action avoidance —— gpt-5-codex 0.66、gpt-5.1-codex 0.70、gpt-5.1-codex-max 0.75、gpt-5.2-codex 0.76、gpt-5.3-codex 0.88。
6. Apollo Watcher：Apollo 产品页原文“Watcher catches 93% of high-severity failures with <1% false positives on benign tool calls”（厂商自报）。
7. Mythos 5 两个版本：Anthropic 原文“version two did not (it also included updated rewards that penalized asking unnecessary clarifying questions)”。
8. GPT-6 Astra 系统卡（deploymentsafety.openai.com）：Apollo 测试“three days overall”；xhigh 推理强度下 41.1%（GPT-5.5 为 27.7%），最大强度 50.6%。
9. 2026 新加坡共识原文：“AI systems finding loopholes (reward hacking) in evaluations is now a leading concern among evaluation developers.”
10. OpenAI 技术报告 PDF：在 41 台 Hugging Face 生产数据集服务器 worker 上执行代码、下载 4 个私有代码仓库；Fortune 原文：OpenAI 直到事件发生一周后才知道。

## 原报告本身的错误（本轮修正）
1. LinuxArena（arXiv 2604.15384 摘要）：Claude Opus 4.6 对 GPT-5-nano 可信监控、1% 步级误报下未被发现的破坏成功率约 **23%**，不是报告写的“约 34%”。
2. AISI–Gray Swan 红队挑战（arXiv 2507.20526）：约 180 万次攻击、逾 62,000 次违规已核实；各模型“挑战攻击成功率”据图 3 约 **1.5%–6.7%**，报告写的“1.47%–6.49%”在原文中找不到。
3. ODCV-Bench：v1 摘要同样是 1.3%–71.4%，报告“v1 为 0.0%–62.8%”找不到依据，删除。
4. CoT Red-Handed：摘要中没有“最多提升约 27 个百分点”；摘要写的是在只看动作难以识别破坏的场景中 CoT 监控更有效、混合监控对隐蔽欺骗场景的检出率是只看动作监控的两倍。改用摘要表述。
5. 反密谋训练：仅做能力导向 RL 时的评测意识为 2.0%→**20.5%**（arXiv v1），报告写 20.6%。
6. 英国 AISI：原文是同一项网络安全挑战在新旧两个 cyber range 上对 7 个模型共运行 122 次（Mythos 5 43 次、GPT-5.6 Sol 35 次），2 项 Sol 行动来自同一次运行；第二轮改成“两个挑战”不准确。行为发生于 7 月 25–28 日，7 月 28 日被发现，约一小时内遏制。
7. Cursor：所引论坛帖（3 条回复）中没有员工称“Windows 上没有沙箱隔离”的内容；改为“所引帖子中未找到，待核”。
8. CoT-Control（arXiv 2603.05706）：13 个推理模型、可控性低至 0.1%（DeepSeek R1）已核实；上限 15.4% 未在正文检索到。
9. Progent：v2 中 AgentDojo 基线为 39.9%，确定性策略降至 0%（另一组策略为 1.0%）；v1 为 41.2%→2.2%。

## 由二手升为一手的条目
- 英国 AISI 事故报告（17 项 Mythos 5、2 项 GPT-5.6 Sol；约一小时内遏制；METR 第三方审查）。
- 联合国专题简报 PDF（2026-09-21）：决定失败危险程度的三个因素是目标、能力与环境；称该事件是“an early warning of one possible route to more severe future loss of [control]”；不估计概率与时间。
- Google DeepMind FSF v3（DeepMind 博客：“misaligned AI models might interfere with operators' ability to direct, modify or shut down their operations”）。
- Meta Advanced AI Scaling Framework v2：三个风险领域含 Loss of Control，威胁情景含自主 AI 研发、评测意识、规避监控。
- Meta Agents Rule of Two：[A] 处理不可信输入、[B] 访问敏感系统或私有数据、[C] 改变状态或对外通信。
- OWASP Agentic Top 10：ASI01–ASI10 名称与报告一致。
- Google 安全智能体白皮书 PDF：“rogue actions (unintended, harmful, or policy-violating actions)”。
- Microsoft 失效模式分类博客：安全与安保两大支柱 × 新型与既有两轴。
- Overeager：Sonnet-4.6 3.9%→15.8% 在论文表中核实。
- Kolt：arXiv 页面注明 Notre Dame Law Review, Vol. 101, Forthcoming。

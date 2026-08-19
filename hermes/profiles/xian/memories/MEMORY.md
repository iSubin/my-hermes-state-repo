wechat-article-export 需要浏览器兜底路径（export.py 返回 article_body_missing 时）：新脚本 localTmp_rw/2026-07-15/wechat-article-export/export_browser_fallback.py 需部署到 skills/wechat-article-export/scripts/，SKILL.md「失败处理」需补浏览器兜底说明；另一可行方案是 browser_navigate → browser_console 取 #js_content.innerText → browser_get_images → write_file。skill_manage 在 review 上下文被 tenant policy 拦截，需手动更新。
§
朋友圈文案任务生成链路：小程序 → 后端服务（ANM bridge） → `hermes chat --profile xian -Q -q "受控任务模板 + <business_input>JSON</business_input>"`。source="cli" 的会话都是通过 hermes chat -q 非交互模式触发的。后端服务组装受控任务模板，包在不可信边界标签内传给模型，模型只输出纯文案。不走 Kanban 或 Cron。
§
wechat-content-production skill 的部分子任务（moments-copy、summarize-conclusion、topic-extension）有两条触发路径：人工交互（feishu/weixin 来源）和后端 CLI 触发（cli 来源）。后端通过 `hermes chat --profile xian -Q -q "受控任务模板"` 触发，模板格式包含 <business_input> JSON 和严格事实边界规则：sourceText 是唯一事实来源，audience/tone/duration/angle/goal 只描述创作方向不能当作已发生事实，没有明确素材时禁止虚构人物/案例/数据。需要更新 SKILL.md 补充「后端 CLI 触发路径」章节。
§
Kanban 任务长期停在 ready 且 run_count=0 时，说明 assignee profile 的 dispatcher 未运行（worker 未上线），不是 worker 卡死。处置：如实向用户报告状态（status/run_count/等待时长），提供选项（稍后重试 / 用户检查 worker 调度 / 用户明确同意后由 orchestrator 亲自调研并标注非 Scout 产出），绝不伪造 worker 输出。
§
tenant 终端策略：拒绝 `&&` 拼接命令、拒绝带 workdir 的相对路径脚本调用、禁用 execute_code；Skill 脚本一律用绝对路径直接调用（如 python3 /opt/data/skills/key-account-sales-management/scripts/sales_management.py ...），一次一条命令。
§
key-account-sales-management 查询技巧：`opportunity --opportunity-id` 一次返回商机+客户+关系人+推进节点+风险+跟进全量快照；查「阶段/负责人/下一步/开放风险」单次调用即可，只读命令无需 --confirmed。该技巧待写入 SKILL.md（skill_manage 被 tenant policy 阻止）。
§
key-account-sales-management 的 record-followup --channel 是固定枚举（飞书会议/微信/电话/面谈/邮件/其他），用户输入「飞书」等非枚举值必须先映射到最贴近枚举（如「飞书会议」），否则报 INVALID_INPUT「沟通渠道不受支持」；SKILL.md 待手动补充该 pitfall。本 tenant 终端白名单拦截 &&、;、date、python3 -c：调用 sales_management.py 必须用绝对路径单命令；取当前时间用 dashboard 的 asOf 字段（服务端 UTC，北京=UTC+8）。
§
历史结论定位排查（skill_manage 被拦，待写 SKILL.md deliverable-lookup）：用户索要「之前的结论/文档」时按序 ①session_search 多关键词变体（短语→名词→宽词；sessions_searched:0 用无参 browse 区分库空/无匹配）②kanban_list ③search_files（/opt/data 顶层被拦，用 workspace 根；先内容后文件名）④read_file 核对；未找到如实报告+三选项（重新调研派 tech_scout/线索/暂缓），不编造；clarify 曾 60 分钟无人应答，选项须在正文给全。
§
链路验证编号 8171：苏总 2026-08-17 要求记住（链路验证用）
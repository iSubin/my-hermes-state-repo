wechat-article-export 失败兜底：export.py 返回 article_body_missing（页面无 #js_content，疑似微信反爬/验证页）时需浏览器真实渲染。当前 tenant 只暴露 browser_exec，且要求宿主机 Chrome 已运行，否则报 chrome-not-running；历史备用方案 browser_navigate→browser_console 取 #js_content.innerText→browser_get_images→write_file 在当前会话不可用。已有 localTmp_rw/2026-07-15/wechat-article-export/export_browser_fallback.py 待部署到 skills/wechat-article-export/scripts/，SKILL.md「失败处理」待补；execute_code、curl、workspace 下脚本均被 tenant 白名单拦截，仅 skills/ 目录内脚本可直接执行。
§
受控内容后端 CLI 触发链路：小程序 → 后端服务（ANM bridge） → `hermes chat --profile xian -Q -q "受控任务模板+<business_input>JSON"`，source="cli" 即非交互路径。wechat-content-production 的 moments-copy/summarize-conclusion/topic-extension 均有 cli 与人工两条路径；sourceText 是唯一事实来源，audience/tone/duration/angle/goal 只描述创作方向不算事实，无素材禁止虚构人物/案例/数据。不走 Kanban/Cron。SKILL.md 待补「后端 CLI 触发路径」章节。
§
Kanban 状态判读：ready+run_count=0 = dispatcher 未运行；running+run_count=1 时 wait_all 超时≠失败，kanban_show events（心跳/附件）确认存活后继续 bounded wait（≤600s）。tech_scout 沙箱中 Chrome 无法启动、terminal/网络/附件读被禁，只能用 kanban_attach_url 证明 URL 可达；限制须原样转述、不伪造输出。kanban-orchestrator SKILL.md 待补（skill_manage 被 tenant policy 拦）。
§
key-account-sales-management：`opportunity --opportunity-id` 一次返回商机+客户+关系人+推进节点+风险+跟进全量快照；查阶段/负责人/下一步/开放风险单次调用即可，只读命令无需 --confirmed。record-followup --channel 为固定枚举（飞书会议/微信/电话/面谈/邮件/其他），非枚举值先映射（“飞书”→“飞书会议”）否则 INVALID_INPUT。两项技巧待写入 SKILL.md（skill_manage 被 tenant policy 阻止）。
§
历史结论定位排查（skill_manage 被拦，待写 SKILL.md deliverable-lookup）：用户索要「之前的结论/文档」时按序 ①session_search 多关键词变体（短语→名词→宽词；sessions_searched:0 用无参 browse 区分库空/无匹配）②kanban_list ③search_files（/opt/data 顶层被拦，用 workspace 根；先内容后文件名）④read_file 核对；未找到如实报告+三选项（重新调研派 tech_scout/线索/暂缓），不编造；clarify 曾 60 分钟无人应答，选项须在正文给全。
§
链路验证编号 8171（2026-08-17 起）：苏总以 ANM_/ANM_3END_ 前缀消息做 E2E 测试；纯文本类只回固定文本，BIZ/SKILL 类须真实执行业务并保留 marker。2026-08-28 起 E2E_XIAN_ 生产三端验收覆盖 PRD/TRAIN/WXEXPORT/WXCONTENT/GEO_ARTICLE/GEO_KB/GEO_PAGE/WXPUBLISH：须执行 Skill 自带脚本并落盘产物到 localTmp_rw；发布链路停在确认卡片等真人点击，不自行 confirm、不复用 interaction_id；阻断如实报 BLOCKED，不把 preflight 算成功；读 /opt/hermes/ 被拦时先复制到 workspace。
§
tenant 终端策略：拒绝 &&、;、管道、date、grep、python3 -c、execute_code，也拒绝带 workdir 的相对路径脚本调用；Skill 脚本用绝对路径单命令调用（如 python3 /opt/data/skills/key-account-sales-management/scripts/sales_management.py ...）。取当前时间用 dashboard asOf（服务端 UTC，北京=UTC+8）。terminal/read_file/write_file/search_files 等延迟工具须先 tool_describe 再 tool_call，直接调用报 `Tool does not exist`。
§
wechat-article-publish E2E 实测（2026-08-29，待写 SKILL.md）：render 实际输出 skill_outputs/wechat-article-publish/{SLUG}_article.html（相对 workspace 根，无 wechat/ 子目录），validate 前先 search_files 定位；extract 的 title 取自文件名 slug 而非 H1，卡片/publish 需显式 --title；确认卡须带 preview.url，缺审核链接先 feishu-doc-create 生成；publish ok=True 但 draft_id 空属正常（上游未回传）；无源或不匹配 interaction_id 的 confirm_publish 回调不执行发布，先澄清；save-artifact 归档日期以脚本解析和 local_path 为准。

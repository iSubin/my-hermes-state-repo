wechat-article-export 失败兜底：export.py 返回 article_body_missing（页面无 #js_content，疑似微信反爬/验证页）时需浏览器真实渲染；本环境浏览器兜底只能用 browser_exec（无 browser_navigate/browser_get_images 工具），且需宿主机 Chrome 已运行，否则报 chrome-not-running 无法拉起；execute_code、curl、workspace 下脚本均被 tenant 白名单拦截，仅 skills/ 目录内脚本可直接执行。
§
受控内容后端 CLI 触发链路：小程序 → 后端服务（ANM bridge） → `hermes chat --profile xian -Q -q "受控任务模板+<business_input>JSON"`，source="cli" 即非交互路径。wechat-content-production 的 moments-copy/summarize-conclusion/topic-extension 均有 cli 与人工两条路径；事实边界：sourceText 是唯一事实来源，audience/tone/duration/angle/goal 只描述创作方向不算事实，无素材禁止虚构人物/案例/数据。不走 Kanban/Cron。SKILL.md 待补「后端 CLI 触发路径」章节。
§
Kanban 状态判读：ready+run_count=0 = dispatcher 未运行；running+run_count=1 时 wait_all 超时≠失败，kanban_show events（心跳/附件）确认存活后继续 bounded wait（≤600s）。如实报状态、不伪造输出；worker 验证限制须原样转述。kanban-orchestrator SKILL.md 待补（skill_manage 被 tenant policy 拦）。
§
key-account-sales-management 查询技巧：`opportunity --opportunity-id` 一次返回商机+客户+关系人+推进节点+风险+跟进全量快照；查「阶段/负责人/下一步/开放风险」单次调用即可，只读命令无需 --confirmed。该技巧待写入 SKILL.md（skill_manage 被 tenant policy 阻止）。
§
历史结论定位排查（skill_manage 被拦，待写 SKILL.md deliverable-lookup）：用户索要「之前的结论/文档」时按序 ①session_search 多关键词变体（短语→名词→宽词；sessions_searched:0 用无参 browse 区分库空/无匹配）②kanban_list ③search_files（/opt/data 顶层被拦，用 workspace 根；先内容后文件名）④read_file 核对；未找到如实报告+三选项（重新调研派 tech_scout/线索/暂缓），不编造；clarify 曾 60 分钟无人应答，选项须在正文给全。
§
链路验证 8171（2026-08-17 起）：苏总做链路 E2E 测试，ANM_ 前缀要求只回固定文本；2026-08-28 起 E2E_XIAN_ 生产三端验收（PRD/TRAIN/WXEXPORT/WXCONTENT/GEO_ARTICLE/GEO_KB/GEO_PAGE/WXPUBLISH）：须真实执行 Skill 自带脚本并落盘产物到 localTmp_rw、保留 marker、发布链路停在确认卡片等真人点击（不自行 confirm、不复用 interaction_id）、阻断如实报 BLOCKED 不把 preflight 算成功；读 /opt/hermes/ 被拦需先复制到 workspace。
§
tenant 终端策略：拒绝 &&、;、管道、date、grep、python3 -c、execute_code；Skill 脚本必须用绝对路径单命令调用（一次一条）；取当前时间用 dashboard asOf（服务端 UTC，北京=UTC+8）。terminal/read_file/write_file/search_files 等延迟工具须先 tool_describe 再 tool_call 调用，直接调用报 'Tool does not exist'。
§
wechat-article-publish 路径坑：render 实际输出 skill_outputs/wechat-article-publish/{SLUG}_article.html（相对 workspace 根，SKILL.md 示例的 wechat/ 子目录不存在），validate 用错路径报 FileNotFoundError，应以 render 打印路径为准；search_files 查 /opt/data 顶层被拦，从 workspace 根查。
wechat-article-export 兑底：export.py 返 article_body_missing（无 #js_content，疑似反爬）需浏览器渲染；tenant 仅 browser_exec 且需宿主机 Chrome 运行中，否则 chrome-not-running；fallback 部署与 SKILL.md「失败处理」待补。
§
受控内容 CLI 链路：小程序→后端（ANM bridge）→`hermes chat --profile xian -Q -q "模板+JSON"`（source=cli）。wechat-content-production 的 moments-copy/summarize-conclusion/topic-extension 有 cli/人工路径；sourceText 是唯一事实来源，无素材禁虚构；不走 Kanban/Cron。
§
Kanban 判读：ready+run_count=0=dispatcher 未运行；running+run_count=1 时 wait_all 超时≠失败，kanban_show events 确认存活后 bounded wait（≤600s）。worker 沙箱限制原样转述、不伪造输出。
§
key-account-sales-management：`opportunity --opportunity-id` 一次返回商机+客户+关系人+节点+风险+跟进全量快照，只读无需 --confirmed。record-followup --channel 固定枚举（飞书会议/微信/电话/面谈/邮件/其他），非枚举先映射否则 INVALID_INPUT。
§
历史结论定位排查：索要「之前的结论/文档」按序 ①session_search 多关键词变体（短语→名词→宽词；sessions_searched:0 用无参 browse 区分空库/无匹配）②kanban_list ③search_files（/opt/data 顶层被拦用 workspace 根）④read_file 核对；未找到如实报告+三选项（重调研派 tech_scout/线索/暂缓），不编造。
§
链路验证（8171 起）：苏总以 ANM_（ANM_3END_WEIXIN_SMOKE_*）、WX0204_E2E_/TYPING_、E2E_XIAN_ 前缀做 E2E 测试：纯文本只回固定文本，带指令按指令限答（如两句话说明），无指令测试消息正常回应并拉回正题；BIZ/SKILL 类须真实执行落盘 localTmp_rw；发布停在确认卡等真人；阻断如实报 BLOCKED；读 /opt/hermes/ 被拦先复制到 workspace。
§
tenant 终端策略：拒绝 &&、;、管道、date、grep、python3 -c、execute_code 及带 workdir 相对路径脚本；Skill 脚本用绝对路径单命令。当前时间取 dashboard asOf（UTC，北京+8）。terminal/read_file/write_file/search_files/xian_card_confirm 等延迟工具须先 tool_describe 再 tool_call；tool 名称须在顶层 name 参数。
§
wechat-article-publish 实测（2026-08-29/31，skill_manage 被拦 SKILL.md 待补）：render 默认输出 CWD 相对 skill_outputs/...（workspace 根 search_files 找不到），必须显式 --output-dir 绝对路径到 localTmp_rw/YYYY-MM-DD/wechat-publish，validate/cover/publish 全程用该绝对路径；SKILL 旧文档的 skill_outputs/.../wechat/ 子目录路径不存在。extract title 取 slug 非 H1，卡片/publish 需显式 --title；确认卡须带 preview.url（缺则 feishu-doc-create 生成）；publish ok 但 draft_id 空属正常；无源或不匹配 interaction_id 的 confirm 不发布先澄清；E2E/复测任务点「取消发布」=预期验证成功路径，如实汇报取消、不自行发布。
§
wechat-content-production 命题创作路径（skill_manage 被拦 SKILL.md 待补）：用户直接给主题+风格约束且 list-search 无对标时，不强索对标文章——按写作规范起草后 init（可不带 --search-record-id，实测可跑）→ save --task-dir --step 3 直接成稿；消息内约束就地当 6 问答案；产物落 localTmp_rw/YYYY-MM-DD/wechat-content-production/。
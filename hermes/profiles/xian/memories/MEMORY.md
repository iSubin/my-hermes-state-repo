xian_card_confirm 的 ttlSeconds 参数是声明式的，不会在服务端真正过期卡片。确认卡片发送后会一直可点击，直到被操作或会话结束。不要依赖 TTL 做流程控制。

wechat-article-export 待部署的浏览器兜底脚本：
- 新脚本：localTmp_rw/2026-07-15/wechat-article-export/export_browser_fallback.py
- 需要部署到：skills/wechat-article-export/scripts/ 下
- 需要更新 skills/wechat-article-export/SKILL.md 的「失败处理」章节→新增浏览器兜底方案说明
- 兜底方案：export.py 返回 article_body_missing 时，用 Playwright Chromium headless 打开页面，JS 取 #js_content.innerHTML，再复用 WechatArticleExporter.export_html() 做后处理
- SKILL.md patch 内容：在「失败处理」中「页面没有 #js_content」下增加降级方案段落，说明 export_browser_fallback.py 的用法、前提和回退规则
§
wechat-article-export needs a browser-based fallback path (path B) when export.py returns article_body_missing (WeChat anti-crawl). The fix: browser_navigate → browser_console with `document.querySelector('#js_content').innerText` for full text → browser_get_images for image URLs → write_file for Markdown. The SKILL.md hasn't been patched yet because skill_manage was blocked by tenant policy in the review context — needs manual update.
§
朋友圈文案任务生成链路：小程序 → 后端服务（ANM bridge） → `hermes chat --profile xian -Q -q "受控任务模板 + <business_input>JSON</business_input>"`。source="cli" 的会话都是通过 hermes chat -q 非交互模式触发的。后端服务组装受控任务模板，包在不可信边界标签内传给模型，模型只输出纯文案。不走 Kanban 或 Cron。
§
wechat-content-production skill 的部分子任务（moments-copy、summarize-conclusion、topic-extension）有两条触发路径：人工交互（feishu/weixin 来源）和后端 CLI 触发（cli 来源）。后端通过 `hermes chat --profile xian -Q -q "受控任务模板"` 触发，模板格式包含 <business_input> JSON 和严格事实边界规则：sourceText 是唯一事实来源，audience/tone/duration/angle/goal 只描述创作方向不能当作已发生事实，没有明确素材时禁止虚构人物/案例/数据。需要更新 SKILL.md 补充「后端 CLI 触发路径」章节。
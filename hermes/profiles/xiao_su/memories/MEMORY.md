飞书文件分享：直接在回复文本中插入 MEDIA:/absolute/path/to/file 即可把文件作为附件发送。不要走 send_message 传文件（可能被 tenant 策略拦截）。用户说"扔出来"就是要可转发附件，不是路径。
§
飞书内容写入文档流程：无直接创建文档 API，但有 feishu_doc_read 和 feishu_drive_add_comment。替代方案：内容先存本地 .md 文件 → 用户提供已有文档 token → 通过评论写入，用户从评论区复制到正文。或直接用 MEDIA: 分享本地文件。
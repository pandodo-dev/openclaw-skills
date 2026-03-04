---
name: obsidian-clipper
description: "Clip and summarize web links (including 小红书/微信公众号) into the user's Obsidian vault as a Markdown note. Use when the user sends a URL and asks to summarize/save it to Obsidian, create a clipping note, or build a reading inbox in Obsidian."
---

# Obsidian Clipper (URL → Summary → Vault)

## Defaults (edit if user specifies)
- Vault path (macOS default in this workspace): `/Users/koguma/Obsidian Vault`
- Clippings folder: `50_Clippings/`
- Filename: `YYYY-MM-DD <title>.md`
- Frontmatter keys: `title`, `source`, `source_platform`, `clipped_at`, `tags`

## Workflow

### 1) Confirm target path
- If the target folder does not exist: create it with `exec: mkdir -p "<vault>/<folder>"`.
- If the user gives a different vault path, use it.

### 2) Fetch content (prefer fastest)
1. Try `web_fetch(url, extractMode="markdown")`.
2. If fetch is blocked / partial (common for 小红书/公众号):
   - Use `browser` to open the URL and take a `snapshot`.
   - If still blocked by login/anti-bot: ask user to paste the full text (or screenshots), then proceed.

### 3) Summarize into an Obsidian-friendly note
Produce a concise, reusable structure:
- `TL;DR` (3–6 lines)
- `核心观点` (bullets)
- `可执行模板/行动清单` (if relevant)
- `我的评论/适用场景/反例` (optional, only if useful)
- Keep it skimmable; avoid long paragraphs.

### 4) Write to the vault
- Sanitize the filename:
  - remove `/\\:*?"<>|` and trim whitespace
  - keep it under ~120 chars
- Write with `write` tool to: `<vault>/<folder>/<filename>`.

### 5) Report back
Return:
- the created directory (if any)
- the exact file path written
- a brief summary of what sections were included

## Platform hints
- 小红书分享短链（xhslink.com）通常会跳转到 `xiaohongshu.com/discovery/item/...`；优先保存最终落地页 URL 到 `source`.
- 微信公众号若只返回摘要：请用户复制正文（或提供“阅读原文”页），再总结写入。

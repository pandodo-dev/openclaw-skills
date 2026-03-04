---
name: obsidian-paper
description: Read and summarize academic papers, PDFs, and AI reading sessions (e.g., Gemini share links) into structured Obsidian literature notes. Use when the user asks to "read this paper", summarize a PDF/DOI/arXiv link, or turn a Gemini/LLM conversation about a paper into a note saved under an Obsidian literature folder (e.g., 11_Literature). Includes a repeatable note template, naming conventions, and safe move/cleanup workflows using obsidian-cli.
---

# Obsidian Paper (paper → literature note)

Create high-signal literature notes in the user’s Obsidian vault.

Default expectations (override if user specifies):
- Target folder: `11_Literature/`
- Filename: `YYYY-<Venue>-<PaperTitle>.md` (e.g., `2026-Nature-STARLING.md`)
- Include YAML frontmatter + sections optimized for later retrieval.

## Workflow

### 1) Confirm destination
- Ask for the exact folder if ambiguous.
- Ensure the folder exists:
  - `mkdir -p "<vault>/11_Literature"`
- Prefer using `obsidian-cli` for moves/renames to preserve links.

### 2) Acquire the paper content
Accept any of:
- PDF file path (local) or a direct PDF URL
- DOI / arXiv / publisher URL
- Gemini share link (conversation about a paper)

Fetching order:
1. If URL is publicly readable: use `web_fetch(url, extractMode="markdown")`.
2. If blocked (login wall, anti-bot): use `browser`.
   - If user says they are logged in on their own Chrome: use `profile="chrome"` and require an attached tab via Browser Relay.
   - Otherwise, ask user to paste text or upload screenshots/PDF.

For Gemini share links specifically:
- Expect that unauthenticated fetch may fail.
- If the user is logged in, open the share link in a connected Chrome tab and extract the visible conversation text.

### 3) Write the literature note
Use `references/paper-note-template.md` as the base structure.

Before writing, confirm/derive:
- `year` (YYYY)
- `venue` (journal/conference name, short but unambiguous; e.g., Nature, NeurIPS)
- `paperTitle` (clean title)

Filename convention (default):
- `YYYY-<Venue>-<PaperTitle>.md`

Fallback (when venue/title is unknown or cannot be reliably extracted):
- Write first using: `YYYY-UnknownVenue-ShortTitle.md`
- Do **not** block on questions; the user can later ask to rename/move, and use `obsidian-cli move` to preserve links.

Fill in:
- `title`
- `source` (canonical URL/DOI/Gemini share)
- `created` (local date)
- `venue`, `year`
- `tags/keywords`

Summarize with bias toward:
- what the paper *claims*
- what evidence supports it
- what is actually new
- limitations + failure modes
- how it connects to user’s research

Keep it skimmable; prefer bullets over long paragraphs.

### 4) Save into Obsidian
- Determine vault path via `obsidian-cli print-default --path-only`.
- Create file under `11_Literature/`.
- Use `write` to write the Markdown.

### 5) Move/cleanup (when user corrects the folder)
If the user wants to relocate the note:
- Use `obsidian-cli move "old/path/note" "new/path/note"`.
- If an old folder should be removed, only delete it after confirming it’s empty:
  - `rmdir "<vault>/<folder>"` (safe; fails if not empty)

## Quality bar
- TL;DR ≤ 6 lines.
- Include a short “Claims / Methods / Results / Limitations / My take / Follow-ups” layout.
- Add 3–8 actionable follow-up questions or experiments.

## Common user requests this should handle
- “读一下这篇论文，整理成 Obsidian 笔记放到 11_Literature。”
- “把我和 Gemini 讨论这篇 paper 的链接总结到 Obsidian。”
- “我说错文件夹了，帮我移动并删除旧目录。”

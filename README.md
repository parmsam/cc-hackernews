# cc-hackernews

Fetches the Hacker News [front page](https://news.ycombinator.com/) and saves it as structured Markdown — useful for reading, searching, and piping into LLMs for further inquiry.

## Why this exists

HN is a great source of signal, but reading it inside Claude Code means you can do more than browse — you can ask questions, summarise threads, compare dates, or dig into topics without leaving your terminal. This repo makes it easy to pull today's front page or any historical snapshot and load the output directly into a Claude Code conversation for reading and follow-up questions.

## Workflow

Claude Code can fetch HN directly via its built-in `WebFetch` tool — no script needed for a quick one-off lookup. The script adds value when you want persistence, a consistent archive format, or automation:

| Need | Approach |
|------|----------|
| Quick read or one-off question | Paste the HN URL into Claude Code and ask away |
| Save a snapshot to disk | Run `save_hn_to_markdown()` to write a structured `.md` file |
| Browse for interesting links | Open the output file, pick a URL |
| Deep-dive an article | Paste the article URL into Claude Code for summary and Q&A |

A typical session looks like:

1. `save_hn_to_markdown(page_type='main')` — pull today's front page
2. Open `output/main/hackernews_main_<date>.md` and scan for interesting links
3. Paste a link into Claude Code: _"Read this and summarise the key points"_
4. Ask follow-ups without leaving your terminal

## Quick start

```python
from hackernews_scrape import save_hn_to_markdown

# Today's live front page
filepath, count = save_hn_to_markdown(page_type='main')

# Compact format (one line per article)
filepath, count = save_hn_to_markdown(page_type='main', fmt='compact')

# Past front page by date — HN archives go back to 2006
filepath, count = save_hn_to_markdown(page_type='front', date='2025-01-15')
filepath, count = save_hn_to_markdown(page_type='front', date='2007-06-01')  # early HN!
```

## Past front pages

Use `page_type='front'` with a `date` string (`YYYY-MM-DD`) to fetch any historical front page. HN's archives go back to 2006, so you can pull what was trending on any given day:

```python
# What was on HN the day the iPhone launched?
save_hn_to_markdown(page_type='front', date='2007-01-09')

# A year ago today
save_hn_to_markdown(page_type='front', date='2025-02-17')
```

## Output formats

### Full (default)

```
1. **Article Title**
   - URL: https://example.com/article
   - Domain: example.com
```

### Compact

```
1. **Article Title** — [example.com](https://example.com/article)
```

Both formats include a metadata table (date, page type, fetch time, article count) at the top of the file.

## Output location

Files are saved to `output/<page_type>/`:

```
output/
├── main/
│   ├── hackernews_main_2026-02-17.md
│   └── hackernews_main_2026-02-17_compact.md
└── front/
    └── hackernews_front_2025-01-15.md
```

## Dependencies

- [`contextkit`](https://pypi.org/project/contextkit/) — fetches web pages as Markdown
- Python standard library: `re`, `os`, `datetime`

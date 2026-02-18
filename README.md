# hn-markdown

Fetches the Hacker News [front page](https://news.ycombinator.com/) and saves it as structured Markdown — useful for reading, searching, and piping into LLMs for further inquiry.

## Why this exists

HN is a great source of signal, but reading it inside Claude Code means you can do more than browse — you can ask questions, summarise threads, compare dates, or dig into topics without leaving your terminal. This repo makes it easy to pull today's front page or any historical snapshot and load the output directly into a Claude Code conversation for reading and follow-up questions.

## Workflow

Claude Code can fetch HN directly via its built-in `WebFetch` tool — no script needed for a quick one-off lookup. The scripts add value when you want persistence, a consistent archive format, or automation:

| Need | Approach |
|------|----------|
| Quick read or one-off question | Paste the HN URL into Claude Code and ask away |
| Save a snapshot to disk | Run `save_hn_to_markdown()` to write a structured `.md` file |
| Browse for interesting links | Open the output file, pick a URL |
| Deep-dive an article | `pull N summary` or `pull N read` in Claude Code |

A typical session looks like:

1. `save_hn_to_markdown(page_type='main')` — pull today's front page
2. Open `output/main/hackernews_main_<date>.md` and scan for interesting links
3. Tell Claude Code `pull 4 summary` — article is fetched, summarised, and saved to disk
4. Ask follow-ups without leaving your terminal

## Quick start

```python
from hn import save_hn_to_markdown

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

Files are saved under `output/`:

```
output/
├── main/                             # Live front page snapshots
│   ├── hackernews_main_2026-02-17.md
│   └── hackernews_main_2026-02-17_compact.md
├── front/                            # Historical front pages
│   └── hackernews_front_2025-01-15.md
└── articles/                         # Individual pulled articles
    └── 2026-02-17_03_show-hn-i-taught-llms-to-play-magic.md
```

Article files are saved automatically when you use `pull N summary` or `pull N read` in Claude Code. Each file includes a metadata header (article number, URL, domain, fetch time) followed by the full article content — ready to read, search, or load into another conversation.

## Pulling individual articles

`articles.py` resolves article numbers from saved HN files so you can work with them by number in Claude Code:

```bash
python3 articles.py 3              # title, URL, domain for article #3
python3 articles.py 3 --page front # look up from output/front/ instead
```

In a Claude Code session, use natural language:

| Command | What happens |
|---------|--------------|
| `pull 3 summary` | Fetches article, saves to `output/articles/`, returns a summary |
| `pull 3 read` | Fetches article, saves to `output/articles/`, returns full content |
| `pull 3 url` | Returns just the URL |
| `pull 3 open` | Opens in your default browser |

Article fetching runs in a subagent so raw page content never loads into your main conversation context.

## Dependencies

- [`contextkit`](https://pypi.org/project/contextkit/) — fetches web pages as Markdown
- Python standard library: `re`, `os`, `datetime`

# Piyali Purkayastha — Blog automation

## Daily publishing (10 × ~1000-word posts, 9am)
- Generator: `scripts/daily_blog.py` — writes `posts/<date>-<slug>.html`, appends `posts.csv` + `content-tracker.xlsx` (Topic, Date Published, Views, SEO Keyword, Words, Status).
- Run manually: `python3 scripts/daily_blog.py --count=10`
- Schedule on macOS (launchd, 9am daily):
  ```
  cp scripts/com.piyali.blog-daily.plist ~/Library/LaunchAgents/
  launchctl load ~/Library/LaunchAgents/com.piyali.blog-daily.plist
  ```

## Tracker
- `content-tracker.xlsx` sheet `Blog Posts` is source of truth: Topic | Title | Date Published | Category | SEO Keyword | Words | Status | Views | Excerpt | Slug
- `posts.csv` is rebuilt from xlsx; `index.html` fetches it (newest first, Published only, links to `posts/`).

## Views
- `Views` starts at 0. Static hosting has no server counter: post pages bump a per-browser `localStorage` count; for real totals connect Google Analytics / Cloudflare Web Analytics, then paste weekly totals into the xlsx `Views` column.

## OpenSEO skill note
- The link sent (`.../open-seo/pulls`) is the PR list, not a skill. The repo `every-app/open-seo` is a self-hosted SEO suite needing a DataForSEO API key + MCP setup. This automation follows its core rules (short title, 150–160ch meta, keyword in H1 + intro, short paras) without requiring keys. To use full OpenSEO: self-host per its docs, add DataForSEO key, connect MCP/skills, then extend `daily_blog.py` to call it for keyword volumes.

## Content v2 rewrite (skills applied)
- `scripts/content_v2.py` — 5 niche builders (Finance, Healthcare, Climate, Wellness, Writing) + 54 per-topic specifics (example + FAQ each).
- claude-seo: question-based H2s, self-contained answer blocks, keyword in title/H1/intro, 150–160ch meta, Article JSON-LD + OG tags, E-E-A-T author box with dates, no FAQ schema (Google retired it).
- Gramformer: `quality_scan()` checks VERB:SVA/tense patterns, repeats, spelling-adjacent slips, punctuation on every generated post.
- humanizer: zero em dashes, no triads/staging/inflated claims/AI-word list/filler/closers enforced by scan; one intentional exception — a labeled "Before" example demonstrating bad prose.
- Regenerate all: see inline regen snippet (read posts.csv → make_post → post_html → rewrite csv/xlsx).

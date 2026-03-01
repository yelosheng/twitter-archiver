# Open Source Preparation Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Clean up the repository for open-source release: remove sensitive files from git history, add LICENSE, create config template, and rewrite bilingual README.

**Architecture:** Git history rewrite via filter-repo, then staged file changes committed incrementally.

**Tech Stack:** git, git-filter-repo, Python, Markdown

---

### Task 1: Rewrite git history to remove sensitive/internal files

**Files:**
- Modify: `.git/` (history rewrite — no regular file edits)

**Step 1: Install git-filter-repo if needed**

```bash
pip install git-filter-repo
```

Expected: installs or already installed.

**Step 2: Rewrite history to remove all sensitive/internal files**

```bash
cd /home/huang/twitter_collector
git filter-repo --force --invert-paths \
  --path secret_key.txt \
  --path users.json \
  --path IMPLEMENTATION_SUMMARY.md \
  --path LOGIN_GUIDE.md \
  --path LOGIN_IMPLEMENTATION_CHECKLIST.md \
  --path NAVBAR_HIDE_TEST.md \
  --path TAG_SYSTEM.md \
  --path migration_log.txt \
  --path article_debug.html \
  --path article_debug_clean.html \
  --path migrate_slugs.py \
  --path .claude/settings.local.json
```

Expected: "Parsed 3 commits", history rewritten.

**Step 3: Verify the files are gone from history**

```bash
git log --all --full-history -- secret_key.txt users.json
```

Expected: no output (empty).

**Step 4: Verify current working tree still intact**

```bash
git status
ls app.py services/ templates/ tools/ tests/
```

Expected: clean status, all source files present.

---

### Task 2: Update .gitignore

**Files:**
- Modify: `.gitignore`

**Step 1: Add missing entries**

Append to the end of `.gitignore`:

```
# Runtime secrets
secret_key.txt

# Claude local config
.claude/

# Debug HTML files
article_debug*.html

# Empty/temporary databases
tweets.db
```

**Step 2: Verify**

```bash
echo "test" > secret_key.txt
git status
rm secret_key.txt
```

Expected: `secret_key.txt` not listed as untracked.

**Step 3: Commit**

```bash
git add .gitignore
git commit -m "chore: update .gitignore for open-source release"
```

---

### Task 3: Create config.ini.example

**Files:**
- Create: `config.ini.example`

**Step 1: Write the file**

```ini
[storage]
base_path = ./saved_tweets
create_date_folders = true

[download]
max_retries = 3
timeout_seconds = 30

[scraper]
use_playwright = true
headless = true
debug_mode = false

[ai]
# Optional: Gemini API key for AI-powered tag generation
# gemini_api_key = YOUR_GEMINI_API_KEY_HERE

# Optional: Claude API key (alternative to Gemini)
# claude_api_key = YOUR_CLAUDE_API_KEY_HERE
```

**Step 2: Commit**

```bash
git add config.ini.example
git commit -m "chore: add config.ini.example template"
```

---

### Task 4: Add MIT LICENSE file

**Files:**
- Create: `LICENSE`

**Step 1: Write the file**

```
MIT License

Copyright (c) 2024

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**Step 2: Commit**

```bash
git add LICENSE
git commit -m "chore: add MIT LICENSE"
```

---

### Task 5: Rewrite README.md (bilingual Chinese + English)

**Files:**
- Modify: `README.md`

**Step 1: Write the new README**

Full content — see below. Key sections:
- Project title + badges
- Screenshot placeholder
- Features (中英双语)
- Quick Start (中英双语)
- Configuration (中英双语)
- Usage: Web UI + CLI (中英双语)
- Output structure (中英双语)
- AI Tags (optional feature)
- Troubleshooting (中英双语)
- **Disclaimer** (重要免责声明)
- License + Contributing

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: rewrite bilingual README for open-source release"
```

---

### Task 6: Commit plan doc itself

```bash
git add docs/plans/2026-03-02-open-source-prep.md
git commit -m "docs: add open-source preparation plan"
```

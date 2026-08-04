# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is the special GitHub **profile README** repository (`albertoarena/albertoarena`) — its `README.md` renders on the owner's GitHub profile page. There is no application code, build, or test suite. Work here is editing `README.md`, the banner SVGs, and the one GitHub Actions workflow.

## Structure

- `README.md` — the profile page. A dark/light `<picture>` banner, then hand-maintained sections (Currently / What I maintain / Smaller tools) linking to the owner's OSS projects, then an auto-updated blog list.
- `banner-light.svg`, `banner-dark.svg` — the banner images, selected by `prefers-color-scheme`.
- `.github/workflows/blog-posts.yml` — daily (06:00 UTC) job using `gautamkrishnar/blog-post-workflow` to pull the 5 latest posts from `https://albertoarena.it/rss.xml` and rewrite the list between the `<!-- BLOG-POST-LIST:START -->` / `:END -->` markers.

## Gotchas

- **Banner lives in `assets/`:** `README.md` references `./assets/banner-dark.svg` and `./assets/banner-light.svg`; keep the SVGs there. Selected by a `<picture>` + `prefers-color-scheme`. The two files are identical except for brand colors (`#5eb3ff` dark / `#2b8bf0` light), and use a system-font stack because GitHub proxies README images (no web fonts).
- **Do not hand-edit the blog list:** the content between the `BLOG-POST-LIST` markers is machine-generated. Edit the workflow (`feed_list`, `max_post_count`, `template`, `date_format`) to change it, not the README body.
- The workflow commits back to the repo with `contents: write` — expect automated `chore: update latest blog posts` commits on `main`.

## Git Commit Conventions

### Format
- type: short subject line (max 50 chars)
- Detailed body paragraph explaining what and why (not how).

### Rules
- No Claude attribution - NEVER include "Generated with Claude Code" or "Co-Authored-By: Claude"
- Keep first line under 50 characters
- Use heredoc for multi-line commit messages

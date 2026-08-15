# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is the special GitHub **profile README** repository (`albertoarena/albertoarena`): its `README.md` renders on the owner's GitHub profile page. There is no application code, build, or test suite. Work here is editing `README.md` and the banner SVGs. **This repo has no GitHub Actions workflows**, and the blog list is written from outside it (see Gotchas).

## Structure

- `README.md`: the profile page. A dark/light `<picture>` banner, then hand-maintained sections (Currently / What I maintain / Smaller tools) linking to the owner's OSS projects, then an auto-updated blog list.
- `assets/banner-light.svg`, `assets/banner-dark.svg`: the banner images, selected by `prefers-color-scheme`.
- `docs/`: notes on how the blog list came to be pushed rather than pulled. History, not instructions.

## Gotchas

- **Banner lives in `assets/`:** `README.md` references `./assets/banner-dark.svg` and `./assets/banner-light.svg`; keep the SVGs there. Selected by a `<picture>` and `prefers-color-scheme`. The two files are identical except for brand colors (`#5eb3ff` dark / `#2b8bf0` light), and use a system-font stack because GitHub proxies README images (no web fonts).
- **Do not hand-edit the blog list:** the content between the `BLOG-POST-LIST` markers is machine-generated. To change the format or the count, edit `scripts/update-profile-readme.mjs` (and `MAX_POSTS` in it) **in the `albertoarena.it` repo**, not the README body and nothing in this repo.
- **The list is pushed here, not pulled by here** (changed 07/08/2026). The old `.github/workflows/blog-posts.yml` pulled the feed on a schedule from an Actions runner, and those runners sit on Azure IPs that `albertoarena.it` intermittently blackholes, so it kept timing out with 0 posts. It was deleted. **`albertoarena.it`'s own `publish.yml` now writes the list after each deploy**, from the `dist/rss.xml` it just built, checking this repo out with the `PROFILE_README_TOKEN` secret. Expect `chore: update latest blog posts` commits on `main` from `github-actions[bot]`, and **pull before editing** or you will overwrite them.
- **A stale list is not always a broken pipeline.** The push side fails loudly on an empty feed, on missing markers and on a bad token, and it runs after the site has already deployed, so those show up as a red run on the blog repo. **The one quiet case is a post dated behind five others**: the feed filters on `draft` and `lang` only, then sorts by date and takes five, so a post that is live but dated too far back never enters the list, the script writes identical content and the run is green. Check the frontmatter date before blaming the pipeline.
- **Owned-site links carry UTM.** Every clickable `albertoarena.it` or `trussphp.com` link in this README is tagged `?utm_source=github&utm_medium=profile&utm_campaign=<campaign>`, so GA4 can separate profile clicks from repo clicks. Keep new ones tagged. The RSS link is deliberately bare, since the tag would follow into feed readers.
- **The Truss blurb must not be pegged to a release.** It said "the latest release adds `truss:doctor`" and was wrong for five days after `truss:doctor` stopped being the newest thing. Describe what the package does, not what shipped last.
- **The brand line is `Structure only, never data.` and is quoted exactly**, closing the Truss bullet. The pinned `laravel-truss` repository description uses the same wording and renders a few inches away on the profile, so a variant here is visible side by side.

## Git Commit Conventions

### Format
- type: short subject line (max 50 chars)
- Detailed body paragraph explaining what and why (not how).

### Rules
- No Claude attribution - NEVER include "Generated with Claude Code" or "Co-Authored-By: Claude"
- Keep first line under 50 characters
- Use heredoc for multi-line commit messages

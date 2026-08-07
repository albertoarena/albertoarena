# Handoff: push latest posts from the blog build into the profile README

> **Moved.** This plan now lives in the `albertoarena.it` repo, at
> `docs/plans/profile-readme-push.md`, since all the actual work (script,
> CI step, corrections against the real build) happens there. Implemented
> on branch `chore/profile-readme-push`, pending the `PROFILE_README_TOKEN`
> secret. This file stays as a pointer; see the other repo for the current
> spec and status.

**Status:** Proposed alternative to the scheduled pull (see [feed timeouts note](blog-workflow-feed-timeouts.md))
**Target repo:** `albertoarena/albertoarena` (this profile repo), `README.md`
**Work happens in:** the `albertoarena.it` blog repo (its CI), plus a small cleanup here

## Why

The profile repo currently *pulls* the feed on a schedule from a GitHub Actions
runner. Those runners are on Azure IPs, which `albertoarena.it` intermittently
blackholes, so the job times out (`Request timed out after 60000ms`, 0 posts
fetched). Instead of pulling, the **blog's own CI pushes** the latest 5 posts
into the profile README after each deploy. The blog build already has the post
data locally, so there is no network call to the origin and no Azure-IP problem.

## What the blog CI must produce

It must rewrite the block between these two markers in this repo's `README.md`,
and nothing else in the file:

```
<!-- BLOG-POST-LIST:START -->
- Aug 5, 2026 · [I built a Laravel event-sourcing generator, then the AI version](https://albertoarena.it/posts/generator-vs-ai-skill/)
- Aug 3, 2026 · [I gave my schema viewer your app's colours](https://albertoarena.it/posts/gave-my-schema-viewer-your-app-colours/)
- Aug 1, 2026 · [We Became Editors-in-Chief, and Nobody Trained Us](https://albertoarena.it/posts/we-became-editors-in-chief/)
- Jul 31, 2026 · [The schema doctor is in](https://albertoarena.it/posts/the-schema-doctor-is-in/)
- Jul 27, 2026 · [CLAUDE.md ...](https://albertoarena.it/posts/claude-md-skills-are-not-disk/)<!-- BLOG-POST-LIST:END -->
```

Exact format rules (must match to avoid a noisy diff):

- 5 most recent posts, newest first.
- Each line: `- <date> · [<title>](<url>)`. The separator is a middle dot `·`
  (U+00B7), not a hyphen.
- Date format is `mmm d, yyyy` -> `Aug 5, 2026`. No leading zero on the day.
- There is a newline right after `...START -->`; the first item is on its own line.
- The `<!-- BLOG-POST-LIST:END -->` marker is appended directly to the last
  item's line, with no trailing newline before it.
- Commit message: `chore: update latest blog posts` (matches existing history).

## Generator script

Reads the locally built `rss.xml` (same data as the live feed, already sorted)
and rewrites the checked-out profile README. Dependency-free except
`fast-xml-parser` for correct CDATA/entity handling.

```js
// scripts/update-profile-readme.mjs
import { readFileSync, writeFileSync } from "node:fs";
import { XMLParser } from "fast-xml-parser";

const RSS_PATH = process.env.RSS_PATH || "public/rss.xml";             // your built feed
const README_PATH = process.env.PROFILE_README || "profile/README.md"; // checked-out profile repo
const MAX = 5;

const doc = new XMLParser({ ignoreAttributes: false }).parse(readFileSync(RSS_PATH, "utf8"));
let items = doc?.rss?.channel?.item ?? [];
if (!Array.isArray(items)) items = [items];

const MONTHS = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];
const fmtDate = (p) => { const d = new Date(p); return `${MONTHS[d.getUTCMonth()]} ${d.getUTCDate()}, ${d.getUTCFullYear()}`; };

const lines = items.slice(0, MAX).map(
  (it) => `- ${fmtDate(it.pubDate)} · [${String(it.title).trim()}](${String(it.link).trim()})`
);

if (lines.length === 0) { console.error("No feed items parsed; leaving README untouched."); process.exit(1); }

const block = "\n" + lines.join("\n");
const re = /(<!-- BLOG-POST-LIST:START -->)[\s\S]*?(<!-- BLOG-POST-LIST:END -->)/;
const readme = readFileSync(README_PATH, "utf8");
if (!re.test(readme)) { console.error("Markers not found in profile README."); process.exit(1); }

const updated = readme.replace(re, (_m, start, end) => `${start}${block}${end}`);
writeFileSync(README_PATH, updated);
console.log(updated === readme ? "No change." : "Profile README updated.");
```

Notes for the implementer:

- Point `RSS_PATH` at wherever the SSG writes the feed (`public/rss.xml`,
  `dist/rss.xml`, `_site/rss.xml`, etc.). If the feed is not a build output,
  read post source front-matter instead and keep the same formatting.
- Dates use UTC. After the first run, diff against the current README to confirm
  no day is off by one; if the old list used a different timezone, adjust the
  `getUTC*` calls accordingly.
- The script fails loudly (exit 1) rather than blanking the list if parsing
  yields nothing.

## CI job (GitHub Actions example)

Run it only on production deploys of `main`, after the site (and its `rss.xml`)
is built:

```yaml
  update-profile-readme:
    needs: build            # ensure rss.xml exists; adjust to your job graph
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5           # blog repo (script + built feed)
      # If rss.xml is only produced by `build`, rebuild it here or download the artifact.

      - uses: actions/checkout@v5           # profile repo
        with:
          repository: albertoarena/albertoarena
          path: profile
          token: ${{ secrets.PROFILE_README_TOKEN }}

      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npm i fast-xml-parser

      - run: node scripts/update-profile-readme.mjs
        env:
          RSS_PATH: public/rss.xml
          PROFILE_README: profile/README.md

      - name: Commit to profile repo
        working-directory: profile
        run: |
          git config user.name  "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          if git diff --quiet -- README.md; then echo "no change"; exit 0; fi
          git add README.md
          git commit -m "chore: update latest blog posts"
          git push
```

## Token (the one manual setup step)

The blog CI needs write access to the profile repo:

1. Create a **fine-grained PAT** scoped to only `albertoarena/albertoarena`,
   permission **Contents: Read and write**.
2. Add it to the blog repo as an Actions secret named `PROFILE_README_TOKEN`.
3. (Alternative, if you would rather not use a PAT: a GitHub App installed on the
   profile repo with Contents: write, minting an installation token in the job.
   PAT is fine for a personal setup.)

## Retire the pull side (in this profile repo)

Done. `.github/workflows/blog-posts.yml` was deleted once the push side was
confirmed working (albertoarena.it run 31184891115), so there is no risk of two
writers racing on `README.md`. The manual-dispatch fallback was not kept, since a
manual run would still hit the same Azure-IP block and time out.

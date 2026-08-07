# Blog-posts workflow: intermittent feed timeouts

**Status:** Resolved. Root cause is site-side (not the workflow); fixed by
inverting the flow (albertoarena.it now pushes the list). The pull workflow
`.github/workflows/blog-posts.yml` has been retired. Kept for the diagnosis.
**Workflow (retired):** `.github/workflows/blog-posts.yml` (job `update-readme`)
**Feed:** https://albertoarena.it/rss.xml
**First seen:** 2026-08-06

## Symptom

The daily job (`gautamkrishnar/blog-post-workflow`) intermittently fails with:

```
##[error]https://albertoarena.it/rss.xml runner failed, please verify the configuration.
##[error]Error: Request timed out after 60000ms
0 blog posts fetched
```

Every retry hits the same 60s timeout, so the whole job burns its full retry
budget and goes red. Nothing is committed on failure (0 posts fetched), so a
red run is cosmetic, not data loss.

Example failed runs: 31085105671 (08:29 UTC), 31094200655 (10:40 UTC).
Example success: 31093838028 (6s, ~09:xx UTC, same day).

## Root cause

Not a config problem and not a general outage. The feed responds fine from a
normal network but *hangs* (connection blackholed, not a fast reject) for
GitHub's runner IPs during certain windows:

- From a home/office machine: HTTP 200 in ~0.2s, consistently.
- From a datacenter IP: HTTP 200 (~0.8s) when tested manually.
- From the GitHub runner (Azure ranges): full 60s timeout on every attempt for
  the entire ~11-minute run, yet a different run the same day succeeded in 6s.

Pattern = **intermittent** unreachability from GitHub's Azure ranges, consistent
with a rate limit or bot-protection rule (e.g. Cloudflare Bot Fight Mode /
rate limiting / a WAF rule) that trips under certain conditions, not an
always-on block. The `url.parse()` DeprecationWarning in the log is unrelated
noise from inside the action.

## What was changed on the workflow side

- `retry_count` 3 -> 5, `retry_wait_time` 5s -> 60s. Covers short blips
  (~10 min window now). Does NOT fix a sustained block: if the site refuses the
  runner for the whole window, more retries just cost more minutes.
- `actions/checkout@v4` -> `@v5` (Node 24 runtime). Clears an unrelated Node 20
  deprecation warning. Not related to the timeout.

## The real fix (site-side, not in this repo)

Check the host/CDN in front of `albertoarena.it` for anything throttling Azure /
GitHub Actions IP ranges, and allowlist the feed path (`/rss.xml`) or those
ranges. If it's Cloudflare: look at Bot Fight Mode / Super Bot Fight Mode,
rate-limiting rules, and WAF rules.

Confirm with a datacenter-sourced request during a failing window (should hang
like the runner does, while a local request stays instant).

## If red runs become annoying and the site fix isn't done

Options, in order of preference:
- Invert the flow: have the blog build push the post list into this README
  instead of pulling it here. Removes the Azure-IP failure entirely. Full spec
  in docs/blog-push-handoff.md.
- Leave it: the daily schedule usually lands in a good window and nothing is
  miscommitted on failure.
- Fetch the feed via a proxy/path not subject to the bot rule. Note: free public
  proxies tested unreliable (allorigins times out, corsproxy.io is paywalled).
- Mark the fetch step `continue-on-error: true` so the job stays green (hides
  genuine failures too, so only if the noise outweighs that).

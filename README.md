<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/banner-dark.svg">
  <img src="./assets/banner-light.svg" alt="Alberto Arena, senior software engineer. Developer tooling for Laravel and event sourcing. An ER diagram in the style of Truss: filament_truss and homebrew_truss both reference laravel_truss.">
</picture>

Senior software engineer. I build and maintain developer tooling for Laravel and event
sourcing, and I write about Laravel, developer tools, and AI-assisted coding with Claude Code.

## Truss

[**Truss**](https://github.com/albertoarena/laravel-truss) is a live ER diagram (ERD) of your Laravel schema, safe to run against production. It exports the schema as grounding context for a coding agent, over an optional read-only MCP server, and `truss:doctor` reviews the structure for problems and can fail your CI build. Structure only, never data.

```bash
composer require albertoarena/laravel-truss --dev
```

Then open `/truss` in your local app. Outside a Laravel app, `brew install albertoarena/truss/truss` puts the standalone `truss` binary on your PATH.

<a href="https://packagist.org/packages/albertoarena/laravel-truss"><img src="https://img.shields.io/packagist/dt/albertoarena/laravel-truss?style=flat-square&label=downloads&color=2b8bf0" alt="Packagist downloads" align="absmiddle"></a> · [Docs](https://trussphp.com/?utm_source=github&utm_medium=profile&utm_campaign=bio) · [Source](https://github.com/albertoarena/laravel-truss)

- [filament-truss](https://github.com/albertoarena/filament-truss) puts the Truss diagram of your real database inside a Filament panel.
- [homebrew-truss](https://github.com/albertoarena/homebrew-truss) is the Homebrew tap for the standalone Truss binary: schema viewer, ER diagram and schema doctor.

> [!TIP]
> Available for freelance and consulting work. [Here is what that looks like](https://albertoarena.it/pages/consulting/?utm_source=github&utm_medium=profile&utm_campaign=consulting).

## More I maintain

| Area | Project | What it does |
| --- | --- | --- |
| Event sourcing | [laravel-event-sourcing-generator](https://github.com/albertoarena/laravel-event-sourcing-generator) | Scaffolds a whole Spatie domain from one Artisan command. <a href="https://packagist.org/packages/albertoarena/laravel-event-sourcing-generator"><img src="https://img.shields.io/packagist/dt/albertoarena/laravel-event-sourcing-generator?style=flat-square&label=downloads&color=2b8bf0" alt="Packagist downloads" align="absmiddle"></a> |
| | [filament-event-sourcing](https://github.com/albertoarena/filament-event-sourcing) | Brings Spatie event sourcing into Filament v4: write through aggregates, browse stored events, replay projectors. |
| Claude Code | [claude-laravel-event-sourcing](https://github.com/albertoarena/claude-laravel-event-sourcing) | Designs and generates event-sourced domain code. A test of how far a skill can replace a code generator. |
| | [claude-code-laravel-starter](https://github.com/albertoarena/claude-code-laravel-starter) | A lean docs skeleton for Laravel. CLAUDE.md is RAM, docs/ is disk. |
| Laravel | [codemetry-laravel](https://github.com/albertoarena/codemetry-laravel) | Git-powered code quality metrics for a Laravel app. |
| Sites and apps | [PHP Lives](https://phplives.dev/) | Real things people shipped with PHP in the last six months. [Source](https://github.com/albertoarena/phplives.dev) |
| | [Cogway](https://cogway.dev/?utm_source=github&utm_medium=profile&utm_campaign=projects) | Interactive explainers for Laravel and PHP. |
| | [Sudo Play](https://sudoplay.dev/?utm_source=github&utm_medium=profile&utm_campaign=projects) | A macOS menu bar app that picks today's Spotify playlist from what you are coding and how you feel about it. [Source](https://github.com/albertoarena/sudo-play) |
| | [tryJev](https://tryjev.albertoarena.it/?utm_source=github&utm_medium=profile&utm_campaign=projects) | An unofficial demo for Jev, a TypeSafe AI model that answers typed questions with calibrated probabilities. |

<details>
<summary>Smaller tools</summary>

- [envaudit](https://github.com/albertoarena/envaudit) validates `.env` files in a pipeline. Zero dependencies.
- [laravel-netsons-deploy](https://github.com/albertoarena/laravel-netsons-deploy) deploys a Laravel app to Netsons shared hosting from a GitHub Action.
- [github-traffic-badge](https://github.com/albertoarena/github-traffic-badge) turns repository traffic into a badge.

</details>

[All projects](https://albertoarena.it/projects/?utm_source=github&utm_medium=profile&utm_campaign=projects)

## Writing

<!-- BLOG-POST-LIST:START -->
- Oct 23, 2026 · [A job offer came from a recruiter who passed away years ago](https://albertoarena.it/posts/a-job-offer-came-from-a-recruiter-who-passed-away-years-ago/)
- Oct 19, 2026 · [PHP is not dead. Here is what people shipped](https://albertoarena.it/posts/php-is-not-dead-here-is-what-people-shipped/)
- Oct 15, 2026 · [What I Learned From My First Claude Code Cloud Sessions](https://albertoarena.it/posts/what-i-learned-from-my-first-claude-code-cloud-sessions/)
- Oct 8, 2026 · [The Last Man Who Could Read Code: A Short Story](https://albertoarena.it/posts/the-last-man-who-could-read-code/)
- Oct 5, 2026 · [The Sound of Silence: What Your AI Agent Does Not Tell You](https://albertoarena.it/posts/the-sound-of-silence/)<!-- BLOG-POST-LIST:END -->

[Read all posts on my blog](https://albertoarena.it/?utm_source=github&utm_medium=profile&utm_campaign=bio&utm_content=posts) · [RSS](https://albertoarena.it/rss.xml)

**Newsletter.** Subscribe and get my free Spatie Event Sourcing cheat sheet (printable PDF), plus practical notes on Laravel and AI-assisted development, roughly once a month. No spam. **[Get the cheat sheet →](https://albertoarena.it/subscribe/?utm_source=github&utm_medium=profile&utm_campaign=newsletter&utm_content=albertoarena)**

---

[albertoarena.it](https://albertoarena.it/?utm_source=github&utm_medium=profile&utm_campaign=bio&utm_content=footer) · [dev/null](https://dev-null.sh/@albertoarena) · [Mastodon](https://phpc.social/@albertoarena) · [@alberto_arena](https://x.com/alberto_arena) · [LinkedIn](https://www.linkedin.com/in/alberto-arena-ba44a624/) · [YouTube](https://www.youtube.com/@AlbertoArenaDev)

# notfound blog — design

**Date:** 2026-05-07
**Author:** notfound
**Status:** Approved

## Goal

Stand up a personal blog and reference site for the `notfound` security-research persona. The site will host long-form pages (about, security policy, contact, PGP, tools, resources) and serve as the future home for technical writeups (RE notes, malware analyses, exploit case studies). No posts at launch — only supporting pages.

The site complements the GitHub profile repo (`notfoundsoft/notfoundsoft`). The blog gets its own repo and serves at the GitHub Pages user-site URL.

## Constraints and Decisions

| Decision           | Choice                                                                 |
|--------------------|------------------------------------------------------------------------|
| SSG                | Jekyll                                                                 |
| Theme              | `jekyll-theme-hacker` (GitHub built-in supported list)                 |
| Build              | Native GitHub Pages Jekyll build (no Action workflow)                  |
| Repo name          | `notfoundsoft.github.io` (user-site convention)                        |
| Live URL           | `https://notfoundsoft.github.io`                                       |
| Local dir          | `/home/anthony/Projects/security/notfound_blog`                        |
| Default branch     | `main`, source from repo root                                          |
| Plugins            | `jekyll-feed`, `jekyll-seo-tag`, `jekyll-sitemap`                      |
| Comments           | None                                                                   |
| Search             | None (revisit when post volume justifies it)                           |
| Permalink          | `/posts/:slug/`                                                        |
| Content license    | CC-BY-4.0 for prose; MIT for site code (templates, CSS, config)        |
| Identity / signing | `notfound <notfoundsoft@gmail.com>`, GPG key `AA18BCA70A410258`        |

## Architecture

### Directory layout

```
notfound_blog/
├── _config.yml
├── _layouts/
│   └── default.html
├── _includes/
│   ├── header.html
│   └── footer.html
├── _posts/                  (empty; .gitkeep)
├── assets/
│   ├── css/overrides.css
│   └── pubkey.asc
├── 404.html
├── index.md
├── about.md
├── security.md
├── contact.md
├── pgp.md
├── tools.md
├── resources.md
├── archive.md
├── feed.xml                 (auto)
├── sitemap.xml              (auto)
├── robots.txt
├── Gemfile
├── .gitignore
├── LICENSE                  (CC-BY-4.0)
├── README.md
└── docs/superpowers/specs/  (this design doc)
```

### Components

- **Layout shell (`_layouts/default.html`)**: overrides the theme default to add site-wide nav (top) and footer (bottom). Wraps theme markup so per-page `content` still renders through the theme.
- **Includes**: `header.html` (nav links to all top-level pages) and `footer.html` (license, RSS link, PGP fingerprint, contact email).
- **Pages**: each top-level Markdown file uses `layout: default` and provides its own content. Pages are not posts — they live at `/about/`, `/security/`, etc.
- **Posts dir**: `_posts/` exists but is empty. Future posts use `_posts/YYYY-MM-DD-slug.md` with front-matter (`title`, `date`, `tags`, `categories`).
- **Asset overrides (`assets/css/overrides.css`)**: small style file loaded after the theme stylesheet to soften contrast, adjust monospace stack, and tune code-block / table styling.
- **404 page (`404.html`)**: custom branded 404 leaning into the `notfound` identity. Static HTML, not Markdown, to avoid layout interference.

### Page content plan

| Page         | Path           | Purpose                                                                  |
|--------------|----------------|--------------------------------------------------------------------------|
| Home         | `/`            | Banner, short pitch, "no posts yet" placeholder, quick links             |
| About        | `/about/`      | Long-form whoami, `/proc/self/status`-style block, background framing    |
| Security     | `/security/`   | Vulnerability disclosure policy (port from profile `SECURITY.md`)        |
| Contact      | `/contact/`    | Email, PGP fingerprint, profile/blog links, encrypted-only emphasis      |
| PGP          | `/pgp/`        | Full fingerprint, `gpg --recv-keys` instructions, `--verify` example     |
| Tools        | `/tools/`      | Toolchain long-form: disassemblers, debuggers, emulation, tracing        |
| Resources    | `/resources/`  | Phrack, books, papers, CTFs, trackers                                    |
| Archive      | `/archive/`    | Posts list (empty) and tag list (empty); ready to populate               |
| 404          | `/404.html`    | Branded not-found page                                                   |
| RSS feed     | `/feed.xml`    | Auto via `jekyll-feed`                                                   |
| Sitemap      | `/sitemap.xml` | Auto via `jekyll-sitemap`                                                |
| Robots       | `/robots.txt`  | Allow all; reference sitemap                                             |

### Theme and accessibility

`jekyll-theme-hacker` ships with bright green-on-black. Pure `#33ff33` body text fails WCAG AA contrast.

**Mitigation**: `assets/css/overrides.css` softens body text to a mid-green readable on black while preserving full bright accent on headings, links, and code highlights. Keeps the Matrix energy without making prose painful.

Other CSS overrides:
- Code blocks: padding and border tweaks
- Tables: thin borders, cell spacing
- Web font: `JetBrains Mono` loaded via Google Fonts as the primary monospace stack, with theme default and `monospace` as fallbacks. Loaded with `font-display: swap` so first paint isn't blocked.

### Navigation

Single top bar: `whoami | security | contact | pgp | tools | resources | archive`. The wordmark "notfound" links to `/`. No sidebar.

Footer: license line, RSS link, PGP fingerprint, contact email.

### Cross-linking

- Profile repo README currently references `notfound.github.io` (broken — username is `notfoundsoft`). Update both web and blog lines to `notfoundsoft.github.io` as part of this work.
- Blog footer + about page link to `https://github.com/notfoundsoft`.
- Same PGP fingerprint and email used in both repos.

## Configuration

### `_config.yml`

```yaml
title: notfound
tagline: HTTP/1.1 404 — not the binary you're looking for
description: Reverse engineering, malware analysis, exploit analysis. Linux x86_64 / ARM / AArch64.
url: https://notfoundsoft.github.io
author:
  name: notfound
  email: notfoundsoft@gmail.com
theme: jekyll-theme-hacker
plugins:
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-sitemap
permalink: /posts/:slug/
```

### `Gemfile`

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
```

The `github-pages` meta-gem pins Jekyll, the theme, and the three plugins to versions that GitHub Pages actually serves. No drift between local preview and production.

### `.gitignore`

Standard Jekyll: `_site/`, `.jekyll-cache/`, `.jekyll-metadata`, `vendor/`, `.bundle/`. Plus OS junk and editor swap files.

## Deployment

1. Push to `notfoundsoft/notfoundsoft.github.io` `main` branch.
2. Repo settings → Pages → Source: deploy from branch `main`, root `/`.
3. GitHub Pages serves `https://notfoundsoft.github.io` automatically. No Action workflow.

Local preview (developer-only, not required to ship): `bundle install && bundle exec jekyll serve`. Documented in repo `README.md`.

## Out of scope

- Individual blog posts (none at launch)
- Custom domain registration / DNS
- Analytics
- Newsletter / email subscription
- Comments
- Site search
- Math typesetting (KaTeX/MathJax)
- Webmentions / IndieWeb
- Dark/light mode toggle

Each is a future addition if and when justified.

## Future work

- First post — once a writeup is ready
- Tag and category pages
- Site search (Lunr.js or similar) when post count exceeds ~30
- Custom domain if acquired
- Theme refinement or migration if `jekyll-theme-hacker` proves limiting

## Identity and signing

- Local git identity: `notfound <notfoundsoft@gmail.com>` (repo-scoped via `git config --local`)
- GPG signing key: `AA18BCA70A410258` (ed25519 primary, cv25519 encrypt subkey)
- All commits to be signed
- Push via existing SSH alias `github-notfoundsoft`

## Acceptance criteria

The site is considered complete when:

- All nine top-level pages plus the 404 are published and reachable
- Top nav appears on every page
- RSS feed and sitemap are served
- The site is reachable at `https://notfoundsoft.github.io`
- Profile repo links updated to match
- All commits are GPG-signed and authored as `notfound <notfoundsoft@gmail.com>`

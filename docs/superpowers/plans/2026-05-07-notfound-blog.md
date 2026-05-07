# notfound Blog Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up a Jekyll-based GitHub Pages site at `https://notfoundsoft.github.io` with nine top-level pages, a custom 404, and an empty posts framework — no posts yet.

**Architecture:** Native GitHub Pages Jekyll build. `jekyll-theme-hacker` as base, customized via a single `_layouts/default.html` shell + `assets/css/overrides.css`. No build action; GitHub Pages does it on push to `main`.

**Tech Stack:** Jekyll, Liquid, the `github-pages` meta-gem, plugins `jekyll-feed`, `jekyll-seo-tag`, `jekyll-sitemap`. Ruby toolchain for local preview. Markdown + a single static HTML page.

**Working directory:** `/home/anthony/Projects/security/notfound_blog`
**Remote:** `git@github-notfoundsoft:notfoundsoft/notfoundsoft.github.io.git`
**Spec:** `docs/superpowers/specs/2026-05-07-notfound-blog-design.md`

---

## File Structure

Files created by this plan (relative to repo root):

```
.gitignore
Gemfile
_config.yml
README.md
LICENSE
LICENSE-CODE
robots.txt
404.html
index.md
about.md
security.md
contact.md
pgp.md
tools.md
resources.md
archive.md
_layouts/default.html
_includes/header.html
_includes/footer.html
_posts/.gitkeep
assets/css/overrides.css
assets/pubkey.asc
```

---

## Task 1: Bootstrap (Gemfile, config, README, .gitignore)

**Files:**
- Create: `Gemfile`
- Create: `_config.yml`
- Create: `.gitignore`
- Create: `README.md`

- [ ] **Step 1: Create `Gemfile`**

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
```

- [ ] **Step 2: Create `_config.yml`**

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
exclude:
  - Gemfile
  - Gemfile.lock
  - vendor
  - docs
  - README.md
```

- [ ] **Step 3: Create `.gitignore`**

```
# Jekyll
_site/
.jekyll-cache/
.jekyll-metadata
vendor/
.bundle/

# OS
.DS_Store
Thumbs.db

# Editors
*.swp
*.swo
*~
.vscode/
.idea/

# Claude Code
.claude/settings.local.json
```

- [ ] **Step 4: Create `README.md`**

```markdown
# notfoundsoft.github.io

Source for <https://notfoundsoft.github.io>.

## Local preview

```
bundle install
bundle exec jekyll serve
```

Then open <http://127.0.0.1:4000>.

## Structure

- Markdown pages at the repo root
- Layout shell: `_layouts/default.html`
- Theme: `jekyll-theme-hacker` with overrides in `assets/css/overrides.css`
- Posts (when added): `_posts/YYYY-MM-DD-slug.md`

## Licenses

- Prose: CC-BY-4.0 (`LICENSE`)
- Site code (templates, CSS, config): MIT (`LICENSE-CODE`)
```

- [ ] **Step 5: Verify build succeeds**

```
bundle install
bundle exec jekyll build
```

Expected: site builds without errors. `_site/` directory created. `_site/index.html` does NOT exist yet (no `index.md`), but build itself should succeed without "file not found" errors.

If `bundle install` fails with Ruby version errors, check `ruby --version` is at least 2.7. The `github-pages` gem documents its required Ruby version.

- [ ] **Step 6: Commit**

```
git add Gemfile Gemfile.lock _config.yml .gitignore README.md
git commit -m "Bootstrap Jekyll site"
```

---

## Task 2: Layout shell (default layout + nav include + footer include)

**Files:**
- Create: `_layouts/default.html`
- Create: `_includes/header.html`
- Create: `_includes/footer.html`

- [ ] **Step 1: Create `_includes/header.html`**

```html
<nav class="site-nav">
  <a class="brand" href="/">notfound</a>
  <span class="sep">~</span>
  <a href="/about/">whoami</a>
  <a href="/security/">security</a>
  <a href="/contact/">contact</a>
  <a href="/pgp/">pgp</a>
  <a href="/tools/">tools</a>
  <a href="/resources/">resources</a>
  <a href="/archive/">archive</a>
</nav>
```

- [ ] **Step 2: Create `_includes/footer.html`**

```html
<footer class="site-footer">
  <div class="footer-line">
    prose CC-BY-4.0 · code MIT · <a href="/feed.xml">RSS</a> · <a href="https://github.com/notfoundsoft">github</a>
  </div>
  <div class="footer-line mono">
    pgp C80C 3C16 6DEF C9D7 8C03  23BE AA18 BCA7 0A41 0258 ·
    <a href="/assets/pubkey.asc">pubkey.asc</a>
  </div>
</footer>
```

- [ ] **Step 3: Create `_layouts/default.html`**

This wraps the `jekyll-theme-hacker` base by emitting our own document and pulling the theme's stylesheet plus our overrides. Theme layout is overridden entirely so we control nav and footer placement.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  {% seo %}
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap">
  <link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">
  <link rel="stylesheet" href="{{ '/assets/css/overrides.css' | relative_url }}">
  <link rel="alternate" type="application/rss+xml" title="{{ site.title }}" href="{{ '/feed.xml' | relative_url }}">
</head>
<body>
  <div class="wrapper">
    {%- include header.html -%}
    <main class="content">
      {{ content }}
    </main>
    {%- include footer.html -%}
  </div>
</body>
</html>
```

Note: the theme ships its CSS at `/assets/css/style.css`. Our overrides load after it so they win.

- [ ] **Step 4: Build and verify the layout is wired**

```
bundle exec jekyll build
```

Expected: build succeeds.

```
grep -q "site-nav" _site/feed.xml || echo "no pages yet, layout untested"
```

Layout is verified end-to-end in Task 4 once `index.md` exists. For now, the build passing with no syntax errors is enough.

- [ ] **Step 5: Commit**

```
git add _layouts _includes
git commit -m "Add default layout, header, and footer"
```

---

## Task 3: CSS overrides (contrast + JetBrains Mono)

**Files:**
- Create: `assets/css/overrides.css`

- [ ] **Step 1: Create `assets/css/overrides.css`**

```css
:root {
  --fg: #b9ffb9;
  --fg-bright: #33ff33;
  --bg: #0a0f0a;
  --accent: #5cff5c;
  --muted: #6f9f6f;
}

html, body {
  font-family: "JetBrains Mono", "DejaVu Sans Mono", "Menlo", monospace;
  font-size: 15px;
  line-height: 1.55;
  color: var(--fg);
  background: var(--bg);
}

h1, h2, h3, h4, h5, h6 {
  color: var(--fg-bright);
  font-weight: 700;
}

a {
  color: var(--accent);
  text-decoration: underline;
  text-decoration-thickness: 1px;
  text-underline-offset: 2px;
}
a:hover { color: var(--fg-bright); }

code, pre {
  font-family: "JetBrains Mono", "DejaVu Sans Mono", "Menlo", monospace;
}

pre {
  background: #050805;
  border: 1px solid #1a3a1a;
  padding: 12px 14px;
  overflow-x: auto;
}

code {
  background: #050805;
  padding: 1px 4px;
  border-radius: 2px;
}

table {
  border-collapse: collapse;
  margin: 1em 0;
}
th, td {
  border: 1px solid #1a3a1a;
  padding: 6px 10px;
}
th {
  color: var(--fg-bright);
  text-align: left;
}

blockquote {
  border-left: 2px solid var(--muted);
  margin: 1em 0;
  padding: 0.2em 1em;
  color: var(--muted);
}

.wrapper {
  max-width: 880px;
  margin: 2em auto;
  padding: 0 1.2em;
}

.site-nav {
  margin-bottom: 1.5em;
  padding-bottom: 0.5em;
  border-bottom: 1px solid #1a3a1a;
}
.site-nav .brand {
  color: var(--fg-bright);
  font-weight: 700;
  margin-right: 0.5em;
}
.site-nav .sep { color: var(--muted); margin: 0 0.4em; }
.site-nav a { margin-right: 0.6em; }

.site-footer {
  margin-top: 3em;
  padding-top: 1em;
  border-top: 1px solid #1a3a1a;
  color: var(--muted);
  font-size: 0.92em;
}
.site-footer .footer-line { margin: 0.3em 0; }
.site-footer .mono { word-break: break-all; }

hr {
  border: 0;
  border-top: 1px dashed #1a3a1a;
  margin: 2em 0;
}
```

- [ ] **Step 2: Build**

```
bundle exec jekyll build
test -f _site/assets/css/overrides.css && echo "OK" || echo "MISSING"
```

Expected: `OK`.

- [ ] **Step 3: Commit**

```
git add assets/css/overrides.css
git commit -m "Add CSS overrides: JetBrains Mono, contrast tweaks"
```

---

## Task 4: Home page (`index.md`)

**Files:**
- Create: `index.md`

- [ ] **Step 1: Create `index.md`**

````markdown
---
layout: default
title: notfound
---

```
              _    __                       _
  _ __   ___ | |_ / _| ___  _   _ _ __   __| |
 | '_ \ / _ \| __| |_ / _ \| | | | '_ \ / _` |
 | | | | (_) | |_|  _| (_) | |_| | | | | (_| |
 |_| |_|\___/ \__|_|  \___/ \__,_|_| |_|\__,_|
```

> `HTTP/1.1 404` — not the binary you're looking for.

Reverse engineer. Malware analyst. Exploit analyst.
Userland to ring 0, on `x86_64` and `ARM/AArch64`.

I read binaries the way other people read novels. Slower. Less linear. More footnotes. Outputs ship to defenders: detections, mitigations, advisories, hardening notes.

## $ ls latest/

No posts yet. The lab is busy. Output drops here when something's worth reading.

## $ ls pages/

- [whoami](/about/) — long-form intro
- [security](/security/) — vulnerability disclosure policy
- [contact](/contact/) — email, links
- [pgp](/pgp/) — public key + verify
- [tools](/tools/) — toolchain
- [resources](/resources/) — phrack, papers, CTFs, books
- [archive](/archive/) — posts (empty for now)
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
grep -q "site-nav" _site/index.html && echo "nav OK" || echo "NAV MISSING"
grep -q "JetBrains" _site/index.html && echo "font OK" || echo "FONT MISSING"
grep -q "404" _site/index.html && echo "tagline OK" || echo "TAGLINE MISSING"
```

Expected: three OK lines.

- [ ] **Step 3: Commit**

```
git add index.md
git commit -m "Add home page"
```

---

## Task 5: About page (`about.md`)

**Files:**
- Create: `about.md`

- [ ] **Step 1: Create `about.md`**

````markdown
---
layout: default
title: whoami
permalink: /about/
---

# `$ whoami`

Reverse engineer. Malware analyst. Exploit analyst. Userland to ring 0, on `x86_64` and `ARM/AArch64`.

```
Name:       notfound
State:      R (reversing)
TracerPid:  0
SigCgt:     SIGSEGV SIGBUS SIGILL  (caught, analyzed, filed)
Threads:    probably too many
```

## What I do

I read binaries. The fun ones don't ship symbols. I'm interested in what's actually executing — not what the README claims, not what the type system promises. The bug is always in the assumption: mine, the developer's, or the compiler's.

Most of my hours go to:

- Static and dynamic reverse engineering of Linux ELF binaries
- Triage of malware samples — implants, droppers, miners, ransomware, rootkits
- Vulnerability root-cause analysis and exploitability assessment
- Crash and fuzzer-finding triage
- Detection and signature development (YARA, Sigma)
- Mitigation review from the defender's chair

## Why this exists

Defenders need clear, sourced, reproducible analysis. Not flashy demos. Not theatrical disclosures. Output here serves that audience: detections you can run, mitigations you can apply, post-incident understanding you can act on.

## What I'm not

I'm not a vendor. I'm not a journalist. I'm not selling a course. If something looks like marketing copy, it's a bug — open an issue.

## Pointers

- [security policy](/security/)
- [contact](/contact/)
- [pgp](/pgp/)
- [github profile](https://github.com/notfoundsoft)
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/about/index.html && echo "page OK" || echo "MISSING"
grep -q "whoami" _site/about/index.html && echo "content OK" || echo "CONTENT MISSING"
```

Expected: two OK lines.

- [ ] **Step 3: Commit**

```
git add about.md
git commit -m "Add about page"
```

---

## Task 6: Security page (`security.md`)

**Files:**
- Create: `security.md`

- [ ] **Step 1: Create `security.md`**

````markdown
---
layout: default
title: security
permalink: /security/
---

# `$ cat security.policy`

## Scope

This policy covers vulnerabilities in code, configurations, and research artifacts published under the `notfoundsoft` GitHub account. Reports about third-party software should go to the relevant vendor.

## Reporting a vulnerability

Send reports to <notfoundsoft@gmail.com>, encrypted with the PGP key on the [pgp page](/pgp/).

Include:

- Affected repository, commit, or release
- Reproduction steps or proof of concept
- Impact assessment
- Suggested mitigation, if known

## Coordinated disclosure

- Acknowledgement within 72 hours of receipt
- Initial assessment within 7 days
- Fix timeline depends on severity and complexity. Communicated case by case.
- Public disclosure coordinated with the reporter, typically after a fix is available or a 90-day window has elapsed

## Out of scope

- Issues in unmaintained or archived repositories
- Vulnerabilities requiring privileged local access already granted by the user
- Theoretical issues without demonstrated impact
- Findings produced solely by automated scanners with no validation

## Safe harbor

Good-faith research conducted under this policy will not be referred for legal action. Avoid privacy violations, data destruction, service disruption, and any access to systems or data beyond what is necessary to demonstrate the issue.
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/security/index.html && echo "OK" || echo "MISSING"
```

Expected: `OK`.

- [ ] **Step 3: Commit**

```
git add security.md
git commit -m "Add security policy page"
```

---

## Task 7: Contact page (`contact.md`)

**Files:**
- Create: `contact.md`

- [ ] **Step 1: Create `contact.md`**

````markdown
---
layout: default
title: contact
permalink: /contact/
---

# `$ finger notfound`

```
Login:       notfound
Name:        notfound
Office:      /lab
On since:    a while
Mail:        notfoundsoft@gmail.com
PGP:         C80C 3C16 6DEF C9D7 8C03  23BE AA18 BCA7 0A41 0258
GitHub:      https://github.com/notfoundsoft
Web:         https://notfoundsoft.github.io
```

Encrypted mail preferred. Plaintext welcome but expect dry replies.

For vulnerability reports, see the [security policy](/security/). For PGP details and verification, see [pgp](/pgp/).

No DMs on platforms I don't list. If a message claims to be from me on Discord, Telegram, Signal, Twitter/X, Mastodon, or anywhere else — it isn't.
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/contact/index.html && echo "OK" || echo "MISSING"
grep -q "notfoundsoft@gmail.com" _site/contact/index.html && echo "email OK" || echo "EMAIL MISSING"
```

Expected: two OK lines.

- [ ] **Step 3: Commit**

```
git add contact.md
git commit -m "Add contact page"
```

---

## Task 8: PGP page (`pgp.md` + asset copy)

**Files:**
- Create: `pgp.md`
- Create: `assets/pubkey.asc` (copied from profile repo)

- [ ] **Step 1: Copy public key from the profile repo**

```
cp /home/anthony/Projects/security/notfound_profile/pubkey.asc assets/pubkey.asc
test -s assets/pubkey.asc && echo "OK" || echo "MISSING"
```

Expected: `OK`. The file should be ~13 lines, beginning with `-----BEGIN PGP PUBLIC KEY BLOCK-----`.

- [ ] **Step 2: Create `pgp.md`**

````markdown
---
layout: default
title: pgp
permalink: /pgp/
---

# `$ gpg --list-keys notfound`

```
pub   ed25519/AA18BCA70A410258 2026-05-07 [SC] [expires: 2028-05-06]
      C80C3C166DEFC9D78C0323BEAA18BCA70A410258
uid                            notfound <notfoundsoft@gmail.com>
sub   cv25519/5957F2B8BEC1F4F5 2026-05-07 [E]  [expires: 2028-05-06]
```

**Fingerprint:** `C80C 3C16 6DEF C9D7 8C03  23BE AA18 BCA7 0A41 0258`

[Download `pubkey.asc`](/assets/pubkey.asc)

## Import

From this site:

```
curl -fsSL https://notfoundsoft.github.io/assets/pubkey.asc | gpg --import
```

From a keyserver:

```
gpg --keyserver hkps://keys.openpgp.org --recv-keys C80C3C166DEFC9D78C0323BEAA18BCA70A410258
```

## Verify

After import, confirm the fingerprint matches before trusting:

```
gpg --fingerprint notfoundsoft@gmail.com
```

Expected output should match the block above. If it doesn't, do not trust the key.

## Encrypt mail to me

```
gpg --encrypt --armor -r notfoundsoft@gmail.com message.txt
```

## Verify a signed commit or release

```
git verify-commit <commit-sha>
git verify-tag    <tag>
```

## Notes

- `[SC]` primary key signs and certifies. Long-lived identity.
- `[E]` cv25519 subkey handles encryption. Rotates independently.
- Expires 2028-05-06. Watch this page for renewal or rotation announcements.
- If this key is revoked, a notice will appear at the top of [contact](/contact/) and on the GitHub profile README.
````

- [ ] **Step 3: Build and verify**

```
bundle exec jekyll build
test -f _site/pgp/index.html && echo "page OK" || echo "PAGE MISSING"
test -f _site/assets/pubkey.asc && echo "key OK" || echo "KEY MISSING"
grep -q "C80C3C166DEFC9D78C0323BEAA18BCA70A410258" _site/pgp/index.html && echo "fp OK" || echo "FP MISSING"
```

Expected: three OK lines.

- [ ] **Step 4: Commit**

```
git add pgp.md assets/pubkey.asc
git commit -m "Add pgp page and public key asset"
```

---

## Task 9: Tools page (`tools.md`)

**Files:**
- Create: `tools.md`

- [ ] **Step 1: Create `tools.md`**

````markdown
---
layout: default
title: tools
permalink: /tools/
---

# `$ which $TOOLS`

A working set, not a worship list. Each tool earns its place by what I do with it. Substitutes exist for all of them.

## Disassemblers

- **Ghidra** — primary for cross-arch and large binaries. Decompiler is honest about what it doesn't know.
- **IDA Pro** — used where it wins: legacy projects, plugin ecosystem, PDB-heavy targets.
- **Binary Ninja** — clean MLIL/HLIL pivots, scriptable, fast.
- **radare2 / rizin** — terminal-native. Indispensable for scripting, batch jobs, and quick looks.

## Debuggers

- **GDB** — with `pwndbg` or `gef`. Layout, heap inspection, breakpoint scripting.
- **LLDB** — when GDB stalls or when working LLVM-side.
- **rr** — record-replay debugging. Time travel beats guessing.

## Emulation and tracing

- **QEMU** — both user-mode for cross-arch ELF and system-mode for kernel and embedded targets.
- **strace / ltrace** — first pass. Cheap, fast, often enough.
- **eBPF / bpftrace** — when syscall tracing isn't enough and the host is real.

## Binutils

- **objdump, readelf, nm, file** — read first, write later.
- **xxd, hexyl** — for the dumps that don't deserve a disassembler.

## Detection and signatures

- **YARA** — sample families, loader stages, capability tagging.
- **Sigma** — host telemetry and EDR-agnostic detections.
- **CAPA** — for capability inventory before deeper triage.

## Languages

- **C** — to read what the targets are written in.
- **Python** — to glue, script, and prototype tooling.
- **Assembly** (`x86_64`, `ARM`, `AArch64`) — to verify what the decompiler shows.
- **Rust** — for tooling I want to run for years without revisiting.

## Notes

I do not list IDEs, terminals, fonts, or shell aliases. Those change. The tools above don't.
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/tools/index.html && echo "OK" || echo "MISSING"
```

Expected: `OK`.

- [ ] **Step 3: Commit**

```
git add tools.md
git commit -m "Add tools page"
```

---

## Task 10: Resources page (`resources.md`)

**Files:**
- Create: `resources.md`

- [ ] **Step 1: Create `resources.md`**

````markdown
---
layout: default
title: resources
permalink: /resources/
---

# `$ cat resources.md`

A starting library. Not exhaustive, not sacred. The right path is the one you finish.

## Foundational reading

- *Smashing The Stack For Fun And Profit* — Aleph One, **Phrack 49**. The canonical entry point.
- *Hacking: The Art of Exploitation* — Jon Erickson. Build the mental model from C and assembly up.
- *The Art of Software Security Assessment* — Dowd, McDonald, Schuh. The auditor's reference.
- *A Guide to Kernel Exploitation* — Perla, Oldani. Kernel-side primitives.
- *Practical Reverse Engineering* — Dang, Gazet, Bachaalany. RE fundamentals across architectures.

## Phrack — recommended issues

- 49.14 — *Smashing The Stack* (Aleph One)
- 56.7  — *Once upon a free()...* (heap unlink, anonymous)
- 57.8  — *Vudo Malloc Tricks* (Michel Kaempf / MaXX)
- 58.4  — *Advanced return-into-lib(c) exploits* (Nergal)
- 67.10 — *A Eulogy for Format Strings* (captain planet)

Browse the rest at <https://www.phrack.org/>.

## Papers worth keeping open

- *On the Effectiveness of Address-Space Randomization* — Shacham et al. The ASLR honesty paper.
- *Counterfeit Object-oriented Programming* — Schuster et al. Why CFI is hard in C++.
- *Q: Exploit Hardening Made Easy* — Schwartz, Avgerinos, Brumley. Automating ROP gadget search.
- *Automatic Exploit Generation* — Avgerinos, Cha, Hao, Brumley.
- *Fuzz Testing in Practice* — Manès et al. (survey).

## Trackers worth watching

- Project Zero blog: <https://googleprojectzero.blogspot.com/>
- OSS-Fuzz issue tracker: <https://issues.oss-fuzz.com/>
- LWN.net (kernel and security): <https://lwn.net/>
- kernel.org CVE list: <https://lore.kernel.org/linux-cve-announce/>
- CISA KEV catalog: <https://www.cisa.gov/known-exploited-vulnerabilities-catalog>

## Wargames and CTFs

- **microcorruption** — embedded reverse engineering. <https://microcorruption.com/>
- **pwnable.kr** — pwning fundamentals. <https://pwnable.kr/>
- **pwnable.tw** — harder pwn. <https://pwnable.tw/>
- **ROP Emporium** — ROP-only challenges. <https://ropemporium.com/>
- **pwn.college** — structured curriculum. <https://pwn.college/>
- **crackmes.one** — RE practice samples. <https://crackmes.one/>

## Historical and cultural

- **Phrack** archives: <https://www.phrack.org/archives/>
- **2600: The Hacker Quarterly** — context for where the culture came from.
- **The Cuckoo's Egg** — Cliff Stoll. Defender's-eye view of an early intrusion. Still relevant.
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/resources/index.html && echo "OK" || echo "MISSING"
grep -q "Phrack" _site/resources/index.html && echo "content OK" || echo "CONTENT MISSING"
```

Expected: two OK lines.

- [ ] **Step 3: Commit**

```
git add resources.md
git commit -m "Add resources page"
```

---

## Task 11: Archive page (`archive.md`)

**Files:**
- Create: `archive.md`

- [ ] **Step 1: Create `archive.md`**

````markdown
---
layout: default
title: archive
permalink: /archive/
---

# `$ ls _posts/`

```
total 0
```

No posts yet.

When something's worth publishing, it lands here. Until then, the lab is busy.

Subscribe to the [RSS feed](/feed.xml) if you'd rather not check.

---

## When posts exist

This page will list them, newest first, with title, date, and tags. Tag pages will be linked from each entry. Search will land here if and when post volume justifies it.
````

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/archive/index.html && echo "OK" || echo "MISSING"
```

Expected: `OK`.

- [ ] **Step 3: Commit**

```
git add archive.md
git commit -m "Add archive placeholder page"
```

---

## Task 12: Custom 404 page (`404.html`)

**Files:**
- Create: `404.html`

- [ ] **Step 1: Create `404.html`**

```html
---
layout: default
title: 404 — not found
permalink: /404.html
---

<style>
  .e404-banner { color: var(--fg-bright); white-space: pre; line-height: 1.1; }
  .e404-tag { color: var(--muted); margin-top: 1em; }
  .e404-actions { margin-top: 2em; }
</style>

<pre class="e404-banner">
  _  _    ___  _  _
 | || |  / _ \| || |
 | || |_| | | | || |_
 |__   _| |_| |__   _|
    |_|  \___/   |_|
</pre>

<p class="e404-tag">HTTP/1.1 404 — the resource exists. You're just looking at the wrong address.</p>

<div class="e404-actions">
  <p>Try one of these:</p>
  <ul>
    <li><a href="/">home</a></li>
    <li><a href="/about/">whoami</a></li>
    <li><a href="/archive/">archive</a></li>
    <li><a href="/feed.xml">rss</a></li>
  </ul>
</div>

<p class="e404-tag">If you arrived here via an external link that should work, please <a href="/contact/">let me know</a>.</p>
```

- [ ] **Step 2: Build and verify**

```
bundle exec jekyll build
test -f _site/404.html && echo "OK" || echo "MISSING"
grep -q "404" _site/404.html && echo "content OK" || echo "CONTENT MISSING"
```

Expected: two OK lines.

- [ ] **Step 3: Commit**

```
git add 404.html
git commit -m "Add custom 404 page"
```

---

## Task 13: Robots, posts placeholder

**Files:**
- Create: `robots.txt`
- Create: `_posts/.gitkeep`

- [ ] **Step 1: Create `robots.txt`**

```
User-agent: *
Allow: /

Sitemap: https://notfoundsoft.github.io/sitemap.xml
```

- [ ] **Step 2: Create `_posts/.gitkeep`** (empty file so the directory ships)

```
touch _posts/.gitkeep
```

- [ ] **Step 3: Build and verify**

```
bundle exec jekyll build
test -f _site/robots.txt && echo "robots OK" || echo "ROBOTS MISSING"
test -f _site/sitemap.xml && echo "sitemap OK" || echo "SITEMAP MISSING"
test -f _site/feed.xml && echo "feed OK" || echo "FEED MISSING"
```

Expected: three OK lines (`sitemap.xml` and `feed.xml` are auto-generated by the plugins).

- [ ] **Step 4: Commit**

```
git add robots.txt _posts/.gitkeep
git commit -m "Add robots.txt and posts dir placeholder"
```

---

## Task 14: License files

**Files:**
- Create: `LICENSE` (CC-BY-4.0 for prose)
- Create: `LICENSE-CODE` (MIT for site code)

- [ ] **Step 1: Create `LICENSE`** (CC-BY-4.0)

The full CC-BY-4.0 legal code is long. Use a short header pointing to the canonical text:

```
Creative Commons Attribution 4.0 International (CC BY 4.0)

Prose content in this repository (Markdown pages, page text) is licensed under
the Creative Commons Attribution 4.0 International License.

You are free to share and adapt the material for any purpose, including
commercially, as long as you give appropriate credit, provide a link to the
license, and indicate if changes were made.

Full license text:
https://creativecommons.org/licenses/by/4.0/legalcode

Attribution suggestion:
"notfound — https://notfoundsoft.github.io"

Site code (templates, CSS, configuration) is licensed separately. See LICENSE-CODE.
```

- [ ] **Step 2: Create `LICENSE-CODE`** (MIT)

```
MIT License

Copyright (c) 2026 notfound

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

Note: this license applies to site code (Liquid templates in `_layouts/` and
`_includes/`, CSS in `assets/css/`, and configuration files such as
`_config.yml` and `Gemfile`). Prose content is licensed under CC-BY-4.0 (see
LICENSE).

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

- [ ] **Step 3: Commit**

```
git add LICENSE LICENSE-CODE
git commit -m "Add licenses: CC-BY-4.0 prose, MIT code"
```

---

## Task 15: Local build smoke test

**Files:** none

- [ ] **Step 1: Full clean build**

```
rm -rf _site .jekyll-cache
bundle exec jekyll build
```

Expected: build succeeds with no errors or warnings.

- [ ] **Step 2: Verify all expected outputs exist**

```
for path in \
  _site/index.html \
  _site/about/index.html \
  _site/security/index.html \
  _site/contact/index.html \
  _site/pgp/index.html \
  _site/tools/index.html \
  _site/resources/index.html \
  _site/archive/index.html \
  _site/404.html \
  _site/robots.txt \
  _site/feed.xml \
  _site/sitemap.xml \
  _site/assets/pubkey.asc \
  _site/assets/css/overrides.css ; do
    test -f "$path" && echo "OK   $path" || echo "MISS $path"
done
```

Expected: all 14 lines printed `OK`.

- [ ] **Step 3: Verify nav appears on a representative sample**

```
for path in _site/index.html _site/about/index.html _site/security/index.html _site/404.html ; do
    grep -q "site-nav" "$path" && echo "OK   $path" || echo "MISS $path"
done
```

Expected: 4 `OK` lines.

- [ ] **Step 4: Optional — open in browser**

```
bundle exec jekyll serve
```

Visit <http://127.0.0.1:4000>. Click through every nav link. Check 404 by visiting <http://127.0.0.1:4000/does-not-exist>. Stop with Ctrl-C.

If anything looks broken, fix it inline and re-run the smoke test. No commit needed if no files changed.

---

## Task 16: Push to GitHub and enable Pages

**Files:** none. This is git + GitHub UI work.

- [ ] **Step 1: Confirm SSH alias works**

```
ssh -T git@github-notfoundsoft 2>&1 | head -3
```

Expected: greeting line addressed to `notfoundsoft`. If denied or wrong user, fix `~/.ssh/config` first.

- [ ] **Step 2: Create the repo on GitHub**

If it does not already exist, create `notfoundsoft.github.io` under the `notfoundsoft` account. Empty repository — no README, no `.gitignore`, no license (we have all three locally).

- [ ] **Step 3: Add remote and push**

```
git remote add origin git@github-notfoundsoft:notfoundsoft/notfoundsoft.github.io.git
git push -u origin main
```

If the push fails with permission denied, verify Step 1.

- [ ] **Step 4: Enable Pages**

In the GitHub web UI, on the new repo:

1. Settings → Pages
2. Source: **Deploy from a branch**
3. Branch: **main**, Folder: **/ (root)**
4. Save

The first build takes 30–90 seconds. Watch the Actions tab for build status.

- [ ] **Step 5: Verify the live site**

```
curl -fsSL https://notfoundsoft.github.io/ -o /tmp/live-home.html
grep -q "site-nav" /tmp/live-home.html && echo "live OK" || echo "LIVE MISSING"
```

Expected: `live OK`. If it returns 404 for a few minutes after Pages is enabled, that's normal — re-run after a wait.

Sample additional checks:

```
curl -fsI https://notfoundsoft.github.io/about/        | head -1
curl -fsI https://notfoundsoft.github.io/feed.xml      | head -1
curl -fsI https://notfoundsoft.github.io/sitemap.xml   | head -1
curl -fsI https://notfoundsoft.github.io/assets/pubkey.asc | head -1
curl -fsI https://notfoundsoft.github.io/does-not-exist | head -1
```

Expected: 200 for the first four, 404 for the last (which renders the custom 404).

---

## Task 17: Update profile repo cross-links

**Files:**
- Modify: `/home/anthony/Projects/security/notfound_profile/README.md`

The profile README currently points to `https://notfound.github.io` for both web and blog. The actual URL is `https://notfoundsoft.github.io`. Fix.

- [ ] **Step 1: Switch directory to the profile repo**

```
cd /home/anthony/Projects/security/notfound_profile
```

- [ ] **Step 2: Update both URLs**

In `README.md`, replace the lines:

```
- web   <https://notfound.github.io>
- blog  <https://notfound.github.io>
```

with:

```
- web   <https://notfoundsoft.github.io>
- blog  <https://notfoundsoft.github.io>
```

- [ ] **Step 3: Verify**

```
grep "notfoundsoft.github.io" README.md
```

Expected: two matching lines.

```
grep "notfound.github.io" README.md | grep -v notfoundsoft
```

Expected: no output.

- [ ] **Step 4: Commit and push**

```
git add README.md
git commit -m "Fix blog URL: notfoundsoft.github.io"
git push
```

The profile repo's history was previously squashed and force-pushed. This is a normal additive commit on top — no force push needed.

---

## Self-Review Checklist (already done)

The plan was reviewed against the spec for: spec coverage, placeholders, type/path consistency. No issues found inline. If the implementer finds gaps, fix and continue.

## Done when

- All 17 tasks complete
- `https://notfoundsoft.github.io/` returns 200 with the home page
- Profile repo README points to `notfoundsoft.github.io`
- Local repo `main` matches `origin/main`, all commits GPG-signed

# Orderly Chaos — Operations Reference

Quick reference for day-to-day blog operations. For the full setup history, see `SETUP.md`.

---

## Site Overview

| | |
|---|---|
| **URL** | https://orderlychaos.io |
| **GitHub repo** | github.com/nkinder/orderlychaos |
| **Generator** | Hugo Extended v0.146.0 |
| **Theme** | PaperMod (git submodule at `themes/PaperMod/`) |
| **Deployment** | GitHub Actions → GitHub Pages (auto on push to `main`) |
| **Domain registrar** | GoDaddy |
| **DNS** | 4 A records → 185.199.108–111.153; CNAME www → nkinder.github.io |

> **Important:** The `nkinder.github.io` repo hosts images used by the Smart Card Removinator README. Do not touch it or redirect it — the blog lives in the separate `orderlychaos` repo.

---

## Writing a Post

### Create from template
```bash
cd ~/Blog/orderlychaos
hugo new posts/my-post-title.md
```
This generates `content/posts/my-post-title.md` using the template in `archetypes/default.md`.

### Front matter fields
```yaml
---
title: "Your Post Title"
date: 2026-04-12
draft: true          # change to false to publish
description: "One sentence summary shown in post listings"
categories: ["FPGA"]
tags: ["verilog", "oscilloscope"]
series: []           # optional — groups related posts
showToc: true
cover:
  image: "images/my-post/cover.jpg"   # optional cover image
  alt: "Description of image"
---
```

### Post structure (from archetypes/default.md)
- Hook (1 paragraph)
- Background (optional — keep brief, link rather than explain)
- The Project (main content — decisions, what didn't work)
- Implementation (annotated code, schematics, captures)
- Results (photos, video, scope captures)
- What's Next
- Resources (links, GitHub repo, datasheets)

### Publish a draft
Change `draft: true` → `draft: false` in the front matter, then commit and push.

---

## Local Preview

```bash
cd ~/Blog/orderlychaos
hugo server -D        # -D includes draft posts
```

Open http://localhost:1313 — changes hot-reload automatically.

To preview published-only (no drafts):
```bash
hugo server
```

---

## Committing and Pushing (via Claude Code)

Open a terminal in `~/Blog/orderlychaos/` and run `claude`, then ask it to commit and push your changes. Or do it manually:

```bash
git add content/posts/my-post.md
git commit -m "Add post: Your Post Title"
git push
```

GitHub Actions builds and deploys automatically. Monitor with:
```bash
gh run list --repo nkinder/orderlychaos
gh run watch --repo nkinder/orderlychaos   # live progress
```

Typical build + deploy time: ~60 seconds.

---

## Adding Images to Posts

Images go in `static/images/your-post-name/`. They're served at `/images/your-post-name/filename.jpg`.

Reference in Markdown:
```markdown
![Alt text](/images/your-post-name/photo.jpg)
```

For a cover image, set `cover.image` in the front matter (path relative to `static/`):
```yaml
cover:
  image: "images/my-post/cover.jpg"
  alt: "Brief description"
  caption: "Optional caption shown under image"
```

---

## Triggering a Manual Rebuild

If CSS or layout looks wrong (usually happens after a config change or domain update):

```bash
gh workflow run hugo.yml --repo nkinder/orderlychaos
gh run watch --repo nkinder/orderlychaos
```

---

## Config Reference

Main config file: `hugo.toml`

| Setting | Location | Notes |
|---|---|---|
| Site title/description | `[params]` | Update `title`, `description` |
| Home page intro text | `[params.homeInfoParams]` | `Title` and `Content` fields |
| Social icons | `[[params.socialIcons]]` | GitHub + RSS configured |
| Dark/light/auto theme | `params.defaultTheme` | Currently `"auto"` (follows system) |
| Posts per page | `[pagination] pagerSize` | Currently 10 |
| Syntax highlight style | `[markup.highlight] style` | Currently `"monokai"` |
| Nav menu items | `[[menu.main]]` | Posts, Categories, Tags, About, Search |
| Comments | `params.comments` | Currently false — enable when giscus is set up |

---

## Cowork + Claude Code Workflow

**Cowork** (the Anthropic desktop app) handles:
- Drafting and editing post content
- Updating config files
- Updating this reference and the blog plan doc
- Generating outlines from uploaded source material (slides, notes)

**Claude Code** (terminal in `~/Blog/orderlychaos/`) handles:
- `git add / commit / push`
- `gh` CLI operations (create repos, trigger workflows, check run status)
- Hugo local commands

Typical flow:
1. Open Cowork → edit or create content files
2. Switch to Claude Code terminal → commit and push
3. Check Actions tab or run `gh run watch` to confirm deploy

---

## Future Enhancements

- [ ] **Comments:** Set up giscus (GitHub Discussions-based). Free, no ads, fits technical audience. See https://giscus.app — set `params.comments = true` in hugo.toml after configuring.
- [ ] **Analytics:** Plausible.io (privacy-friendly) or Google Analytics. Add script in `layouts/partials/extend_head.html`.
- [ ] **Banner/cover images:** PaperMod supports per-post cover images (see front matter above) and a profile image on the home page via `params.profileMode`.
- [ ] **RSS:** Already enabled at https://orderlychaos.io/index.xml
- [ ] **giscus TTL bump:** Once everything is stable, raise GoDaddy DNS TTL from 600 → 3600.

---

## Post Queue

| # | Title | Status |
|---|---|---|
| 1 | DrellaBot: Lights-Out Factory | Ready to Write |
| 2 | CAN Bus Temp Monitor for Racepak | Idea |
| 3 | Smart Card Removinator v2 (FPGA) | Idea |
| 4 | Lissajous on Oscilloscope via FPGA | Idea |
| 5 | Video Synthesis: Rutt/Etra + LZX | Idea |

To start post #1: upload the DrellaBot presentation to Cowork and ask for a blog post outline.

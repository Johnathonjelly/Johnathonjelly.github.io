# The Peripatetic

A personal blog built with [Hugo](https://gohugo.io/) and the
[Blowfish](https://blowfish.page/) theme, deployed automatically to GitHub Pages.

## Getting started (3 steps)

1. **Clone with submodules** (Blowfish is a git submodule):
   ```bash
   git clone --recurse-submodules https://github.com/Johnathonjelly/johnathonjelly.github.io.git
   ```
   Already cloned without `--recurse-submodules`? Run:
   ```bash
   git submodule update --init --recursive
   ```

2. **Enable GitHub Pages**: in the repo, go to **Settings → Pages → Build and
   deployment**, and set **Source** to **GitHub Actions**. The included workflow
   (`.github/workflows/hugo.yml`) builds and deploys on every push to `main`.

3. **Start writing**: add Markdown files under `content/posts/` and push. That's it.

## Writing posts

Create a new post:
```bash
hugo new posts/my-new-post.md
```
Posts use TOML front matter:
```toml
+++
date = '2026-06-25T12:00:00-07:00'
draft = false
title = "My Post Title"
description = "A short summary for listings and SEO"
categories = ["Mathesis"]
tags = ["Hugo", "Writing"]
+++
```
Set `draft = false` to publish. Drafts are not built in production.

## Local development

Requires **Hugo Extended ≥ 0.158.0** (the deploy pipeline uses `0.161.1`).
```bash
hugo server -D    # -D includes drafts; visit http://localhost:1313
```

## Configuration

All site config lives in `config/_default/`:

| File | Purpose |
|------|---------|
| `hugo.toml` | Core Hugo settings, taxonomies, outputs |
| `params.toml` | Theme options — homepage layout, colors, article/list display |
| `languages.en.toml` | Site title, author bio, social links |
| `menus.en.toml` | Header and footer navigation |
| `markup.toml` | Markdown rendering and syntax highlighting |

Common tweaks:

- **Color scheme** — `params.toml` → `colorScheme` (`blowfish`, `slate`, `forest`,
  `fire`, `ocean`, `avocado`, `princess`, `congo`).
- **Homepage layout** — `params.toml` → `[homepage] layout` (currently `background`).
- **Background image** — drop an image at `assets/img/background.svg` (or `.jpg`/`.png`)
  and point `defaultBackgroundImage` / `[homepage] homepageImage` at it.
- **Author photo** — add `assets/img/author.jpg` and uncomment the `image` line in
  `languages.en.toml`.

Full documentation: <https://blowfish.page/docs/>

## Deployment

Pushing to `main` triggers `.github/workflows/hugo.yml`, which installs Hugo
Extended, checks out the theme submodule, builds the site, and publishes it to
GitHub Pages. No manual steps required after the initial Pages setup.

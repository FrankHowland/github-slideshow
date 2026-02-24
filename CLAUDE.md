# CLAUDE.md

This file provides guidance to AI assistants working in this repository.

## Project Overview

This is **github-slideshow**, a [GitHub Learning Lab](https://lab.github.com/) course repository that teaches Git and GitHub through an interactive browser-based slideshow. It uses [Jekyll](https://jekyllrb.com/) as a static site generator and [Reveal.js](https://revealjs.com/) as the presentation framework, intended to be hosted on GitHub Pages.

## Tech Stack

| Layer | Technology |
|---|---|
| Static site generator | Jekyll 3.9 (via `github-pages` gem) |
| Presentation framework | Reveal.js 3.9.2 (npm package) |
| Markdown processor | kramdown with rouge syntax highlighting |
| HTML validation | htmlproofer |
| Hosting | GitHub Pages |
| Runtime | Ruby (Bundler), Node.js (npm) |

## Repository Structure

```
github-slideshow/
├── _config.yml          # Jekyll + Reveal.js configuration
├── index.html           # Entry point: loops through slides and renders them
├── Gemfile              # Ruby dependencies (github-pages gem suite)
├── package-lock.json    # Locked npm dependencies
├── _posts/              # Slide content as Markdown files
│   └── 0000-01-01-intro.md
├── _layouts/            # Jekyll HTML layout templates
│   ├── presentation.html  # Full reveal.js container
│   ├── slide.html         # Individual slide wrapper
│   └── print.html         # Print-optimized layout
├── _includes/           # Reusable Jekyll HTML fragments
│   ├── head.html          # CSS and asset links
│   ├── script.html        # Reveal.js initialization
│   └── slide.html         # Slide content renderer
├── script/              # Shell utility scripts
│   ├── setup            # Install dependencies
│   ├── server           # Run development server
│   ├── cibuild          # CI build and validation
│   └── stage            # Deploy to staging
└── node_modules/        # npm packages (reveal.js)
```

## Development Workflows

### Initial Setup

```bash
script/setup
```

This installs Ruby gems via Bundler (and Homebrew dependencies on macOS).

### Running Locally

```bash
script/server
```

Starts `bundle exec jekyll serve` with live reload. The site is available at `http://localhost:4000`.

Pass extra Jekyll options as arguments:

```bash
script/server --drafts --port 5000
```

### Building for CI

```bash
script/cibuild
```

Builds the site to `_site/` with `--baseurl "."` and then validates HTML using `htmlproofer`. This is the same check that runs in CI.

### Validating HTML Manually

```bash
bundle exec jekyll build --baseurl "."
bundle exec htmlproofer _site/index.html --empty-alt-ignore
```

## Adding Slides

Slides live in `_posts/` as Markdown files. The naming convention is:

```
YYYY-MM-DD-<slug>.md
```

Slides are rendered in **reverse** chronological order by `index.html`, so the file with the earliest date becomes the first slide shown.

Each slide file requires YAML front matter:

```markdown
---
layout: slide
title: "My Slide Title"
---

Slide content goes here.

You can use **Markdown**, HTML, and reveal.js [data attributes](https://revealjs.com/markdown/).
```

## Jekyll Configuration (`_config.yml`)

Key settings:

- `timezone: Europe/Berlin`
- `markdown: kramdown` with `input: GFM` (GitHub Flavored Markdown)
- `highlighter: rouge`
- Reveal.js theme: `solarized` (dark variant)
- Slide transitions: `linear` with `slide` backgrounds
- Presentation dimensions: `1000 × 920 px`
- Slide numbers: `c/t` format (current/total, flattened)

## Code Conventions

### Indentation (enforced by `.editorconfig`)

| File type | Style |
|---|---|
| JSON, JS, CSS, SCSS, YAML, HTML | 2-space indentation |
| Markdown | 4-space indentation |
| All other files | Tabs |

All files use **UTF-8** encoding and **LF** line endings. Trailing whitespace is trimmed.

### Templates

- Jekyll Liquid syntax: `{{ variable }}` and `{% tag %}`.
- Reusable fragments belong in `_includes/`; full page structures in `_layouts/`.
- Reveal.js configuration is expressed as `data-*` attributes on HTML elements (see `_layouts/presentation.html`).

### Markdown Posts

- File names use ISO-8601 date prefix for ordering (`0000-01-01-intro.md`).
- Front matter must include `layout: slide`.
- Keep slide content concise — one concept per slide.

## Dependency Management

### Ruby

Dependencies are managed via Bundler. The `github-pages` gem pins all Jekyll-related packages to the versions used by GitHub Pages.

```bash
bundle install        # install from Gemfile.lock
bundle update         # update all gems within constraints
```

Do **not** modify `Gemfile.lock` manually.

### npm

Only `reveal.js` is tracked as an npm dependency. The `node_modules/` directory is committed to the repository (unusual, but intentional for portability).

```bash
npm install           # restore node_modules if needed
```

## CI / Validation

The CI pipeline (`script/cibuild`) must pass before merging:

1. `bundle exec jekyll build --baseurl "."` — site must build without errors.
2. `bundle exec htmlproofer _site/index.html --empty-alt-ignore` — HTML must be valid (empty alt attributes are allowed).

There are no unit or integration tests beyond HTML validation.

## Git Workflow

- Default branch: `master`
- Feature work uses pull requests branched from `master`.
- Commit messages should be concise and descriptive (imperative mood preferred).
- Dependabot is configured for automated dependency updates.
- Commits are signed with SSH keys.

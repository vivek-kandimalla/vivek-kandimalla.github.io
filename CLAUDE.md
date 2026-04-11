# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This is the source for Vivek Kandimalla's personal academic website, deployed to GitHub Pages at `https://vivek-kandimalla.github.io`. It is a [Hugo](https://gohugo.io/) static site built from a minimalist academic template originally by Pascal Michaillat (see [README.md](README.md)). The template is a customization of the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme that strips out most blog-oriented features and reshapes them around academic content (papers, books, courses, data, CV).

## Common commands

Local development (requires Hugo extended, v0.147.2 is the version used in CI):

```bash
hugo server          # live-reload dev server at http://localhost:1313
hugo --minify        # one-shot production build into ./public
```

There is no test suite, no linter, and no package.json — pushing to `main` is the only "build" that matters. The GitHub Actions workflow at [.github/workflows/hugo.yml](.github/workflows/hugo.yml) installs Hugo + Dart Sass, runs `hugo --minify`, and deploys `./public` to GitHub Pages. Any local Hugo version mismatch can cause deploy-time differences from local preview.

## Architecture

Hugo's usual conventions apply, but a few things are specific to this template and worth knowing before editing:

- **Theme is vendored, not a submodule.** [themes/PaperMod/](themes/PaperMod/) is a checked-in copy, and `config.yml` sets `theme: PaperMod`. Layout and CSS overrides at the project root (in [layouts/](layouts/) and [assets/css/](assets/css/)) take precedence over the theme's own files — Hugo's lookup order means the project copy wins. When changing presentation, prefer editing the project-level files; the vendored theme should normally be left alone.

- **Site config lives in [config.yml](config.yml).** Most visible changes (bio/subtitle, profile image, social icons, CV link, enabled sections) are configuration, not template edits. Note that `MainSections: ["books","courses","papers","data"]` controls which content directories Hugo treats as primary listings, and `menu.main` is currently empty — all navigation is commented out so the site renders as a single profile page. Adding a section means both creating content under [content/](content/) and uncommenting the corresponding `menu.main` / `profileMode.buttons` entries.

- **Content is organized as page bundles.** Each paper/book/course lives in its own directory under [content/papers/](content/), [content/books/](content/), etc., with an `index.md` plus its PDFs and images co-located as page resources (see [content/papers/paper1/](content/papers/paper1/) for the shape). The `index.md` front matter drives the list rendering, and body markdown uses a convention of `---`-separated sections (Download / Abstract / Citation / Related material) which the list and single templates assume — follow the existing files' structure when adding new entries.

- **Static assets (CV, profile photo, favicons) live in [static/](static/)** and are referenced by filename only (e.g. `cv_vivek.pdf`, `DSC09986_3.JPG` in `config.yml`). Hugo copies everything in `static/` to the site root at build time, so a URL like `cv_vivek.pdf` in config resolves to `/cv_vivek.pdf` on the live site.

- **CSS is split by concern** in [assets/css/common/](assets/css/common/) (per-component: header, footer, profile-mode, post-single, etc.) plus [assets/css/core/theme-vars.css](assets/css/core/theme-vars.css) for design tokens. Dart Sass is required by the CI build, so `.scss` imports are valid.

- **Math rendering is opt-in** via `params.math: true` in config, with the partial at [layouts/partials/math.html](layouts/partials/math.html) handling KaTeX. Markdown `unsafe: true` is enabled, so raw HTML in content files is allowed.

## Editing conventions for content

- To add a paper, copy an existing directory under `content/papers/`, rename it, replace the PDFs/image, and edit `index.md`'s front matter and body sections. The `cover.image` field is relative to the page bundle.
- To change the bio, edit `params.profileMode.subtitle` in [config.yml](config.yml).
- To swap the CV, replace `static/cv_vivek.pdf` (the filename referenced from `params.socialIcons`) — the older `static/cv.pdf` is a leftover from the template and is not currently linked.

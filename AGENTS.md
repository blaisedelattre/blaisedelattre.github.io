# Agent Guidelines for al-folio

A simple, clean, and responsive Jekyll theme for academics.

## Quick Links by Role

- **Are you a coding agent?** → Read [`.github/copilot-instructions.md`](.github/copilot-instructions.md) first (tech stack, build, CI/CD, common pitfalls & solutions)
- **Customizing the site?** → See [`.github/agents/customize.agent.md`](.github/agents/customize.agent.md)
- **Writing documentation?** → See [`.github/agents/docs.agent.md`](.github/agents/docs.agent.md)
- **Need setup/deployment help?** → [INSTALL.md](INSTALL.md)
- **Troubleshooting & FAQ?** → [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- **Customization & theming?** → [CUSTOMIZE.md](CUSTOMIZE.md)
- **Quick 5-min start?** → [QUICKSTART.md](QUICKSTART.md)

## Essential Commands

### Local Development (Docker)

The recommended approach is using Docker.

```bash
# Initial setup & start dev server
docker compose pull && docker compose up
# Site runs at http://localhost:8080

# Rebuild after changing dependencies or Dockerfile
docker compose up --build

# Stop containers and free port 8080
docker compose down
```

### Pre-Commit Checklist

Before every commit, you **must** run these steps:

1.  **Format Code:**
    ```bash
    # (First time only)
    npm install --save-dev prettier @shopify/prettier-plugin-liquid
    # Format all files
    npx prettier . --write
    ```
2.  **Build Locally & Verify:**

    ```bash
    # Rebuild the site
    docker compose up --build

    # Verify by visiting http://localhost:8080.
    # Check navigation, pages, images, and dark mode.
    ```

## Critical Configuration

When modifying `_config.yml`, these **must be updated together**:

- **Personal site:** `url: https://username.github.io` + `baseurl:` (empty)
- **Project site:** `url: https://username.github.io` + `baseurl: /repo-name/`
- **YAML errors:** Quote strings with special characters: `title: "My: Cool Site"`

## Development Workflow

- **Git & Commits:** For commit message format and Git practices, see [.github/GIT_WORKFLOW.md](.github/GIT_WORKFLOW.md).
- **Code-Specific Instructions:** Consult the relevant instruction file for your code type.

| File Type                                     | Instruction File                                                                                |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Markdown content (`_posts/`, `_pages/`, etc.) | [markdown-content.instructions.md](.github/instructions/markdown-content.instructions.md)       |
| YAML config (`_config.yml`, `_data/`)         | [yaml-configuration.instructions.md](.github/instructions/yaml-configuration.instructions.md)   |
| BibTeX (`_bibliography/`)                     | [bibtex-bibliography.instructions.md](.github/instructions/bibtex-bibliography.instructions.md) |
| Liquid templates (`_includes/`, `_layouts/`)  | [liquid-templates.instructions.md](.github/instructions/liquid-templates.instructions.md)       |
| JavaScript (`_scripts/`)                      | [javascript-scripts.instructions.md](.github/instructions/javascript-scripts.instructions.md)   |

## Common Issues

For troubleshooting, see:

- [Common Pitfalls & Workarounds](.github/copilot-instructions.md#common-pitfalls--workarounds) in copilot-instructions.md
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for detailed solutions
- [GitHub Issues](https://github.com/alshedivat/al-folio/issues) to search for your specific problem.

---

## My Site — Editing Workflow (Owner)

> **Scope:** this section is for editing my personal site (`blaisedelattre.github.io`). It is **not** for contributing back to the upstream `al-folio` template — for that, follow the sections above (prettier, full pre-commit checklist, PR to `alshedivat/al-folio`).

### 0. Prerequisites (one-time)

- Docker Desktop installed (`brew install --cask docker`) and **running** (whale icon in menu bar).
- Repo cloned, on `main`.

### 1. Start the local dev loop

```bash
docker compose pull        # first time only (or after upstream image bump)
docker compose up          # serves at http://localhost:8080 with auto-reload
```

Edit any file under `_pages/`, `_news/`, `_bibliography/`, `assets/`, etc. — the site rebuilds and the browser tab can be refreshed. Watch the container logs for Liquid/YAML errors.

### 2. Where to edit common things

| What I want to change             | File / folder                                       |
| --------------------------------- | --------------------------------------------------- |
| Homepage bio, profile pic, links  | [_pages/about.md](_pages/about.md)                  |
| News items (homepage feed)        | [_news/](_news/) — one `announcement_N.md` per item |
| Publications list                 | [_bibliography/papers.bib](_bibliography/papers.bib) |
| Profile image                     | [assets/img/](assets/img/) (referenced by filename in `about.md` frontmatter) |
| CV / PDFs                         | [assets/pdf/](assets/pdf/)                          |
| Social icons, site title, URL    | [_config.yml](_config.yml)                          |

### 3. Invariants — never break these

- `_config.yml` **must** keep:
  ```yaml
  url: https://blaisedelattre.github.io
  baseurl:    # empty — this is a user site, not a project site
  ```
  Reverting these to the upstream `alshedivat` / `/al-folio` defaults breaks every asset and link on the deployed site.
- Do **not** edit `_layouts/`, `_includes/`, `_sass/`, or `assets/js/` unless I'm consciously forking the theme. Stick to content folders.

### 4. Skip the contributor pre-commit ceremony

For content-only edits (bio, news, papers, images), I do **not** need to:

- run `npx prettier . --write` over the whole repo,
- bump theme versions,
- update upstream docs.

Just verify visually at `http://localhost:8080`, commit, push.

### 5. Stop the local server

```bash
docker compose down        # frees port 8080
```

### 6. Deploy

```bash
git add -p && git commit -m "..." && git push origin main
```

The [.github/workflows/deploy.yml](.github/workflows/deploy.yml) workflow builds Jekyll and pushes the result to the `gh-pages` branch via `JamesIves/github-pages-deploy-action`. GitHub Pages must be configured as **Source: Deploy from a branch → Branch: `gh-pages` / root**. Wait 1–3 min, then check https://blaisedelattre.github.io.

### Template content I've hidden (kept in source for later)

The al-folio template ships with demo pages and demo collections (Einstein bio, sample blog posts, sample projects, bookshelf, lab "people" page, etc.). I've **hidden** everything I have no real content for — files are still in the repo, just not rendered into the built site.

How hiding works here:

| Mechanism                                | Effect                                                                                    |
| ---------------------------------------- | ----------------------------------------------------------------------------------------- |
| `published: false` in page frontmatter   | Jekyll skips the page entirely. `/the-permalink/` returns 404.                            |
| `nav: false` in page frontmatter         | Page (if published) is rendered but removed from the top navbar.                          |
| `exclude:` in `_config.yml`              | Files / directories are not read by Jekyll at all. Best for whole demo-content folders.   |
| File renamed with leading `_`            | Jekyll ignores files starting with `_` outside declared collections (e.g. archived bibs). |

Currently hidden:

| What                                | Where                                                  | How to re-enable                                                          |
| ----------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------- |
| Projects page + demo project cards  | [_pages/projects.md](_pages/projects.md) + `_projects/` excluded | Remove `published: false` + `nav: false` from frontmatter; un-exclude `_projects/` in `_config.yml`; add real projects under `_projects/`. |
| Repositories page                   | [_pages/repositories.md](_pages/repositories.md)       | Remove `published: false` + `nav: false`; populate `github_repos:` in [_data/repositories.yml](_data/repositories.yml). |
| People / lab profiles               | [_pages/profiles.md](_pages/profiles.md)               | Remove `published: false` + `nav: false`; replace the placeholder `profiles:` block with real entries; replace `about_einstein.md` references. |
| Blog index + demo posts             | [_pages/blog.md](_pages/blog.md) + `_posts/` excluded  | Remove `published: false` + `nav: false`; un-exclude `_posts/` in `_config.yml`; add real posts under `_posts/`. |
| Bookshelf + demo book               | [_pages/books.md](_pages/books.md) + `_books/` excluded | Remove `published: false`; un-exclude `_books/`; replace `the_godfather.md` with real entries. |
| Submenus dropdown (bookshelf / blog) | [_pages/dropdown.md](_pages/dropdown.md)               | Remove `published: false` + `nav: false` after re-enabling at least one child page.       |
| Einstein bibliography               | `_bibliography/_archive_einstein.bib`                  | Rename back to a `.bib` file Jekyll-Scholar picks up, or merge entries into `papers.bib`. |
| Einstein CV structure               | `_data/_archive_cv_einstein.yml`                       | Copy the relevant sections back into `_data/cv.yml`.                       |
| LAMSADE source HTML/assets          | `public_html_lamsade/` (excluded)                      | Reference material; not meant to be served — keep excluded.                |

Pages I intentionally **keep visible** because I have real content for them: [about](_pages/about.md), [publications](_pages/publications.md) (driven by [papers.bib](_bibliography/papers.bib)), [teaching](_pages/teaching.md), [CV](_pages/cv.md), [news](_pages/news.md).

### Where this could live instead

This subsection is fine in `AGENTS.md` while it stays short. If it grows past ~50 lines, extract it to one of:

- **`OWNER_NOTES.md`** at the repo root — simplest, discoverable, gitignored from upstream merges if I ever sync the theme.
- **`.github/agents/owner.agent.md`** — matches the existing `customize.agent.md` / `docs.agent.md` pattern, keeps agent-only docs out of the project root.
- **`.claude/CLAUDE.local.md`** (gitignored) — if I want it to be agent-context but not committed.

For now, keeping it here is fine: one file, one link from `CLAUDE.md`.

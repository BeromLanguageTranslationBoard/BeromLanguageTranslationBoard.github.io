# Berom Language & Translation Board — Site Guide

## Quick Start

```bash
# Render locally
uv run quarto render

# Preview with live reload
uv run quarto preview
```

Site is deployed via GitHub Actions on push to `main`. The CI publishes to the `gh-pages` branch which GitHub Pages serves.

---

## Quarto Version Trap

**Your local Quarto and CI Quarto must match.** If they don't, YAML validation errors or missing features will silently break the build.

| Where | Version | Set in |
|---|---|---|
| Local | `1.9.37` | `quarto check` |
| CI | `1.9.37` | `.github/workflows/publish.yml` |

If you upgrade quarto locally (`quarto upgrade`), update the CI version too.

### Names that changed between Quarto versions

| Feature | 1.5 name | 1.9 name | Used in |
|---|---|---|---|
| Callout style | `callout-style` | `callout-appearance` | `_quarto.yml` |

Always use the 1.5-compatible name: `callout-style: simple`.

---

## Design System

The site shares the same design system as **SisengAI** — a dual-theme setup:

| | Light (LivingStory) | Dark (CreatorMagic) |
|---|---|---|
| **SCSS file** | `html/styles.scss` | `html/styles-dark.scss` |
| **Background** | Cream `#FDFBF7` | Zinc `#09090b` |
| **Cards** | White + mahogany shadow | Zinc-900 `#18181b` + zinc border |
| **Primary** | Amber `#D4720A` | Amber `#f59e0b` |
| **Headings** | Mahogany `#321208` | `#fafafa` |
| **Body text** | Stone `#57534E` | Zinc-200 `#e4e4e7` |
| **Footer** | Dark mahogany `#321208` | Pure zinc-950 `#09090b` |

**Both SCSS files must be kept in sync.** When adding a new component or class, add it to BOTH files — each with its own color variables. The light file uses `$cream-*`, `$mahogany-*`, `$stone-*`. The dark file uses `$zinc-*`, `$amber-*`.

**Do NOT use `[data-bs-theme="dark"]` blocks.** The two-file approach (separate compiled CSS) is what makes the toggle work.

---

## File Map

| File | Role |
|---|---|
| `_quarto.yml` | Site config: theme, navbar, footer, repo-actions, fonts |
| `html/styles.scss` | Light theme: LivingStory design system |
| `html/styles-dark.scss` | Dark theme: CreatorMagic zinc palette. MUST mirror styles.scss |
| `index.qmd` | Homepage: hero + stats + about card + contribution cards + latest posts + latest news |
| `blog/index.qmd` | Blog listing page (grid, 2 columns) |
| `blog/_metadata.yml` | Defaults for all blog posts: freeze, callout-style, repo-actions |
| `News/index.qmd` | News listing page (grid, 2 columns) |
| `about.qmd` | Bilingual (Berom + English) about page |
| `404.qmd` | Custom 404 with redirect |
| `.github/workflows/publish.yml` | CI: render → publish to gh-pages |

---

## How To…

### Add a new blog post

1. Create a folder under `blog/` with the date prefix:
   ```
   blog/2026-June-01-My-Post/
   ```

2. Inside, create `index.qmd`:
   ```markdown
   ---
   title: "Your Post Title"
   author: "Author Name"
   date: "01 June 2026"
   image: optional-banner.jpg
   categories: [Berom, English, Culture]
   format: html
   ---

   Your content here.
   ```

3. **Add the new directory to `index.qmd`'s listing YAML** (see "Updating the homepage" below).

4. Render: `uv run quarto render`

### Add a news item

Same process as blog posts, but create the folder under `News/`:
```
News/2026-June-01-Announcement/
```

And add it to the `latest-news` listing in `index.qmd`.

### Updating the homepage listing

**CRITICAL — the homepage does NOT auto-discover new posts.** When you add a blog post or news item, you must add its folder path to the listing YAML in `index.qmd`:

```yaml
listing:
  - id: latest-posts
    contents:
      - blog/2026-January-15_New_Post    # ← add new paths here
      - blog/2025-October-27_Ya_fey_na_kat
      - blog/2025-September-15_proverb_on_money
      # ... existing entries ...
    sort: "date desc"
    type: default
    max-items: 3
  - id: latest-news
    contents:
      - News/2026-February-01_Announcement  # ← add new paths here
      - News/2025-November-24_Wusal_Berom_2025
      # ... existing entries ...
    sort: "date desc"
    type: default
    max-items: 2
```

The listing takes the 3 most recent blog posts and 2 most recent news items automatically. Just add the new folder path — the `.qmd` inside is auto-discovered.

**To list all existing directories** (for reference):
```bash
ls -d blog/20* News/*/
```

### Use section labels and cards

```markdown
<span class="section-label">Mission</span>

### Section Title

<p class="section-subtitle">Descriptive subtitle text.</p>

<div class="exp-card">
  <p class="exp-title">Card Title</p>
  <p class="exp-institution">Subtitle</p>
  <p class="exp-description">Description text.</p>
  <span class="exp-badge">Label</span>
</div>
```

### Use alternating section backgrounds

```markdown
::: {.content-section}
<!-- cream background -->
:::

::: {.content-section-alt}
<!-- white background -->
:::
```

### Test locally before pushing

```bash
uv run quarto render && uv run quarto preview
```

### Debug CI failures

1. Check Quarto version: `quarto check` vs `.github/workflows/publish.yml`
2. Check no YAML keys use 1.9-only names
3. GitHub Actions: `https://github.com/BeromLanguageTranslationBoard/BeromLanguageTranslationBoard.github.io/actions`

### Regenerate after deleting _freeze/

```bash
rm -rf _freeze/ _site/
uv run quarto render
```

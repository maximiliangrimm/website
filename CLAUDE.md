# CLAUDE.md — Personal website (maximiliangrimm.com)

## What this project is

A static personal website for an academic economist. Hosted on **GitHub Pages**, served at **maximiliangrimm.com**. Three hand-written files do everything: `index.html`, `research.html`, `style.css`. No framework, no build step, no JavaScript, no package manager.

The owner is a researcher, not a web developer. He edits files in a text editor, previews by double-clicking `index.html`, and deploys by pushing to GitHub. Any change you suggest must fit this workflow.

## Hard constraints — do not violate

1. **No build tooling.** No npm, no Node, no bundlers, no Vite, no Tailwind compile step, no Sass compiler. The browser must render the files as-is. If you want CSS variables, write them by hand in `style.css`.
2. **No JavaScript** unless explicitly asked for. Currently the site has zero JS and should stay that way. `<details>`/`<summary>` for the expandable abstracts is HTML, not JS — keep it that way.
3. **No frameworks.** Do not propose migrating to Quarto, Hugo, Jekyll, Astro, 11ty, Next.js, or anything else. This was a deliberate choice after considering them.
4. **No new dependencies.** Only external resource is Google Fonts (EB Garamond). Do not add icon libraries, CSS frameworks, analytics scripts, or fonts without explicit permission.
5. **No dark mode, no animations beyond a 120ms hover transition, no gradients, no shadows.** The aesthetic is deliberately restrained.
6. **The Fed disclaimer in `<footer>` is non-negotiable.** It must appear on every page. Owner is a Federal Reserve Board employee; the text is a compliance requirement.

## Aesthetic — what the site is going for

Refined academic minimalism. Reference points: Benjamin Moll, Atif Mian, Jón Steinsson, Emi Nakamura — top economists who use plain HTML and let the content carry the page. The visual signal is *restraint*. An overdesigned site for a Fed economist targeting Top-5 / Top-3 finance journals reads as unserious.

**Design tokens (defined in `style.css` at the `:root`):**

| Token | Value | Use |
|---|---|---|
| `--paper` | `#fbfaf6` | Background (warm off-white, like aged paper) |
| `--ink` | `#1c1917` | Primary text |
| `--muted` | `#6b6259` | Secondary text, metadata, author lists |
| `--rule` | `#e7e2d6` | Dividers, borders |
| `--accent` | `#8c2718` | Links (deep oxblood) |
| `--accent-hover` | `#5a1810` | Link hover |
| `--measure` | `38rem` | Single column max-width |

**Typography:** EB Garamond throughout, loaded from Google Fonts. Weight 400 for body, 500–600 for emphasis. Base font size is 18px (set on `html`). Line-height 1.6. Italic for venue names and the disclaimer.

**Layout:** Single centered column, max-width `var(--measure)`. Generous vertical whitespace. No sidebar, no multi-column grids except the landing-page intro (170px photo + bio column, collapses to single column under 600px viewport).

If asked to "modernize" or "make it pop" — push back. The owner picked this aesthetic on purpose.

## File layout

```
site/
├── index.html             # landing page (intro paragraph + photo)
├── research.html          # papers organized into Publications / Working Papers / Work in Progress
├── style.css              # all styling, well-commented section headers
├── CNAME                  # single line: maximiliangrimm.com — required by GitHub Pages
├── .nojekyll              # empty file — disables Jekyll so CLAUDE.md and other .md files aren't served as public pages
├── .gitignore             # ignores LaTeX build artifacts and editor folders
└── assets/
    ├── profile.jpg        # portrait
    ├── CV.tex             # CV source — edit this, then recompile to refresh CV.pdf
    ├── CV.pdf             # compiled CV (the file the site actually links to)
    ├── res.cls            # LaTeX class file required by CV.tex (do not delete)
    ├── favicon.png        # 512×512 PNG
    └── googlescholar.png  # nav icon (rendered grayscale via a CSS filter)
```

The two HTML files share an identical `<header>` and `<footer>` block. If the nav or footer changes, both files must be updated together. The current nav, in order, is: `Home · Research · CV · ✉ (inline SVG envelope) · Scholar (PNG icon)`. (Future option: a static-site generator would deduplicate the shared header/footer, but the owner has explicitly chosen not to use one.)

## Common edit recipes

### Adding a new paper to `research.html`

Copy an existing `<article class="paper">` block and paste it into the correct section. The structure is fixed:

```html
<article class="paper">
  <h3>Title of the paper</h3>
  <p class="authors">
    with
    <a href="https://coauthor-a.example/">Coauthor A</a> and
    <a href="https://coauthor-b.example/">Coauthor B</a>
  </p>
  <p class="venue">
    Status / venue.
    &nbsp;·&nbsp; <a href="#">PDF</a>
    &nbsp;·&nbsp; <a href="#">SSRN</a>
    &nbsp;·&nbsp; <a href="#">Slides</a>
  </p>
  <details>
    <summary>Abstract</summary>
    <p>Abstract text here.</p>
  </details>
</article>
```

Conventions used throughout the site:

- **Coauthor names are hyperlinks** to their personal websites, listed alphabetically by last name. If a coauthor has no website, leave their name as plain text.
- **Sole-authored papers**: omit the `<p class="authors">` element entirely. Do not write "Sole-authored".
- **Do not repeat the section status in the venue line.** No "Working paper." inside the Working Papers section; no "Work in progress." inside the Work in Progress section — the heading already conveys it.
- **Published papers**: the venue line uses italics: `<em>Journal Name</em>, Vol. X(Y), 20XX, pp. XX–XX.` Use an en-dash (`–`), not a hyphen, in page ranges.

The three section headings (`<h2 class="section">`) are: **Publications**, **Working Papers**, **Work in Progress**. Keep them in that order — most-credentialed first. Within each section, also list most-credentialed first.

### Updating the bio on `index.html`

The `<div class="bio">` contains three short paragraphs. Keep paragraphs short and high-signal. The owner is a Fed economist with an R&R at the JF and a Top-5 submission; the bio should not undersell that, but should also not list every paper (that's what `research.html` is for).

### Updating CV or photo

**CV**: edit `assets/CV.tex`, then recompile:

```bash
cd assets && pdflatex CV.tex
```

This regenerates `assets/CV.pdf` — the file the site actually serves. The class file `assets/res.cls` is required (referenced by `\documentclass{res}` in `CV.tex`); do not delete it. LaTeX build artifacts (`.aux`, `.log`, `.fls`, `.fdb_latexmk`, `.synctex.gz`) are gitignored. Always recompile before pushing, or the website's CV link will serve a stale PDF.

**Photo**: replace `assets/profile.jpg`. Filenames must stay as `CV.pdf` and `profile.jpg` — the HTML hardcodes these paths.

### When the title, description, or portrait changes

The `<head>` of each HTML file contains a block of Open Graph and Twitter Card meta tags used by Bluesky, Twitter, LinkedIn, Slack, and iMessage to render link previews. If the page title, the meta description, or the portrait changes, update the corresponding `og:title`, `og:description`, `og:image`, and `og:url` lines on each affected page — otherwise link previews will go stale.

## Local preview

Double-click `index.html`. Browser opens. That's the whole workflow. No server needed.

If asked about a local dev server (rare), the simplest option is:
```bash
cd site && python3 -m http.server 8000
```
Then visit `http://localhost:8000`. Use only if testing something where `file://` paths misbehave.

## Deployment

Push to GitHub → GitHub Pages serves the updated site within ~60 seconds. The `CNAME` file maps the GitHub Pages output to `maximiliangrimm.com`. DNS is configured at the domain registrar (currently WordPress.com) with four A-records pointing at `185.199.108–111.153` and a `www` CNAME to `<username>.github.io`.

If the owner asks why a change isn't showing up: check (1) DNS propagation if domain-related, (2) browser cache (hard refresh), (3) GitHub Actions tab for build errors (rare for static HTML).

## Owner context

The owner:

- PhD in Economics (Bonn, 2025), now at the Federal Reserve Board's Financial Stability Division.
- Targets Top-5 Economics (AER, QJE, JPE, Econometrica, RES) and Top-3 Finance (JF, JFE, RFS) journals. One R&R at the JF, one Top-5 submission, several working papers.
- Technical stack outside this project: Stata for econometrics, Python for everything else, LaTeX for writing, Git for version control, Linux Ubuntu.
- Newbie to web development. Concrete step-by-step instructions when introducing anything new (e.g., a CLI command).

## What's deliberately absent

When the owner asks "should I add X?" the answer is usually no. The following have been considered and rejected:

| Thing | Why not |
|---|---|
| Blog / news / talks page | Adds maintenance burden; empty sections signal a junior researcher. Add only when there's real content. |
| Teaching page | The owner is a research economist at the Fed, not in a teaching role. |
| Twitter / X / LinkedIn icons in nav | No. The Google Scholar icon was added at the owner's request and is the only profile-link icon in the nav. SSRN is the remaining academically-relevant option if ever wanted; everything else stays out. |
| Dark mode toggle | Off-white paper aesthetic is intentional. A dark mode would require redesigning the palette and adds complexity. |
| Analytics | If asked, suggest Plausible or GoatCounter (privacy-respecting). Avoid Google Analytics on a personal academic site. |
| Newsletter / RSS / comments | No. |
| A homepage hero image, background pattern, or banner | No. The portrait is the only image on the homepage. |
| JavaScript-driven anything (search bar, theme switcher, scroll animations) | No. See "Hard constraints." |

## Tone for the owner

Direct, no preamble. Skip "great question!" and similar. When proposing a change, show the diff or the exact replacement HTML/CSS. When in doubt about whether to make a change, default to fewer modifications, not more.

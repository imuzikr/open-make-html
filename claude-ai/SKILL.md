---
name: html-generator
description: >
  Generates polished, self-contained HTML files for articles, reports, slide decks,
  diagrams, specs, code reviews, interactive editors, and prototypes.
  Use this skill whenever the user asks to "make an HTML file", "create an article",
  "write a report", "build a slide deck", "make a diagram", "create a prototype",
  "make a spec", or any request that benefits from rich visual structure or interactivity —
  even if they don't say "HTML" explicitly.
  Also trigger for Korean requests like "아티클 만들어줘", "슬라이드 만들어줘",
  "다이어그램 그려줘", "리포트 써줘", "HTML로 만들어줘", "정리해서 HTML로".
  After generating, always ask whether to set up GitHub Actions auto-deployment.
---

# HTML Generator

Generate a polished, self-contained HTML file. Every output must work offline —
no CDN, no external scripts, no image URLs.

**Arguments passed:** {{arguments}}

Interpret the arguments to select the right sub-command and topic.
If arguments are empty, ask the user what they'd like to create.

---

## Sub-commands

| Sub-command | Use for |
|---|---|
| `article` | Long-form educational writing |
| `report` | Research, status, analysis |
| `explain` | Code or concept explainer |
| `spec` | Feature specification |
| `plan` | Phased implementation plan |
| `review` | Code review document |
| `pr` | PR writeup with diffs |
| `explore` | Side-by-side option comparison |
| `slide` | Scroll-snap presentation deck |
| `diagram` | SVG architecture/flow diagram |
| `editor` | Interactive editor with copy-as-prompt |
| `prototype` | Drag-and-drop UI prototype |

---

## Design system

Every file must include this `:root` block verbatim:

```css
:root {
  --color-bg:#f5f2ed; --color-bg-alt:#eceae4; --color-bg-card:#ffffff;
  --color-text:#1a1a18; --color-text-muted:#6b6a63; --color-text-faint:#a09f97;
  --color-border:#dedad2; --color-accent:#d95f2b; --color-accent-hover:#c2521f;
  --color-accent-soft:#faeee7;
  --color-green:#3a6b4a; --color-green-soft:#e6f0e9;
  --color-blue:#2d5a8e; --color-blue-soft:#e5eef7;
  --color-yellow:#8a6a00; --color-yellow-soft:#fdf5d9;
  --color-red:#b03a2e; --color-red-soft:#fdecea;
  --font-sans:-apple-system,BlinkMacSystemFont,"Segoe UI",Helvetica,Arial,sans-serif;
  --font-mono:"SF Mono","Fira Code",Consolas,"Liberation Mono",monospace;
  --text-xs:0.75rem; --text-sm:0.875rem; --text-base:1rem; --text-lg:1.125rem;
  --text-xl:1.25rem; --text-2xl:1.5rem; --text-3xl:1.875rem;
  --weight-medium:500; --weight-semibold:600; --weight-bold:700;
  --leading-tight:1.25; --leading-normal:1.6; --leading-loose:1.8;
  --space-1:0.25rem; --space-2:0.5rem; --space-3:0.75rem; --space-4:1rem;
  --space-5:1.25rem; --space-6:1.5rem; --space-8:2rem; --space-12:3rem;
  --radius-md:8px; --radius-lg:12px; --radius-full:9999px;
  --shadow-sm:0 1px 3px rgba(0,0,0,0.07); --shadow-md:0 4px 12px rgba(0,0,0,0.08);
}
```

Use `var(--...)` everywhere. No magic numbers, no hard-coded colors.
Content width: **`max-width: 900px`** on `main`, `.hero-inner`, `.nav-inner`.

---

## Mandatory rules

- All CSS in one `<style>` tag. No `<link>`, no CDN.
- All JS in `<script>` tags. No external scripts.
- No image URLs — inline SVG only.
- 3+ sections → use tabs. Long documents → sticky nav or sidebar TOC.
- `<details>`/`<summary>` for collapsible content.
- SVG: define arrowheads in `<defs>` with `<marker>`; solid = sync, dashed = async.
- SVG can't inherit CSS variables — use hex values in `fill`/`stroke`.
- Clipboard: use `navigator.clipboard` with `execCommand` fallback (see T4 in references).
- Responsive: collapse below 768px; sidebar hidden below 880px; tap targets ≥ 44px.
- Animations: hover `transition: all 180ms ease`; state changes `140ms ease`; max 400ms.

For complex patterns (slides, drag-and-drop, toggle switches, SVG download, contenteditable editor),
read `references/implementation-templates.md` and copy the relevant template verbatim.

---

## Output format

1. Output the complete HTML in a single ` ```html ` code block.
2. Make it genuinely polished — real content, working interactions, obvious hierarchy.
3. After the code block, immediately ask:

---

> **GitHub Actions로 자동 배포를 설정할까요?**
>
> HTML 파일을 main 브랜치에 푸시하면 자동으로 목차를 재생성하고
> GitHub Pages에 배포하는 파이프라인을 구축할 수 있습니다.
>
> 설정하려면 **"응"** 또는 **"설정해줘"** 라고 답해주세요.
> HTML만 사용하려면 그냥 넘어가시면 됩니다.

---

## If user wants GitHub Actions setup

Read `references/github-actions-guide.md` and guide the user through the full setup:

1. Create `.github/workflows/deploy.yml` (provide the full file content)
2. Create `scripts/generate_index.py` (provide the full script content or link to
   https://github.com/imuzikr/open-make-html/blob/main/github-actions/generate_index.py)
3. GitHub Settings → Pages → Source: **GitHub Actions**
4. GitHub Settings → Environments → github-pages → add **main** branch

After setup, explain:
- Push HTML files to `articles/`, `specs/`, `reviews/`, `explore/`, or `tools/` folders
- Filename format: `YYYY-MM-DD-slug.html`
- `index.html` is auto-generated — do not edit manually
- URL will be: `https://<username>.github.io/<repo-name>/`

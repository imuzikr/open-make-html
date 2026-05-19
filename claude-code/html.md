# /html — HTML Generator

Generate a polished, self-contained HTML file. Use for any output that benefits from
structure, navigation, interactivity, or visual hierarchy.

---

## How to invoke

```
/html <sub-command> <topic>
```

Examples:
```
/html article 온톨로지란 무엇인가
/html slide 분산 시스템 입문
/html report Q2 인프라 인시던트
/html explore 메시지 큐 선택지
/html diagram 마이크로서비스 아키텍처
/html editor 고객지원 챗봇 프롬프트 작성기
/html prototype 태스크 Kanban 보드
```

If no sub-command is given, choose the best fit or default to `report`.

---

## Sub-commands & save paths

| Sub-command | Use for | Folder |
|---|---|---|
| `article` | Long-form educational writing | `articles/` |
| `report` | Research, status, analysis | `articles/` |
| `explain` | Code or concept explainer | `articles/` |
| `spec` | Feature specification | `specs/` |
| `plan` | Phased implementation plan | `specs/` |
| `review` | Code review document | `reviews/` |
| `pr` | PR writeup with diffs | `reviews/` |
| `explore` | Side-by-side option comparison | `explore/` |
| `slide` | Scroll-snap presentation deck | `tools/` |
| `diagram` | SVG architecture/flow diagram | `articles/` |
| `editor` | Interactive editor with copy-as-prompt | `tools/` |
| `prototype` | Drag-and-drop UI prototype | `tools/` |

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

Use `var(--...)` everywhere. **Content width: `max-width: 900px`** on `main`, `.hero-inner`, `.nav-inner`.

---

## Mandatory rules

### Self-contained
- All CSS in one `<style>` tag. No `<link>`, no CDN, no `@import`.
- All JS in `<script>` tags. No external scripts.
- No image URLs — inline SVG only.
- Must render offline.

### Layout & navigation
- 3+ sections → tabs (`.tab-btn` / `.tab-panel`, inline JS, first tab active).
- Long docs → sticky nav or sidebar TOC.
- Collapsible content → native `<details>`/`<summary>`.
- Responsive: collapse below 768px; sidebar hidden below 880px; tap targets ≥ 44px.

### SVG diagrams
- All diagrams inline `<svg>`. Never ASCII art.
- Define arrowheads in `<defs>` with `<marker>`; use `marker-end` on paths.
- Solid lines = sync. Dashed (`stroke-dasharray`) = async.
- SVG can't inherit CSS variables — use hex values directly.

### Code blocks
- `.code-block-header` + `<pre><code>` + language label + Copy button.
- Tokens: `.tok-keyword`, `.tok-string`, `.tok-comment`, `.tok-number`, `.tok-function`.

### Clipboard
Always use `navigator.clipboard` with `execCommand` fallback:
```js
function copyToClipboard(text, btn) {
  const label = btn.textContent;
  const flash = ok => { btn.textContent = ok?'Copied ✓':'Failed'; btn.disabled=true;
    setTimeout(()=>{ btn.textContent=label; btn.disabled=false; }, 1500); };
  if (navigator.clipboard && window.isSecureContext)
    navigator.clipboard.writeText(text).then(()=>flash(true)).catch(fallback);
  else fallback();
  function fallback() {
    const ta=document.createElement('textarea'); ta.value=text;
    ta.style.cssText='position:fixed;opacity:0;'; document.body.appendChild(ta);
    ta.focus(); ta.select();
    try { flash(document.execCommand('copy')); } catch { flash(false); }
    document.body.removeChild(ta);
  }
}
```

### Animations
- Hover: `transition: all 180ms ease`.
- State changes: `transition: background 140ms ease`.
- Max duration: 400ms.

### Implementation templates
For slides, drag-and-drop, toggle switches, SVG download, contenteditable editors —
copy verbatim from the templates below. Do not rewrite from memory.

#### T1 · CSS scroll-snap slide deck
```html
<div id="deck" style="height:100vh;overflow-y:scroll;scroll-snap-type:y mandatory;">
  <section class="slide" style="height:100vh;scroll-snap-align:start;display:flex;align-items:center;justify-content:center;">
    <!-- content -->
  </section>
</div>
<div id="progress" style="position:fixed;top:0;left:0;height:3px;background:#d95f2b;width:0%;transition:width 200ms ease;z-index:100;"></div>
<div id="counter" style="position:fixed;bottom:1.5rem;right:1.5rem;font-size:0.875rem;color:#6b6a63;"></div>
<button id="btn-prev" onclick="navSlide(-1)" style="position:fixed;bottom:1.5rem;left:50%;transform:translateX(-60px);width:44px;height:44px;border-radius:9999px;border:1px solid #dedad2;background:#fff;cursor:pointer;">←</button>
<button id="btn-next" onclick="navSlide(1)"  style="position:fixed;bottom:1.5rem;left:50%;transform:translateX(12px);width:44px;height:44px;border-radius:9999px;border:1px solid #dedad2;background:#fff;cursor:pointer;">→</button>
<script>
const slides=Array.from(document.querySelectorAll('.slide'));let current=0;
const observer=new IntersectionObserver(entries=>{entries.forEach(e=>{
  if(e.isIntersecting&&e.intersectionRatio>=0.6){current=slides.indexOf(e.target);updateUI();}
});},{threshold:0.6});
slides.forEach(s=>observer.observe(s));
function updateUI(){
  const pct=slides.length>1?(current/(slides.length-1))*100:100;
  document.getElementById('progress').style.width=pct+'%';
  document.getElementById('counter').textContent=(current+1)+' / '+slides.length;
  document.getElementById('btn-prev').style.opacity=current===0?'0':'1';
  document.getElementById('btn-next').style.opacity=current===slides.length-1?'0':'1';
}
function navSlide(dir){slides[Math.max(0,Math.min(slides.length-1,current+dir))].scrollIntoView({behavior:'smooth'});}
document.addEventListener('keydown',e=>{if(e.key==='ArrowRight'||e.key==='ArrowDown')navSlide(1);if(e.key==='ArrowLeft'||e.key==='ArrowUp')navSlide(-1);});
updateUI();
</script>
```

#### T2 · SVG arrowhead markers
```html
<svg viewBox="0 0 600 300" width="100%" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrow" markerWidth="10" markerHeight="7" refX="9" refY="3.5" orient="auto">
      <polygon points="0 0,10 3.5,0 7" fill="#6b6a63"/>
    </marker>
    <marker id="arrow-dash" markerWidth="10" markerHeight="7" refX="9" refY="3.5" orient="auto">
      <polygon points="0 0,10 3.5,0 7" fill="#a09f97"/>
    </marker>
  </defs>
  <line x1="100" y1="150" x2="280" y2="150" stroke="#6b6a63" stroke-width="1.5" marker-end="url(#arrow)"/>
  <line x1="320" y1="150" x2="500" y2="150" stroke="#a09f97" stroke-width="1.5" stroke-dasharray="5,4" marker-end="url(#arrow-dash)"/>
</svg>
```

#### T3 · HTML5 drag-and-drop Kanban
```html
<style>
.board{display:flex;gap:1rem;} .column{flex:1;background:#eceae4;border-radius:12px;padding:1rem;}
.drop-zone{min-height:48px;}
.card{cursor:grab;padding:0.75rem;background:#fff;border:1px solid #dedad2;border-radius:8px;margin-bottom:0.5rem;transition:opacity 150ms ease;}
.card.dragging{opacity:0.4;} .drop-zone.drag-over{background:#faeee7;border:2px dashed #d95f2b;border-radius:8px;}
</style>
<script>
let dragCard=null;
document.querySelectorAll('.drop-zone').forEach(zone=>{
  zone.addEventListener('dragover',e=>{e.preventDefault();zone.classList.add('drag-over');});
  zone.addEventListener('dragleave',()=>zone.classList.remove('drag-over'));
  zone.addEventListener('drop',e=>{e.preventDefault();zone.classList.remove('drag-over');if(dragCard)zone.appendChild(dragCard);});
});
function makeCard(text){
  const c=document.createElement('div');c.className='card';c.draggable=true;c.textContent=text;
  c.addEventListener('dragstart',()=>{dragCard=c;c.classList.add('dragging');});
  c.addEventListener('dragend',()=>{dragCard=null;c.classList.remove('dragging');});
  return c;
}
</script>
```

#### T4 · CSS toggle switch
```html
<label class="toggle"><input type="checkbox" class="toggle-input">
<span class="toggle-track"><span class="toggle-thumb"></span></span>
<span>Enable feature</span></label>
<style>
.toggle{display:flex;align-items:center;gap:0.75rem;cursor:pointer;}
.toggle-input{position:absolute;opacity:0;width:0;height:0;}
.toggle-track{position:relative;width:40px;height:22px;border-radius:11px;background:#c8c7be;transition:background 140ms ease;flex-shrink:0;}
.toggle-input:checked+.toggle-track{background:#d95f2b;}
.toggle-thumb{position:absolute;top:3px;left:3px;width:16px;height:16px;border-radius:50%;background:#fff;box-shadow:0 1px 3px rgba(0,0,0,.2);transition:transform 140ms ease;}
.toggle-input:checked+.toggle-track .toggle-thumb{transform:translateX(18px);}
</style>
```

#### T5 · SVG download button
```js
function downloadSVG(svgId,filename){
  const blob=new Blob(['<?xml version="1.0" encoding="utf-8"?>\n'+new XMLSerializer().serializeToString(document.getElementById(svgId))],{type:'image/svg+xml'});
  const a=Object.assign(document.createElement('a'),{href:URL.createObjectURL(blob),download:filename||'diagram.svg'});
  document.body.appendChild(a);a.click();document.body.removeChild(a);URL.revokeObjectURL(a.href);
}
```

---

## After generating the file

### 1. Save the file
Filename: `YYYY-MM-DD-slug.html` in the matching folder.

### 2. Ask about deployment

After saving, always say:

> 파일이 저장되었습니다: `[경로/파일명.html]`
>
> **GitHub Actions로 자동 배포를 설정할까요?**
> HTML 파일을 main 브랜치에 푸시하면 자동으로 목차를 재생성하고
> GitHub Pages에 발행하는 파이프라인을 만들 수 있습니다.
>
> - 설정하려면 → **"설정해줘"** 또는 **"응"**
> - 지금 푸시만 하려면 → **"푸시해줘"**
> - 나중에 하려면 → 그냥 넘어가세요

### 3a. If user wants GitHub Actions setup

Walk through these steps one by one:

**Step 1 — Create `.github/workflows/deploy.yml`:**
```yaml
name: Build & Deploy to GitHub Pages
on:
  push:
    branches: ["main"]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: true
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - name: Generate index.html
        run: python3 scripts/generate_index.py
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: "."
      - id: deployment
        uses: actions/deploy-pages@v4
```

**Step 2 — Create `scripts/generate_index.py`:**
Tell the user: "generate_index.py 파일이 필요합니다. 지금 생성할까요, 아니면 https://github.com/imuzikr/open-make-html/blob/main/github-actions/generate_index.py 에서 직접 복사하시겠어요?"
If user wants it generated, create the file (see the full script content below).

**Step 3 — GitHub Pages 설정:**
```
레포 → Settings → Pages → Source → GitHub Actions 선택
```

**Step 4 — 배포 환경 허용:**
```
레포 → Settings → Environments → github-pages → Deployment branches → main 추가
```

After all steps, confirm:
```
설정이 완료되었습니다!
이제 main에 푸시할 때마다 GitHub Actions가 자동으로 실행됩니다.
배포 URL: https://<username>.github.io/<repo-name>/
```

**generate_index.py full content:**
```python
#!/usr/bin/env python3
"""Scans all HTML files and generates a beautiful index.html."""
import re
from pathlib import Path
from datetime import datetime

ROOT = Path(__file__).parent.parent
SKIP = {"index.html", "design-system.html"}
FOLDER_LABELS = {"articles":"Articles","specs":"Specs","reviews":"Reviews","reports":"Reports","explore":"Explore","tools":"Tools"}
FOLDER_GRADIENTS = {
    "articles":[("d95f2b","b84820"),("bf4e20","9e3c16"),("e07035","c85428"),("b85020","9a3e16")],
    "specs":   [("2d5a8e","1e3d6b"),("234e80","163460"),("3666a0","244e82"),("1e3d6b","122845")],
    "reviews": [("3a6b4a","2a4f38"),("2e5c3e","1e4029"),("468060","325a46"),("2a4f38","1a3526")],
    "reports": [("8a6a00","6b5200"),("7a5e00","5e4800"),("9a7800","7a6000"),("6b5200","503c00")],
    "explore": [("6b48c8","4e34a0"),("5c3cb8","422890"),("7a58d4","5e44b0"),("4e34a0","362280")],
    "tools":   [("2a7a7a","1d5555"),("1e6868","144848"),("348888","226666"),("1d5555","103838")],
}
FOLDER_GRADIENT_DEFAULT = [("6b6a63","4a4a45")]

def get_title(path):
    text = path.read_text(encoding="utf-8", errors="ignore")
    m = re.search(r"<title>(.*?)</title>", text, re.IGNORECASE | re.DOTALL)
    return m.group(1).strip() if m else path.stem.replace("-"," ").title()

def get_description(path):
    text = path.read_text(encoding="utf-8", errors="ignore")
    m = re.search(r'<meta\s+name=["\']description["\']\s+content=["\'](.*?)["\']', text, re.IGNORECASE)
    return m.group(1).strip() if m else ""

def get_date(path):
    m = re.match(r"(\d{4}-\d{2}-\d{2})", path.stem)
    return m.group(1) if m else ""

def get_og_image(path):
    text = path.read_text(encoding="utf-8", errors="ignore")
    m = re.search(r'<meta\s+property=["\']og:image["\']\s+content=["\'](.*?)["\']', text, re.IGNORECASE)
    if not m:
        m = re.search(r'<meta\s+content=["\'](.*?)["\']\s+property=["\']og:image["\']', text, re.IGNORECASE)
    return m.group(1).strip() if m else ""

def collect_files():
    folders = {}
    for html in sorted(ROOT.rglob("*.html"), reverse=True):
        if html.name in SKIP: continue
        rel = html.relative_to(ROOT)
        parts = rel.parts
        folder = parts[0] if len(parts) > 1 else "."
        if folder.startswith("."): continue
        folders.setdefault(folder, []).append({
            "path": str(rel), "title": get_title(html),
            "description": get_description(html), "date": get_date(html),
            "og_image": get_og_image(html),
        })
    return folders

BACK_BUTTON_MARKER = 'id="back-to-index"'

def _back_button_snippet(depth):
    prefix = "../" * depth
    return (
        '\n<!-- Back to index -->\n'
        f'<a id="back-to-index" href="{prefix}index.html" aria-label="목록으로 돌아가기" title="목록으로">'
        '<svg viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg" width="18" height="18">'
        '<path d="M8.5 15L3 10l5.5-5" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>'
        '<path d="M3 10h14" stroke="white" stroke-width="2" stroke-linecap="round"/>'
        '</svg></a>\n'
        '<style>\n'
        '#back-to-index{position:fixed;bottom:1.5rem;left:1.5rem;width:44px;height:44px;'
        'background:#6b6a63;color:#fff;border-radius:9999px;display:flex;'
        'align-items:center;justify-content:center;text-decoration:none;'
        'box-shadow:0 4px 16px rgba(0,0,0,.18);transition:background .15s,transform .15s;z-index:800;}\n'
        '#back-to-index:hover{background:#d95f2b;transform:translateX(-2px);}\n'
        '@media(max-width:600px){#back-to-index{bottom:1rem;left:1rem;}}\n'
        '</style>\n'
    )

def inject_back_button(path):
    text = path.read_text(encoding="utf-8", errors="ignore")
    if BACK_BUTTON_MARKER in text: return False
    if "</body>" not in text: return False
    rel = path.relative_to(ROOT)
    depth = len(rel.parts) - 1
    snippet = _back_button_snippet(depth)
    path.write_text(text.replace("</body>", snippet + "</body>", 1), encoding="utf-8")
    return True

def render_tab_buttons(folders):
    all_btn = '<button class="tab-btn active" onclick="filterFolder(\'all\', this)">All</button>'
    folder_btns = ""
    for folder in folders:
        label = FOLDER_LABELS.get(folder, folder.title())
        folder_btns += f'<button class="tab-btn" onclick="filterFolder(\'{folder}\', this)">{label}</button>'
    return all_btn + folder_btns

def render_cards(folders):
    html = ""
    for folder, files in folders.items():
        label = FOLDER_LABELS.get(folder, folder.title())
        variants = FOLDER_GRADIENTS.get(folder, FOLDER_GRADIENT_DEFAULT)
        for idx, f in enumerate(files):
            date_str = f"<span class='card-date'>{f['date']}</span>" if f["date"] else ""
            desc_str = f"<p class='card-desc'>{f['description']}</p>" if f["description"] else ""
            c1, c2 = variants[idx % len(variants)]
            if f["og_image"]:
                thumb = f"<div class='card-thumb'><img src='{f['og_image']}' alt='' loading='lazy'></div>"
            else:
                safe_title = f["title"].replace("<","&lt;").replace(">","&gt;")
                thumb = (f"<div class='card-thumb card-thumb-grad'"
                         f" style='background:linear-gradient(135deg,#{c1},#{c2})'>"
                         f"<span class='card-thumb-text'>{safe_title}</span></div>")
            html += f"""
    <a class="card" href="{f['path']}" data-folder="{folder}">
      {thumb}
      <div class="card-body">
        <div class="card-header"><span class="card-folder">{label}</span>{date_str}</div>
        <h3 class="card-title">{f['title']}</h3>
        {desc_str}
      </div>
    </a>"""
    return html

def build(folders):
    tab_buttons = render_tab_buttons(folders)
    cards = render_cards(folders)
    total = sum(len(v) for v in folders.values())
    generated = datetime.utcnow().strftime("%Y-%m-%d %H:%M UTC")
    return f"""<!DOCTYPE html>
<html lang="ko"><head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>My HTML Library</title>
<style>
:root{{--color-bg:#f5f2ed;--color-bg-alt:#eceae4;--color-bg-card:#ffffff;
  --color-text:#1a1a18;--color-text-muted:#6b6a63;--color-text-faint:#a09f97;
  --color-border:#dedad2;--color-accent:#d95f2b;--color-accent-hover:#c2521f;--color-accent-soft:#faeee7;
  --font-sans:-apple-system,BlinkMacSystemFont,"Segoe UI",Helvetica,Arial,sans-serif;
  --text-sm:0.875rem;--text-base:1rem;--text-lg:1.125rem;--text-2xl:1.5rem;
  --weight-medium:500;--weight-semibold:600;--weight-bold:700;
  --space-2:0.5rem;--space-3:0.75rem;--space-4:1rem;--space-6:1.5rem;--space-8:2rem;--space-12:3rem;
  --radius-md:8px;--radius-lg:12px;--shadow-sm:0 1px 3px rgba(0,0,0,.07);--shadow-md:0 4px 12px rgba(0,0,0,.08);}}
*,*::before,*::after{{box-sizing:border-box;margin:0;padding:0}}
body{{font-family:var(--font-sans);background:var(--color-bg);color:var(--color-text);min-height:100vh}}
header{{background:var(--color-bg-card);border-bottom:1px solid var(--color-border);padding:var(--space-6) var(--space-8);display:flex;align-items:baseline;gap:var(--space-4);}}
header h1{{font-size:var(--text-2xl);font-weight:var(--weight-bold);}}
header .meta{{font-size:var(--text-sm);color:var(--color-text-faint);margin-left:auto}}
.container{{max-width:1100px;margin:0 auto;padding:var(--space-8)}}
.toolbar{{display:flex;align-items:center;gap:var(--space-2);flex-wrap:wrap;margin-bottom:var(--space-6)}}
.tab-btn{{padding:var(--space-2) var(--space-4);border:1px solid var(--color-border);background:var(--color-bg-card);color:var(--color-text-muted);border-radius:var(--radius-md);font-size:var(--text-sm);font-weight:var(--weight-medium);cursor:pointer;transition:all 120ms ease;}}
.tab-btn:hover{{border-color:var(--color-accent);color:var(--color-accent)}}
.tab-btn.active{{background:var(--color-accent);border-color:var(--color-accent);color:#fff}}
.search{{margin-left:auto}}.search input{{padding:var(--space-2) var(--space-4);border:1px solid var(--color-border);border-radius:var(--radius-md);font-size:var(--text-sm);background:var(--color-bg-card);color:var(--color-text);outline:none;width:200px;}}
.search input:focus{{border-color:var(--color-accent)}}
.grid{{display:grid;grid-template-columns:repeat(auto-fill,minmax(300px,1fr));gap:var(--space-4)}}
.card{{background:var(--color-bg-card);border:1px solid var(--color-border);border-radius:var(--radius-lg);text-decoration:none;color:inherit;display:flex;flex-direction:column;overflow:hidden;box-shadow:var(--shadow-sm);transition:all 180ms ease;}}
.card:hover{{box-shadow:var(--shadow-md);border-color:var(--color-accent);transform:translateY(-2px)}}
.card-thumb{{width:100%;height:140px;overflow:hidden;flex-shrink:0;}}
.card-thumb img{{width:100%;height:100%;object-fit:cover;display:block;}}
.card-thumb-grad{{display:flex;align-items:flex-end;padding:var(--space-4);}}
.card-thumb-text{{font-size:var(--text-sm);font-weight:var(--weight-semibold);color:rgba(255,255,255,0.95);line-height:1.45;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden;text-shadow:0 1px 6px rgba(0,0,0,.35);min-height:calc(1.45em * 2);}}
.card-body{{padding:var(--space-6);flex:1;display:flex;flex-direction:column;gap:var(--space-3);}}
.card-header{{display:flex;align-items:center;justify-content:space-between;}}
.card-folder{{font-size:var(--text-sm);font-weight:var(--weight-medium);color:var(--color-accent);background:var(--color-accent-soft);padding:3px var(--space-3);border-radius:var(--radius-md);}}
.card-date{{font-size:var(--text-sm);color:var(--color-text-faint)}}
.card-title{{font-size:var(--text-lg);font-weight:var(--weight-semibold);line-height:1.5;}}
.card-desc{{font-size:var(--text-sm);color:var(--color-text-muted);line-height:1.7;}}
.empty{{text-align:center;color:var(--color-text-faint);padding:var(--space-12);font-size:var(--text-lg)}}
footer{{text-align:center;padding:var(--space-8);color:var(--color-text-faint);font-size:var(--text-sm);border-top:1px solid var(--color-border);margin-top:var(--space-12)}}
@media(max-width:600px){{header{{padding:var(--space-4)}}.container{{padding:var(--space-4)}}.search input{{width:100%}}.toolbar{{flex-direction:column;align-items:stretch}}.search{{margin-left:0}}}}
</style></head><body>
<header><h1>My HTML Library</h1><span class="meta">{total} files · Updated {generated}</span></header>
<div class="container">
  <div class="toolbar">{tab_buttons}<div class="search"><input type="text" placeholder="Search..." oninput="filterSearch(this.value)"></div></div>
  <div class="grid" id="grid">{cards}</div>
  <div class="empty" id="empty" style="display:none">No files found.</div>
</div>
<footer>Generated by GitHub Actions · <a href="design-system.html" style="color:var(--color-accent)">Design System</a></footer>
<script>
let currentFolder='all',currentSearch='';
function update(){{const cards=document.querySelectorAll('.card');let visible=0;
  cards.forEach(c=>{{const fm=currentFolder==='all'||c.dataset.folder===currentFolder;
    const sm=!currentSearch||c.textContent.toLowerCase().includes(currentSearch);
    const show=fm&&sm;c.style.display=show?'':'none';if(show)visible++;}});
  document.getElementById('empty').style.display=visible===0?'':'none';}}
function filterFolder(folder,btn){{currentFolder=folder;document.querySelectorAll('.tab-btn').forEach(b=>b.classList.remove('active'));btn.classList.add('active');update();}}
function filterSearch(val){{currentSearch=val.toLowerCase();update();}}
</script></body></html>"""

if __name__ == "__main__":
    injected = 0
    for html in sorted(ROOT.rglob("*.html")):
        if html.name in SKIP: continue
        rel = html.relative_to(ROOT)
        if rel.parts[0].startswith("."): continue
        if inject_back_button(html): injected += 1
    if injected:
        print(f"Injected back-to-index button into {injected} file(s)")
    folders = collect_files()
    html_out = build(folders)
    out = ROOT / "index.html"
    out.write_text(html_out, encoding="utf-8")
    total = sum(len(v) for v in folders.values())
    print(f"Generated index.html — {total} files across {len(folders)} folders")
```

### 3b. If user wants to push only (no GitHub Actions)

```bash
git add <file-path>
git commit -m "Add article: <title>"
git push origin HEAD:main
```

---

## Quality bar

- Genuinely polished when opened in a browser
- Hierarchy obvious at a glance
- Real data, not Lorem ipsum
- Interactive features must actually work

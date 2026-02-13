---
name: lovable-to-html
description: Convert React/TSX component files (from Lovable, Figma exports, or any React project) into static HTML/CSS/JS with pixel-perfect fidelity. Use this skill whenever the user provides TSX files, a ZIP of React components, or asks to convert React/Lovable components to static HTML. Also trigger when the user says things like "convert my Lovable app", "export to static HTML", "make this TSX into HTML", or "I want a static version of my React project". The output must look identical to the original — zero visual drift, exact styles preserved.
---

# Lovable → Static HTML Converter

This skill takes React/TSX source files (typically exported from Lovable or Figma) and converts them into clean, deployable static HTML + CSS + JS — preserving every pixel of the original design.

The user provides TSX/JSX files (often in a ZIP). Your job is to produce a multi-file static web project that matches the original UI exactly, screen by screen.

## The Core Promise

**The HTML output must be visually indistinguishable from the React original.** Every color, spacing value, font, border radius, shadow, and layout must survive the conversion intact. You are not redesigning — you are translating.

## Phase 1: Intake & Analysis

Before touching any code, build a complete mental model of the source.

### 1.1 Unzip and Inventory

If input is a ZIP:
```bash
unzip -o uploaded.zip -d /home/claude/source_tsx
find /home/claude/source_tsx -name "*.tsx" -o -name "*.jsx" -o -name "*.css" | sort
```

List every file and classify it:
- **Page/Screen components** (contain full layouts, likely route-level)
- **Reusable UI components** (buttons, cards, modals, navbars)
- **Style files** (CSS modules, global CSS, Tailwind config)
- **Type/util files** (not visual — skip or extract constants only)

### 1.2 Read Every TSX File

Read all component files. For each one, note:
- What does this screen/component look like?
- What CSS classes or inline styles are used?
- Which Tailwind utilities appear? (extract all unique classes)
- Are there any conditional styles, variants, or state-based classes?
- What are the data/props this component renders?

### 1.3 Identify Screens

Determine which components map to distinct HTML pages:
- `App.tsx` or router config → map of routes → pages
- Each top-level page component → one `.html` file
- If no routing is found, treat the main component as `index.html`

### 1.4 Identify Shared Design Tokens

Extract from the source:
- **Colors**: all unique hex/rgba values used in Tailwind classes or CSS
- **Typography**: font families, sizes, weights, line heights
- **Spacing**: padding, margin, gap patterns
- **Shadows, borders, radius**: every variant used
- **Breakpoints**: if any responsive behavior exists

Document these in a design token comment block at the top of `style.css`.

---

## Phase 2: Architecture — HTML Structure

### 2.1 Output File Plan

Decide the file structure before writing:

```
output/
├── index.html          ← main screen / home page
├── [screen-name].html  ← one file per additional screen
├── style.css           ← all styles, globally shared
└── app.js              ← interactivity (navigation, modals, toggles)
```

If there is only one screen, `index.html` + `style.css` + `app.js` is sufficient.

### 2.2 HTML Conversion Rules

**Component → HTML element mapping:**

| TSX Pattern | HTML Output |
|-------------|-------------|
| `<div className="...">` | `<div class="...">` |
| `<button onClick={fn}>` | `<button onclick="fn()">` |
| `<Link to="/path">` | `<a href="path.html">` |
| `<img src={url}>` | `<img src="url" alt="...">` |
| `<input {...props}>` | `<input>` with all props as HTML attrs |
| `{condition && <El>}` | Include the element (use JS to hide if dynamic) |
| `{items.map(item => ...)}` | Repeat the HTML element for each sample item |

**Semantic HTML — always use:**
- `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`, `<aside>`
- `<button>` for clickable actions (not `<div>`)
- `<a>` for navigation
- Proper heading hierarchy (h1 → h2 → h3)

**For dynamic/mapped content:** Render a representative set (3–5 items) to demonstrate the layout. Note in comments if real data is needed.

### 2.3 Inter-Screen Navigation

Convert React Router `<Link>` elements to `<a href="screen-name.html">`. Map all routes. If there is a nav bar shared across screens, replicate it in each HTML file (or include it via a JS snippet that injects shared nav).

---

## Phase 3: Styling — Precision is Everything

### 3.1 The Style Preservation Rule

**You must use ONLY the CSS values present in the original files.** Do not invent new values. Do not "clean up" spacing. Do not substitute fonts. Do not round pixel values.

### 3.2 Tailwind Strategy

Most Lovable exports use Tailwind. Two approaches — use whichever is cleaner:

**Option A — Tailwind CDN (fastest, recommended):**
```html
<script src="https://cdn.tailwindcss.com"></script>
```
Include this in `<head>` of every HTML file. Keep all `class` attributes exactly as-is. This is the most faithful approach when the source uses Tailwind.

**Option B — Manual CSS extraction (for production/offline use):**
Extract every unique Tailwind class used across all TSX files, generate the equivalent CSS, and write to `style.css`. Key translations:

```css
.flex { display: flex; }
.flex-col { flex-direction: column; }
.items-center { align-items: center; }
.justify-between { justify-content: space-between; }
.gap-4 { gap: 1rem; }
.p-4 { padding: 1rem; }
.px-6 { padding-left: 1.5rem; padding-right: 1.5rem; }
.py-2 { padding-top: 0.5rem; padding-bottom: 0.5rem; }
.text-sm { font-size: 0.875rem; line-height: 1.25rem; }
.text-xl { font-size: 1.25rem; line-height: 1.75rem; }
.font-bold { font-weight: 700; }
.rounded-lg { border-radius: 0.5rem; }
.rounded-full { border-radius: 9999px; }
.shadow-md { box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); }
/* Extend for all classes present in the source */
```

When the source has custom CSS (globals.css, CSS modules), copy all rules verbatim into `style.css`.

### 3.3 CSS Variable Setup

Define design tokens at the top of `style.css`:
```css
/*
 * ================================================
 * DESIGN TOKENS — extracted from source TSX/Tailwind
 * ================================================
 * Colors: [list all here]
 * Typography: [list font families and scale]
 * Spacing: [document the system]
 */
:root {
  --color-primary: #[hex];
  --color-secondary: #[hex];
  --color-bg: #[hex];
  --color-text: #[hex];
  --font-sans: '[FontName]', system-ui, -apple-system, sans-serif;
}
```

### 3.4 Font and Icon Inclusion

For Google Fonts:
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=FontName:wght@400;500;600;700&display=swap" rel="stylesheet">
```

For lucide-react icons — replace with CDN + data attributes:
```html
<script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script>
```
```html
<!-- <Home className="w-5 h-5" /> becomes: -->
<i data-lucide="home" class="w-5 h-5"></i>
```
```js
// Call in DOMContentLoaded:
lucide.createIcons();
```

---

## Phase 4: Interactivity — app.js

### 4.1 React State → Vanilla JS

| React Pattern | JS Equivalent |
|---------------|---------------|
| `useState(false)` toggle | `let open = false; btn.onclick = () => { open = !open; el.classList.toggle('hidden'); }` |
| Tab switching | `querySelectorAll` + active class toggling |
| Modal open/close | Overlay div + class toggle |
| `useEffect(() => {}, [])` | `document.addEventListener('DOMContentLoaded', fn)` |
| `className={isActive ? 'a' : 'b'}` | `el.classList.toggle('a', condition)` |

### 4.2 Standard app.js Boilerplate

```js
document.addEventListener('DOMContentLoaded', () => {
  // Initialize icons
  if (typeof lucide !== 'undefined') lucide.createIcons();

  // Mark active nav link
  const currentPage = window.location.pathname.split('/').pop() || 'index.html';
  document.querySelectorAll('nav a').forEach(link => {
    if (link.getAttribute('href') === currentPage) link.classList.add('active');
  });

  // [Additional interactive behaviors go here]
});
```

### 4.3 Scope

Convert all visible interactive behaviors. For server-side data fetching (API calls, DB reads), replace with static placeholder content and mark with `<!-- TODO: Replace with real data -->`.

---

## Phase 5: Output & Quality Check

### 5.1 HTML File Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Page Title]</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="style.css">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=..." rel="stylesheet">
  <script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script>
</head>
<body>
  <!-- Converted content -->
  <script src="app.js"></script>
</body>
</html>
```

### 5.2 Self-Audit Checklist

Before presenting output:
- [ ] Every screen has a corresponding HTML file
- [ ] All navigation links use correct `.html` filenames
- [ ] CSS classes preserved exactly from source
- [ ] Custom CSS copied verbatim into `style.css`
- [ ] Images use original URLs or descriptive placeholders
- [ ] Icons converted and initialized
- [ ] Interactive elements (tabs, modals, dropdowns) work in vanilla JS
- [ ] Fonts loaded via CDN
- [ ] No React-specific syntax remains (`useState`, `useEffect`, `{expression}`, `className={}`)

### 5.3 ZIP and Deliver

```bash
cd /home/claude/output && zip -r /mnt/user-data/outputs/static-site.zip .
```

Present the ZIP with `present_files`. Then give the user a brief summary (screens converted, styling approach used, any dynamic data replaced).

---

## Common TSX → HTML Patterns Reference

See `references/tsx-patterns.md` for an extended cheat sheet of TSX-to-HTML translations including shadcn/ui components, framer-motion animations, and complex state patterns.

---

## Edge Cases

**Unresolvable imports:** Skip, note it, implement what's visible in the render output.

**TypeScript annotations:** Strip all types. `const x: string = y` → `const x = y`.

**Context/Redux state:** Replace with static representative values.

**framer-motion:** Convert `animate={{ opacity: 1 }}` to CSS `transition: opacity 0.3s ease`.

**SVG imports:** Inline the SVG markup. If file not available, use a size-matched placeholder.

**Shadcn/UI components:** These are styled HTML — unwrap to semantic elements with their applied classes:
- `<Button>` → `<button class="[btn classes]">`
- `<Card>` / `<CardContent>` → `<div class="[card classes]">`
- `<Dialog>` → `<div class="modal-overlay hidden">` + JS toggle

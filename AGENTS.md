# AGENTS.md - Coding Guidelines for zhouzk-blog

**Generated:** 2026-02-23  
**Commit:** d7ca9ce  
**Branch:** master

---

## OVERVIEW

Static HTML blog and landing pages for zhouzk.com. No build system—pure HTML/CSS/JS with inline styles.

**Total files:** 18 HTML  
**Total lines:** ~5,700

---

## STRUCTURE

```
/
├── index.html              # Homepage + landing page index
├── article/                # Blog articles
│   └── *.html
├── landing-pages/          # SEO product pages
│   ├── *.html
│   └── backup/            # Archived versions
└── AGENTS.md              # This file
```

---

## WHERE TO LOOK

| Task | Location | Notes |
|------|----------|-------|
| New landing page | `landing-pages/` | Follow naming convention in `landing-pages/AGENTS.md` |
| New blog article | `article/` | Standard HTML structure |
| Homepage updates | `index.html` | Contains landing page list |
| Archived pages | `landing-pages/backup/` | Historical versions, do not edit |

---

## CONVENTIONS (THIS PROJECT)

### HTML
- HTML5 doctype, 4-space indent
- Language: `lang="en"` or `lang="zh"` as appropriate
- Max line length: 120 characters

### CSS (All Inline)
- CSS custom properties in `:root` for theming:
  ```css
  :root {
      --primary: #E4007F;
      --dark: #1d1d1f;
      --gray: #86868b;
  }
  ```
- Class naming: kebab-case (`.nav-links`, `.hero-section`)
- Responsive breakpoints: `768px`, `480px`

### JavaScript
- Minimal JS—prefer CSS for animations
- Vanilla JS only, wrap in `DOMContentLoaded`

### SEO Requirements (All Pages)
1. Meta: description, keywords (landing pages)
2. Open Graph: og:type, og:url, og:title, og:description, og:image
3. Schema.org JSON-LD (product pages): Product, Offer, AggregateRating

### Typography
- Primary: Inter (Google Fonts)
- Fallback: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- Base: 16px, line-height 1.5-1.6

### External Resources
- Icons: Font Awesome CDN
- Fonts: Google Fonts
- Images: Yami CDN

---

## ANTI-PATTERNS (THIS PROJECT)

- **NEVER** create standalone CSS/JS files—all inline in HTML
- **NEVER** add build tools or package managers—keep it static
- **NEVER** skip schema markup on product landing pages
- **NEVER** use Chinese font stack for English content (and vice versa)

---

## COMMANDS

```bash
# Preview
python3 -m http.server 8000

# No build/test/lint—manual testing only
```

---

## TESTING CHECKLIST

Before committing:

- [ ] Page loads without console errors
- [ ] Responsive: mobile (320px), tablet (768px), desktop (1024px+)
- [ ] All links clickable
- [ ] Meta tags accurate
- [ ] Schema markup validates (Google Rich Results)
- [ ] Images load
- [ ] No broken internal links

---

## COMMON PATTERNS

### Sticky Nav
```css
.nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 1000;
    background: rgba(255,255,255,0.72);
    backdrop-filter: saturate(180%) blur(20px);
}
```

### CTA Button
```css
.cta-button {
    background: var(--primary);
    color: var(--white);
    padding: 12px 24px;
    border-radius: 980px;
    text-decoration: none;
    transition: background 0.2s;
}
```

---

## CHILD DOCUMENTS

- `landing-pages/AGENTS.md` — Landing page specific conventions

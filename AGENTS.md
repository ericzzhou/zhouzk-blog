# AGENTS.md - Coding Guidelines for zhouzk-blog

**Generated:** 2026-02-23
**Commit:** d7ca9ce
**Branch:** master

---

## OVERVIEW

Static HTML blog and landing pages for zhouzk.com. No build system—pure HTML/CSS/JS with inline styles.

**Total files:** 100+ HTML landing pages
**Total lines:** ~50,000+

---

## COMMANDS

```bash
# Local preview
python3 -m http.server 8000

# No build/test/lint—manual testing only
```

---

## STRUCTURE

```
/
├── index.html              # Homepage + landing page index
├── sitemap.xml            # SEO sitemap
├── robots.txt             # Crawler rules
├── article/               # Blog articles
│   └── *.html
├── landing-pages/         # SEO product pages
│   ├── *.html            # 100+ marketing pages
│   ├── backup/           # Templates and archives (read-only)
│   └── AGENTS.md         # Landing page specific conventions
├── .kiro/skills/seo-landing-page-generator/
│   └── SKILL.md          # Landing page generation SOP
└── AGENTS.md             # This file
```

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
      --primary-deep: #5D4037;
      --primary-light: #FFEBEE;
      --accent: #FF9800;
      --dark: #1d1d1f;
      --gray: #86868b;
      --light-gray: #f5f5f7;
      --white: #ffffff;
  }
  ```
- Class naming: kebab-case (`.nav-links`, `.hero-section`)
- Responsive breakpoints: `768px`, `480px`

### JavaScript
- Minimal JS—prefer CSS for animations
- Vanilla JS only, wrap in `DOMContentLoaded`

### Typography
- Primary: Inter (Google Fonts)
- Fallback: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- Base: 16px, line-height 1.5-1.6

### External Resources
- Icons: Font Awesome CDN
- Fonts: Google Fonts
- Images: Yami CDN

---

## LANDING PAGE SYSTEM

### Generation Workflow

Use the Kiro skill to generate new landing pages:

1. **Skill location**: `.kiro/skills/seo-landing-page-generator/SKILL.md`
2. **Required inputs**: Product name, purchase URL (Yami product page)
3. **Research tool**: Mandatory use of Playwright MCP browser tools to fetch product data
4. **Output location**: `landing-pages/{kebab-case-product-name}.html`

### Naming Convention

```
{brand}-{product-name}-{variant}-{size}.html

Examples:
├── zhou-hei-ya-braising-sauce-14-1oz.html
├── chagee-boya-jasmine-tea.html
├── beauty-of-joseon-relief-sun-spf50-50ml.html
└── kikkoman-hello-kitty-soy-sauce-dispenser.html
```

### SEO Requirements (Mandatory)

Every landing page MUST include:

1. **H1 Tag**: Must be the product name, NOT a marketing slogan
2. **Schema.org JSON-LD**: Product structured data with `offers`, `aggregateRating`
3. **Open Graph**: `og:type` (product), `og:url`, `og:title`, `og:description`, `og:image`
4. **Meta Tags**: description, keywords
5. **Image Attributes**: `width`, `height`, `alt`, `fetchpriority="high"`
6. **UTM Parameters**: Append `?utm_source=zhouzk.com` to purchase links (use `&` if URL already has params)

### Standard Page Structure

```
┌─────────────────────────────────────┐
│ Nav: Logo | Links | [Buy Now]       │  ← sticky, frosted glass
├─────────────────────────────────────┤
│ Hero:                               │
│   - Eyebrow tagline                 │
│   - <h1>Product Name</h1>           │
│   - Product image (rounded/circle)  │
│   - Price: $current ~~$original~~ % │
│   - [Buy Now] [Learn More]          │
├─────────────────────────────────────┤
│ Pain Points (2×2 grid)              │
├─────────────────────────────────────┤
│ Scenes (2×2 grid)                   │
├─────────────────────────────────────┤
│ Stats (4 big numbers)               │
├─────────────────────────────────────┤
│ Ingredients (optional)              │
├─────────────────────────────────────┤
│ Atmosphere (2×2 colored grid)       │
├─────────────────────────────────────┤
│ CTA (final purchase prompt)         │
├─────────────────────────────────────┤
│ Footer: Disclaimer + Copyright      │
└─────────────────────────────────────┘
```

### Post-Creation Checklist

After creating a new landing page:

1. **Update homepage index**: Find `<!-- LANDING-PAGE-LINKS:START -->` and `<!-- LANDING-PAGE-LINKS:END -->` in `index.html`, add new `<li>` entry between them (newest first)
2. **Update sitemap**: Add new URL to `sitemap.xml`
3. **Validate**:
   - Page loads without console errors
   - Responsive: mobile (320px), tablet (768px), desktop (1024px+)
   - Schema markup validates (Google Rich Results)
   - All links clickable
   - Images load

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

## WHERE TO LOOK

| Task | Location | Notes |
|------|----------|-------|
| New landing page | `landing-pages/` | Follow naming convention above |
| Landing page template | `landing-pages/backup/template-kikkoman.html` | Copy and modify |
| Example page | `landing-pages/zhou-hei-ya-braising-sauce-*.html` | Complete working example |
| New blog article | `article/` | Standard HTML structure |
| Homepage updates | `index.html` | Contains landing page list |
| Generation SOP | `.kiro/skills/seo-landing-page-generator/SKILL.md` | Complete workflow |

---

## ANTI-PATTERNS (THIS PROJECT)

- **NEVER** create standalone CSS/JS files—all inline in HTML
- **NEVER** add build tools or package managers—keep it static
- **NEVER** skip schema markup on product landing pages
- **NEVER** use Chinese font stack for English content (and vice versa)
- **NEVER** use `#` as placeholder for purchase links
- **NEVER** use marketing slogan as H1—must be product name

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

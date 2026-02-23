# AGENTS.md - Coding Guidelines for zhouzk-blog

## Project Overview

Static HTML blog and landing pages for zhouzk.com. No build system, package manager, or test framework—pure HTML/CSS/JS.

## Build/Test/Lint Commands

**No build system configured.** This is a static site.

- **Preview**: Open HTML files directly in browser or serve with `python3 -m http.server 8000`
- **No tests**: Manual testing only
- **No linting**: Follow style guidelines below

## Code Style Guidelines

### HTML Structure

- Use HTML5 doctype: `<!DOCTYPE html>`
- Include required meta tags:
  ```html
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Page Title | zhouzk.com</title>
  <meta name="description" content="...">
  ```
- Language attribute: `lang="en"` or `lang="zh"` as appropriate
- Indent: 4 spaces
- Max line length: 120 characters

### CSS Conventions

- Use CSS custom properties in `:root` for theming:
  ```css
  :root {
      --primary: #E4007F;
      --dark: #1d1d1f;
      --gray: #86868b;
  }
  ```
- Class naming: kebab-case (e.g., `.nav-links`, `.hero-section`)
- One declaration per line for readability
- Group related properties (layout, typography, colors)
- Use Flexbox/Grid for layouts
- Include responsive breakpoints:
  ```css
  @media (max-width: 768px) { ... }
  @media (max-width: 480px) { ... }
  ```

### JavaScript

- Minimal JS—prefer CSS for animations
- Use vanilla JS (no frameworks)
- Event listeners: `addEventListener` over inline handlers
- Wrap in `DOMContentLoaded`:
  ```javascript
  document.addEventListener('DOMContentLoaded', function() {
      // Code here
  });
  ```

### SEO Requirements

Every page must include:

1. **Meta tags**: description, keywords (for landing pages)
2. **Open Graph tags**: og:type, og:url, og:title, og:description, og:image
3. **Schema.org JSON-LD** (for product landing pages):
   ```html
   <script type="application/ld+json">
   {
       "@context": "https://schema.org",
       "@type": "Product",
       "name": "...",
       "image": "...",
       "description": "..."
   }
   </script>
   ```

### File Organization

```
/
├── index.html              # Homepage
├── article/                # Blog articles
│   └── *.html
├── landing-pages/          # SEO product pages
│   ├── *.html
│   └── backup/            # Archived versions
└── AGENTS.md              # This file
```

### Landing Page Naming

- Use kebab-case: `product-name-with-details.html`
- Include size/variant: `zhou-hei-ya-braising-sauce-14-1oz.html`
- Update `index.html` landing page list when adding new pages

### Typography & Design

- Primary font: Inter (Google Fonts)
- Fallback: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- Base font size: 16px
- Line height: 1.5-1.6 for body text
- Use system font stack for Chinese content

### External Resources

- Font Awesome for icons (CDN)
- Google Fonts for typography
- Yami CDN for product images

## Cursor/Copilot Rules

None configured. Follow guidelines above.

## Testing Checklist

Before committing:

- [ ] Page loads without console errors
- [ ] Responsive on mobile (320px), tablet (768px), desktop (1024px+)
- [ ] All links clickable and correct
- [ ] Meta tags present and accurate
- [ ] Schema markup validates (test with Google Rich Results)
- [ ] Images load correctly
- [ ] No broken internal links

## Common Patterns

### Sticky Navigation

```css
.nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 1000;
    background: rgba(255,255,255,0.72);
    backdrop-filter: saturate(180%) blur(20px);
}
```

### CTA Buttons

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

### Product Card Structure

```html
<div class="product-card">
    <img src="..." alt="...">
    <h2>Product Name</h2>
    <p class="price">$X.XX</p>
    <a href="..." class="cta-button">Buy Now</a>
</div>
```

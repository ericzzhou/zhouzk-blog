# AGENTS.md - Landing Pages

**Parent:** ../AGENTS.md

---

## OVERVIEW

SEO-optimized product landing pages. Each page targets specific keywords with complete schema markup.

**Files:** 7 active + 8 backup  
**Pattern:** Self-contained HTML files with inline CSS/JS

---

## NAMING CONVENTION

Use kebab-case with product details:

```
{brand}-{product-name}-{variant}-{size}.html

Examples:
├── zhou-hei-ya-braising-sauce-14-1oz.html
├── chagee-boya-jasmine-tea.html
├── kikkoman-hello-kitty-soy-sauce-dispenser.html
└── malawangzi-latiao-mala-extra-spicy.html
```

---

## REQUIRED STRUCTURE

Every landing page MUST include:

### 1. Meta Tags
```html
<title>Product Name | Descriptor</title>
<meta name="description" content="...">
<meta name="keywords" content="...">
```

### 2. Open Graph
```html
<meta property="og:type" content="product">
<meta property="og:url" content="...">
<meta property="og:title" content="...">
<meta property="og:description" content="...">
<meta property="og:image" content="...">
```

### 3. Schema.org JSON-LD
```html
<script type="application/ld+json">
{
    "@context": "https://schema.org",
    "@type": "Product",
    "name": "...",
    "image": "...",
    "description": "...",
    "brand": { "@type": "Brand", "name": "..." },
    "offers": {
        "@type": "Offer",
        "url": "...",
        "priceCurrency": "USD",
        "price": "...",
        "availability": "https://schema.org/InStock"
    },
    "aggregateRating": {
        "@type": "AggregateRating",
        "ratingValue": "...",
        "bestRating": "5",
        "ratingCount": "..."
    }
}
</script>
```

---

## DESIGN PATTERNS

### CSS Variables (Standard)
```css
:root {
    --primary: #8B0000;        /* Brand color */
    --primary-deep: #5D4037;
    --primary-light: #FFEBEE;
    --accent: #FF9800;
    --dark: #1d1d1f;
    --gray: #86868b;
    --light-gray: #f5f5f7;
    --white: #ffffff;
}
```

### Sections (Standard Order)
1. **Nav** — Sticky, glassmorphism
2. **Hero** — Product image + CTA
3. **Features** — 3-4 key benefits
4. **Product Details** — Specs, ingredients
5. **Reviews** — Testimonials
6. **FAQ** — Schema markup
7. **Footer** — Links, disclaimer

---

## WHEN ADDING NEW PAGE

1. Copy template from `backup/template-kikkoman.html`
2. Update all product-specific content
3. Update `../index.html` landing page list
4. Validate schema at [Google Rich Results](https://search.google.com/test/rich-results)

---

## BACKUP DIRECTORY

`backup/` contains archived versions. **Do not modify**—for reference only.

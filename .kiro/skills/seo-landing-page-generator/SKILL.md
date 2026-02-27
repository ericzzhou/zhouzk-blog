---
name: SEO Landing Page Generator
description: Generates Apple-style SEO marketing landing pages for specific products. Triggers when user asks to create a product marketing page, SEO landing page, or product-specific promotional page. Follows a complete SOP including product research, brand color extraction, pain point analysis, scene-based storytelling, and atmosphere marketing. Outputs a single standalone HTML file.
---

# SEO Landing Page Generator

Creates high-converting, Apple-inspired marketing landing pages for individual products. The output is a single standalone HTML file with embedded CSS and JS — no build tools required.

## When to Use This Skill

Activate when the user says things like:
- "Create a marketing page for [product]"
- "Build an SEO landing page for [product]"
- "Make a product page for [product]"
- "Generate a promotional page for [product]"
- "I need a landing page for [product]"

## SOP (Standard Operating Procedure)

Follow these steps in order. Do not skip steps.

### Step 1: Gather Inputs

Collect the following from the user:

| Input | Required | Default |
|-------|----------|---------|
| Product name / description | Yes | — |
| Purchase link (URL) | Yes — ask if not provided, wait for response | — |
| Language | No | English |
| Target audience notes | No | Infer from product |

If the user does NOT provide a purchase link, you MUST ask:

> "What's the purchase link for this product? I need it for the Buy Now buttons."

**Wait for the user's response before continuing.** Do not generate the page without a real purchase link.

### Step 2: Product Research

**只能使用 Playwright MCP 浏览器工具**（`mcp_browser_navigate`、`mcp_browser_snapshot`、`mcp_browser_evaluate`、`mcp_browser_click` 等）来获取产品数据。**绝对禁止**使用 `web_search`、`webFetch`、`remote_web_search`、`mcp_serpapi_search` 或任何其他非 Playwright 的网络工具。

操作流程：
1. 使用 `mcp_browser_navigate` 打开产品购买链接（或 Yami 搜索页 `https://www.yami.com/en/search?q={item_number}`）
2. 使用 `mcp_browser_snapshot` 获取页面快照，提取产品信息
3. 使用 `mcp_browser_evaluate` 执行 JS 提取结构化数据（图片 URL、价格、评分等）
4. 如果页面需要交互（如点击展开详情），使用 `mcp_browser_click`

需要收集的信息：
1. **Product details** — name, brand, price, size/weight, key features, ingredients/materials
2. **Brand identity** — brand colors, visual style, brand positioning, heritage/story
3. **Product images** — find CDN image URLs from the purchase link page (use `mcp_browser_evaluate` to extract `img` src attributes)
4. **Competitive context** — what makes this product unique vs alternatives
5. **Customer sentiment** — reviews, ratings, common praise/complaints

**提取图片 URL 的推荐 JS 代码：**
```javascript
() => {
    const imgs = document.querySelectorAll('img[src*="cdn.yamibuy.net"]');
    return Array.from(imgs).map(img => img.src);
}
```

**提取价格的推荐 JS 代码：**
```javascript
() => {
    const priceEl = document.querySelector('.price-current, .unit-price, [class*="price"]');
    return priceEl ? priceEl.textContent.trim() : 'N/A';
}
```

### Step 3: Define Brand Color Palette

Based on research, define a CSS custom property palette that matches the product's brand identity:

```css
:root {
    --primary: /* brand's main color */;
    --primary-deep: /* darker shade for hover states */;
    --primary-light: /* lighter tint for backgrounds */;
    --accent: /* complementary accent color */;
    --accent-deep: /* darker shade of accent for hover states */;
    --cream: /* warm neutral background */;
    --dark: #1d1d1f;
    --gray: #86868b;
    --light-gray: #f5f5f7;
    --white: #ffffff;
}
```

Color selection rules:
- Extract primary color from the brand's logo or packaging
- If the product has a strong visual identity (e.g., pink for Hello Kitty), use that
- Ensure sufficient contrast ratios for accessibility
- Keep `--dark`, `--gray`, `--light-gray`, `--white` consistent for Apple-style neutrals

### Step 4: Content Strategy

Define the following content blocks based on research:

1. **Hero** — product name as `h1` (NOT a marketing slogan), eyebrow tagline, hero image, price with discount badge, primary + secondary CTA buttons
   - **Price format**: current price in bold + original price with strikethrough + discount badge (e.g., `$1.69 ~~$2.59~~ 34% OFF`)
   - **Hero image variants** (choose based on product):
     - **Direct image**: `<img>` with `border-radius: 24px` — best for products with strong packaging visuals (e.g., snack bags)
     - **Circular container**: product image inside a radial-gradient circle with `float` animation and `drop-shadow` — best for products that benefit from a lifestyle/premium feel (e.g., noodles, beverages)
2. **Pain Points** (4 cards, 2×2 grid) — real user frustrations this product solves
3. **Scenes** (4 cards, 2×2 grid) — specific usage scenarios with emotional context
4. **Craft / Stats** (4 big numbers) — impressive product stats (heritage, ratings, limits, quality)
5. **Atmosphere** (2×2 grid) — lifestyle/experience moments the product enables
6. **CTA** — final purchase call-to-action with urgency

7. **Ingredients / What's Inside** (optional) — real ingredient or material information sourced from the product page, brand website, or trusted retailer. This section is ONLY included when verifiable ingredient data is found.

**Ingredients section rules:**
- 使用 Playwright MCP 浏览器工具（`mcp_browser_navigate`、`mcp_browser_snapshot`、`mcp_browser_evaluate`）从产品页面提取真实成分/材料数据
- Acceptable sources: product page text, nutrition labels, brand official site, trusted retailers (Amazon, Walmart, etc.)
- If the product page only has ingredient info in images (common for Chinese products on Yami), extract what's available from product highlights, descriptions, and reviews
- NEVER fabricate or guess ingredients — if no verifiable data is found, omit this section entirely
- Present ingredients in a clean, scannable format (grid or list)
- For food products: list key ingredients, highlight allergens, note certifications (non-GMO, vegan, etc.)
- For non-food products: list key materials, active ingredients, or notable components
- Design: use a light background section with a simple grid or icon-based layout

Content rules:
- Write from the customer's perspective, not the brand's
- Use conversational, emotionally resonant copy
- Pain points should feel relatable ("Sound familiar?")
- Scenes should be specific moments, not generic benefits
- Stats should use Apple-style oversized numbers

### Step 5: Generate the HTML Page

Produce a single standalone HTML file with these characteristics:

**Design System (Apple-style):**
- Font: `Inter` (Google Fonts) + system font stack fallback
- Sticky frosted-glass navigation bar with `backdrop-filter: blur(20px)`
- Full-viewport hero section with oversized typography (`clamp()` responsive)
- Alternating light/dark full-width sections for storytelling rhythm
- 2×2 grid for pain points, scenes, and ingredients (single column on mobile)
- Large stat numbers with gradient text (`background-clip: text`)
- 2×2 atmosphere grid with colored cells
- Pill-shaped CTA buttons with `border-radius: 980px`
- Scroll-triggered fade-in animations via `IntersectionObserver`
- `prefers-reduced-motion` media query support

**SEO Requirements (mandatory — do not skip):**
- `<h1>` MUST be the product name (e.g., "Brand Product Name 365g"), NOT a marketing slogan. This is critical for search engine indexing.
<!-- - Include `<link rel="canonical">` pointing to the purchase URL -->
- Include Open Graph meta tags: `og:type` (product), `og:url`, `og:title`, `og:description`, `og:image`
- Include Product structured data (JSON-LD) with `@type: Product`, including `name`, `image`, `description`, `brand`, `offers` (price, currency, availability, seller), and `aggregateRating` if available
- `<title>` format: `{Product Name} | {Key Selling Point}`
- `<meta name="description">` should include brand, product type, key differentiator, rating, and platform name
- `<meta name="keywords">` should include product-relevant search terms
- Hero image `<img>` must have: `alt` with descriptive keyword-rich text, `width` and `height` attributes (actual pixel dimensions for SEO/CLS), `fetchpriority="high"`, and CSS `height: auto` to prevent distortion

**Technical Requirements:**
- Single `.html` file, no external dependencies except Google Fonts
- All CSS embedded in `<style>` tag
- All JS embedded in `<script>` tag (only for scroll animations)
- Responsive design (mobile-first breakpoints at 768px)
- Semantic HTML with proper heading hierarchy (`h1` = product name, `h2` = section headings, `h3` = card titles)
- ARIA labels on navigation and key sections
- Real product image(s) from Step 2
- All "Buy Now" / purchase links pointing to the user-provided URL, with `?utm_source=zhouzk.com` appended (use `&utm_source=zhouzk.com` if the URL already contains query parameters)

**File Naming & Output Directory:**
- Use kebab-case: `{product-name}.html`
- Save to `/SEOBlog/landing-pages/` directory (create if it doesn't exist)
- Full path example: `/SEOBlog/landing-pages/malawangzi-latiao-mala-extra-spicy.html`

### Step 6: Update Blog Index

After saving the landing page, update `/SEOBlog/index.html` to add an internal link:

1. Open `/SEOBlog/index.html`
2. Find the `<!-- LANDING-PAGE-LINKS:START -->` and `<!-- LANDING-PAGE-LINKS:END -->` comment markers
3. Add a new `<li>` entry inside the markers with:
   - Relative path: `./landing-pages/{file-name}` (no `.html` extension — Cloudflare Pages handles clean URLs)
   - Link text: the product name from the `<h1>` tag
4. Place the new entry at the top of the list (newest first)

Example:
```html
<li><a href="./landing-pages/product-name">Product Full Name</a></li>
```

### Step 7: Verify

After generating the file:
1. Run `getDiagnostics` on the HTML file to check for syntax issues
2. Confirm all purchase links point to the correct URL
3. Confirm the product image loads (real URL, not placeholder)
4. **SEO checklist** (must pass all):
   - [ ] `<h1>` contains the product name, not a marketing slogan
   <!-- - [ ] `<link rel="canonical">` is present and points to the purchase URL -->
   - [ ] `og:type`, `og:url`, `og:title`, `og:description`, `og:image` meta tags are present
   - [ ] Product JSON-LD structured data is present with `name`, `brand`, `offers`, and `aggregateRating`
   - [ ] Hero `<img>` has `width`, `height`, `alt`, and `fetchpriority="high"`
   - [ ] Heading hierarchy is correct: `h1` (product name) → `h2` (sections) → `h3` (cards)
   - [ ] All purchase links include `?utm_source=zhouzk.com` (or `&utm_source=zhouzk.com` if URL already has query params)

## Page Structure Reference

```
┌─────────────────────────────────────┐
│ <head>: title, meta desc, keywords, │
│ canonical, OG tags, JSON-LD Product │
├─────────────────────────────────────┤
│ Nav: Logo | Links | [Buy Now] CTA   │  ← sticky, frosted glass
├─────────────────────────────────────┤
│                                     │
│         HERO SECTION                │
│   Eyebrow · <h1>Product Name</h1>  │  ← h1 = product name, NEVER a slogan
│        [Product Image]              │  ← img with width/height/alt/fetchpriority
│   Price: $X.XX ~~$Y.YY~~ XX% OFF   │
│   [Buy Now]  [Learn More]           │
│                                     │
├─────────────────────────────────────┤
│                                     │
│    PAIN POINTS (dark bg)            │
│   2×2 grid, icon + title + text     │
│                                     │
├─────────────────────────────────────┤
│                                     │
│    SCENES (light/warm bg)           │
│   2×2 grid, emoji + title + text    │
│                                     │
├─────────────────────────────────────┤
│                                     │
│    CRAFT / STATS (white bg)         │
│   4 big gradient numbers in a row   │
│                                     │
├─────────────────────────────────────┤
│                                     │
│    INGREDIENTS (light-gray bg)      │  ← OPTIONAL: only if real data found
│   2×2 grid, icon + name + desc      │
│   + source disclaimer note          │
│                                     │
├─────────────────────────────────────┤
│                                     │
│    ATMOSPHERE (2×2 grid)            │
│   4 colored cells with emoji+text   │
│                                     │
├─────────────────────────────────────┤
│                                     │
│    CTA SECTION                      │
│   Heading + [Buy Now] + [View on X] │
│                                     │
├─────────────────────────────────────┤
│ Footer: © year + disclaimer + link  │
└─────────────────────────────────────┘
```

**Nav links** should correspond to each section's `id` attribute (e.g., `#pain`, `#scenes`, `#craft`, `#ingredients`, `#vibe`). Adjust link labels to match the product's tone.

## Example References

See the reference example in this skill's directory:
- `references/example-malawangzi-latiao.html` — complete working example with: direct hero image, ingredients section, OG tags, JSON-LD, proper h1, red brand palette. This file demonstrates all SKILL.md conventions.

## Important Notes

- Default language is English unless the user explicitly requests another language
- Always use real product images — never ship with emoji or gradient placeholders
- Purchase links must be real URLs, never `#` placeholders
- Color palette must reflect the actual brand, not a generic pink/blue
- 使用 Playwright MCP 浏览器工具访问产品页面来验证品牌颜色和产品详情，**绝对禁止**使用 web search 或 web fetch
- The page is for marketing demonstration — include a disclaimer in the footer. Template: `© {year} This page is for marketing demonstration purposes. Product details sourced from [Platform Name](URL). All trademarks belong to their respective owners.`
- **SEO is non-negotiable**: H1 = product name, structured data, canonical tag, OG tags, and image attributes must all be present in every generated page. Do not sacrifice SEO for visual design.

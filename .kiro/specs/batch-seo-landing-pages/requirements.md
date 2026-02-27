# 需求文档

## 简介

本文档定义了为 zhouzk.com 静态博客站点批量创建约 90 个 SEO 优化产品 Landing Page 的功能需求。每个页面覆盖 Yami.com 上的多品类商品（护肤、食品、饮料、家电等），遵循纯 HTML + 内联 CSS 的现有模板模式，包含完整的 SEO 元素和响应式设计。

## 术语表

- **Landing_Page**: 针对单个 Yami 产品的 SEO 优化静态 HTML 页面
- **产品数据预处理器 (Preprocessor)**: 将原始产品条目（item_number + product_name）转换为完整 ProductData 结构的处理模块
- **Slug_生成器 (Slug_Generator)**: 将产品名称转换为 URL 安全的 kebab-case 字符串的函数
- **品类分类器 (Category_Classifier)**: 根据产品名称关键词将产品分配到 9 个品类之一的算法
- **模板渲染引擎 (Template_Engine)**: 将产品数据和配色方案填充到标准 HTML 模板中生成完整页面的模块
- **配色方案 (Color_Scheme)**: 每个品类对应的 CSS 颜色变量集合（primary, primary_deep, primary_light, accent, cream）
- **品类 (Category)**: 产品分类枚举，包含 SKINCARE、BEAUTY_DEVICE、FOOD、NOODLES、BEVERAGE、APPLIANCE、HAIRCARE、HEALTH、SNACK 共 9 类
- **item_number**: Yami.com 商品唯一编号，纯数字字符串
- **UTM_链接**: 附加 `?utm_source=zhouzk.com` 追踪参数的 Yami 产品 URL
- **LANDING-PAGE-LINKS_区域**: index.html 中由 HTML 注释标记界定的链接插入区域
- **Schema_JSON-LD**: 嵌入在 HTML `<script type="application/ld+json">` 中的 Schema.org 结构化数据

## 需求

### 需求 1: 产品数据预处理

**用户故事:** 作为站点维护者，我希望将原始产品条目自动转换为完整的产品数据结构，以便后续模板渲染使用。

#### 验收标准

1. WHEN 输入包含 item_number 和 product_name 的产品条目时, THE 产品数据预处理器 SHALL 生成包含 item_number、product_name、brand、category、slug、yami_url、utm_url、filename 全部字段的 ProductData 结构
2. WHEN 产品名称包含已知品牌（如 Biore、COSRX、Zojirushi 等）时, THE 产品数据预处理器 SHALL 从产品名称中正确提取该品牌名
3. WHEN 产品名称不包含任何已知品牌时, THE 产品数据预处理器 SHALL 使用产品名称的第一个词作为品牌名
4. WHEN item_number 为空或非数字字符串时, THE 产品数据预处理器 SHALL 拒绝该条目并返回描述性错误信息
5. WHEN product_name 为空字符串时, THE 产品数据预处理器 SHALL 拒绝该条目并返回描述性错误信息

### 需求 2: Slug 生成

**用户故事:** 作为站点维护者，我希望产品名称能自动转换为 URL 安全的 slug，以便用于文件命名和 URL 构造。

#### 验收标准

1. THE Slug_生成器 SHALL 将产品名称转换为仅包含小写字母、数字和连字符 `[a-z0-9-]` 的字符串
2. WHEN 产品名称包含特殊字符（如 `【】*#+` 等）时, THE Slug_生成器 SHALL 移除这些字符并保留语义信息
3. WHEN 产品名称包含连续空格或特殊字符时, THE Slug_生成器 SHALL 将其替换为单个连字符
4. THE Slug_生成器 SHALL 生成不以连字符开头或结尾的 slug
5. WHEN 生成的 slug 长度超过 80 个字符时, THE Slug_生成器 SHALL 将其截断至 80 个字符并确保不以连字符结尾
6. WHEN 输入为非空字符串时, THE Slug_生成器 SHALL 返回非空的 slug 结果

### 需求 3: 产品品类分类

**用户故事:** 作为站点维护者，我希望产品能自动分类到正确的品类，以便应用对应的配色方案和内容模板。

#### 验收标准

1. WHEN 产品名称包含护肤相关关键词（如 SUNSCREEN、SPF、SERUM、CREAM、MASK 等）时, THE 品类分类器 SHALL 将该产品分类为 SKINCARE
2. WHEN 产品名称包含美容仪器关键词（如 BEAUTY DEVICE、AGE-R、RF BEAUTY 等）时, THE 品类分类器 SHALL 将该产品分类为 BEAUTY_DEVICE
3. WHEN 产品名称包含洗护发关键词（如 SHAMPOO、CONDITIONER、HAIR MASK 等）时, THE 品类分类器 SHALL 将该产品分类为 HAIRCARE
4. WHEN 产品名称包含方便面关键词（如 RAMEN、NOODLES、BULDAK 等）时, THE 品类分类器 SHALL 将该产品分类为 NOODLES
5. WHEN 产品名称包含饮料关键词（如 TEA、DRINK、MILK、SODA 等）时, THE 品类分类器 SHALL 将该产品分类为 BEVERAGE
6. WHEN 产品名称包含家电关键词（如 RICE COOKER、SOY MILK MAKER、KETTLE 等）时, THE 品类分类器 SHALL 将该产品分类为 APPLIANCE
7. WHEN 产品名称包含健康用品关键词（如 TOOTHPASTE、CONDOM 等）时, THE 品类分类器 SHALL 将该产品分类为 HEALTH
8. WHEN 产品名称包含零食关键词（如 CAKE、CHIPS、CANDY、CHOCOLATE 等）时, THE 品类分类器 SHALL 将该产品分类为 SNACK
9. WHEN 产品名称不匹配任何特定品类关键词时, THE 品类分类器 SHALL 将该产品默认分类为 FOOD
10. THE 品类分类器 SHALL 对产品名称执行大小写不敏感的关键词匹配

### 需求 4: URL 构造

**用户故事:** 作为站点维护者，我希望自动生成正确的 Yami 产品链接和 UTM 追踪链接，以便用户能直接跳转购买。

#### 验收标准

1. THE URL_构造器 SHALL 按照 `https://www.yami.com/en/p/{product-slug}/{item_number}` 格式生成产品基础 URL
2. THE URL_构造器 SHALL 在基础 URL 后追加 `?utm_source=zhouzk.com` 生成 UTM 追踪链接
3. WHEN 生成 UTM 链接时, THE URL_构造器 SHALL 确保所有链接以 `https://www.yami.com` 开头

### 需求 5: 品类配色方案

**用户故事:** 作为站点维护者，我希望不同品类的产品页面使用差异化的配色方案，以便提供视觉区分和品类辨识度。

#### 验收标准

1. THE 模板渲染引擎 SHALL 为每个品类提供包含 primary、primary_deep、primary_light、accent、cream 五个 CSS 颜色变量的配色方案
2. WHEN 渲染 Landing_Page 时, THE 模板渲染引擎 SHALL 将品类对应的配色方案写入 CSS `:root` 变量
3. THE 模板渲染引擎 SHALL 为全部 9 个品类（SKINCARE、BEAUTY_DEVICE、FOOD、NOODLES、BEVERAGE、APPLIANCE、HAIRCARE、HEALTH、SNACK）各定义一套独立的配色方案

### 需求 6: HTML 页面生成

**用户故事:** 作为站点维护者，我希望每个产品自动生成结构完整的 HTML Landing Page，以便保持全站一致的页面质量。

#### 验收标准

1. THE 模板渲染引擎 SHALL 生成包含全部 10 个标准 section 的 HTML 页面：Nav、Hero、Pain Points、Scenes、Stats、Details、Atmosphere、CTA、Footer、FadeInScript
2. THE 模板渲染引擎 SHALL 将所有 CSS 样式以内联方式写入 `<style>` 标签，不生成独立的 CSS 文件
3. THE 模板渲染引擎 SHALL 使用 vanilla JavaScript 实现 IntersectionObserver 淡入动画，代码包裹在 DOMContentLoaded 事件中
4. THE 模板渲染引擎 SHALL 生成的 HTML 使用 `lang="en"` 属性（产品内容为英文）
5. THE 模板渲染引擎 SHALL 生成的导航栏使用 glassmorphism 风格（固定定位 + 背景模糊）

### 需求 7: SEO 元标签

**用户故事:** 作为站点维护者，我希望每个页面包含完整的 SEO 元标签，以便搜索引擎正确索引和展示页面。

#### 验收标准

1. THE 模板渲染引擎 SHALL 为每个 Landing_Page 生成包含产品名称的 `<title>` 标签
2. THE 模板渲染引擎 SHALL 为每个 Landing_Page 生成长度在 120 至 160 字符之间的 `<meta name="description">` 标签
3. THE 模板渲染引擎 SHALL 为每个 Landing_Page 生成包含产品相关关键词的 `<meta name="keywords">` 标签
4. THE 模板渲染引擎 SHALL 为每个 Landing_Page 生成完整的 Open Graph 标签集合：og:type（值为 "product"）、og:url、og:title、og:description、og:image
5. THE 模板渲染引擎 SHALL 确保 og:url 指向有效的 Yami 产品链接

### 需求 8: Schema.org 结构化数据

**用户故事:** 作为站点维护者，我希望每个页面包含有效的 Schema.org 结构化数据，以便在搜索结果中获得富媒体展示。

#### 验收标准

1. THE 模板渲染引擎 SHALL 为每个 Landing_Page 生成 `<script type="application/ld+json">` 格式的 Schema.org JSON-LD 数据
2. THE 模板渲染引擎 SHALL 在 JSON-LD 中设置 `@type` 为 "Product"，并包含 name、brand、offers 字段
3. THE 模板渲染引擎 SHALL 确保 JSON-LD 中 offers.url 与页面的 og:url 值一致
4. THE 模板渲染引擎 SHALL 生成可被 JSON.parse 正确解析的 JSON-LD 数据
5. THE 模板渲染引擎 SHALL 在 JSON-LD 中不包含价格信息，以避免过时数据误导用户

### 需求 9: 文件命名

**用户故事:** 作为站点维护者，我希望生成的文件遵循统一的命名规范，以便保持目录结构整洁和可维护。

#### 验收标准

1. THE 模板渲染引擎 SHALL 按照 `{brand}-{product-name}*.html` 模式生成文件名
2. THE 模板渲染引擎 SHALL 确保文件名仅包含 `[a-z0-9-.]` 字符（kebab-case + .html 后缀）
3. WHEN 两个不同产品生成相同文件名时, THE 模板渲染引擎 SHALL 在文件名末尾追加 item_number 后 4 位作为区分
4. THE 模板渲染引擎 SHALL 将所有生成的 HTML 文件写入 `landing-pages/` 目录

### 需求 10: 批量生成与注册

**用户故事:** 作为站点维护者，我希望批量生成所有产品页面并自动注册到站点索引中，以便一次性完成全部页面的上线。

#### 验收标准

1. WHEN 提供包含 90+ 产品条目的清单时, THE 批量生成器 SHALL 为每个产品恰好生成一个 Landing_Page，无遗漏无重复
2. WHEN 批量生成完成后, THE 批量生成器 SHALL 将所有新页面链接插入 index.html 的 LANDING-PAGE-LINKS_区域
3. WHEN 批量生成完成后, THE 批量生成器 SHALL 将所有新页面 URL 添加到 sitemap.xml
4. THE 批量生成器 SHALL 在更新 index.html 时保留 LANDING-PAGE-LINKS_区域中的现有链接
5. WHEN 产品清单中存在重复的 item_number 时, THE 批量生成器 SHALL 拒绝处理并报告重复条目

### 需求 11: 响应式设计

**用户故事:** 作为站点维护者，我希望所有生成的页面在不同设备上都能正确显示，以便覆盖移动端和桌面端用户。

#### 验收标准

1. THE 模板渲染引擎 SHALL 在生成的 CSS 中包含 768px 和 480px 两个响应式断点的 media query
2. THE 模板渲染引擎 SHALL 生成包含 `<meta name="viewport" content="width=device-width, initial-scale=1.0">` 的 viewport 标签
3. THE 模板渲染引擎 SHALL 确保页面在 320px（手机）、768px（平板）、1024px+（桌面）三种宽度下布局合理

### 需求 12: 错误处理

**用户故事:** 作为站点维护者，我希望生成过程能妥善处理异常情况，以便在数据不完整时仍能获得有用的反馈。

#### 验收标准

1. IF 产品名称不包含已知品牌且首词不适合作为品牌名, THEN THE 产品数据预处理器 SHALL 使用 "Generic" 作为品牌名并在文件名中省略品牌前缀
2. IF 产品名称包含特殊字符（如 `【】*#+`）, THEN THE Slug_生成器 SHALL 移除这些字符并生成有效的 slug
3. IF 自动生成的 Yami URL slug 可能与实际不一致, THEN THE URL_构造器 SHALL 提供备用 URL 格式 `https://www.yami.com/en/search?q={item_number}` 作为 fallback
4. IF 品类分类出现模糊匹配（如 "Matcha Powder" 可能属于饮料或食品）, THEN THE 品类分类器 SHALL 优先匹配更具体的关键词，模糊情况默认为 FOOD

### 需求 13: 安全与合规

**用户故事:** 作为站点维护者，我希望所有生成的页面遵循安全最佳实践，以便保护用户和站点声誉。

#### 验收标准

1. THE 模板渲染引擎 SHALL 确保所有外部资源链接使用 HTTPS 协议
2. THE 模板渲染引擎 SHALL 在 Footer 中声明页面为营销演示用途，产品详情来源于 Yami
3. THE 模板渲染引擎 SHALL 确保 UTM 参数不包含任何用户敏感信息
4. THE 模板渲染引擎 SHALL 在 Schema.org JSON-LD 中不包含具体价格数据

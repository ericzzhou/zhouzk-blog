# 需求文档

## 简介

本功能为 zhouzk.com 静态博客站点的 SEO Landing Page Generator Skill 新增 sitemap.xml 和 robots.txt 自动生成能力。通过在 Skill 工作流程中新增 Step 7（生成 sitemap）和 Step 8（确保 robots.txt），实现每次创建 landing page 后自动扫描项目中所有活跃页面，生成符合 Sitemaps 0.9 协议的 XML 文件，配合 Cloudflare Pages 自动部署，使搜索引擎能够自动发现所有页面。

## 术语表

- **Page_Scanner**: 页面扫描器，负责扫描项目目录并收集所有需要纳入 sitemap 的页面路径
- **Sitemap_Generator**: Sitemap 生成器，根据页面列表生成符合 Sitemaps 0.9 协议的 XML 文件
- **Robots_Generator**: Robots.txt 生成器，负责创建或更新 robots.txt 文件以包含 sitemap 声明
- **PageInfo**: 页面信息数据结构，包含 relativePath、url、lastModified、priority、changeFreq 字段
- **Clean_URL**: Cloudflare Pages 提供的无 `.html` 后缀的 URL 格式
- **Skill_Workflow**: AI Skill 工作流程，包含从信息收集到页面生成的完整步骤序列
- **Active_Page**: 活跃页面，指非 backup、非测试、非中间修复文件的 HTML 页面

## 需求

### 需求 1: 页面扫描

**用户故事:** 作为站点管理员，我希望系统能自动扫描项目中所有活跃页面，以便准确收集需要纳入 sitemap 的页面列表。

#### 验收标准

1. WHEN Skill_Workflow 执行到 Step 7, THE Page_Scanner SHALL 扫描 `index.html`、`article/*.html` 和 `landing-pages/*.html` 三个位置的 HTML 文件
2. THE Page_Scanner SHALL 排除 `landing-pages/backup/` 目录下的所有文件
3. THE Page_Scanner SHALL 排除文件名以 `test-` 开头的 HTML 文件
4. THE Page_Scanner SHALL 排除 `landing-pages/kikkoman-fixed.html` 文件
5. WHEN 扫描到一个有效的 HTML 文件, THE Page_Scanner SHALL 为该文件生成一个包含 relativePath、url、lastModified、priority 和 changeFreq 的 PageInfo 记录

### 需求 2: URL 映射

**用户故事:** 作为站点管理员，我希望生成的 URL 符合 Cloudflare Pages 的 Clean URL 格式，以便与实际访问路径一致。

#### 验收标准

1. WHEN 处理 `index.html` 文件, THE Page_Scanner SHALL 将其映射为 `https://zhouzk.com/`
2. WHEN 处理 `article/` 目录下的 HTML 文件, THE Page_Scanner SHALL 将其映射为 `https://zhouzk.com/article/{name}` 格式，其中 `{name}` 为去除 `.html` 后缀的文件名
3. WHEN 处理 `landing-pages/` 目录下的 HTML 文件, THE Page_Scanner SHALL 将其映射为 `https://zhouzk.com/landing-pages/{name}` 格式，其中 `{name}` 为去除 `.html` 后缀的文件名
4. THE Page_Scanner SHALL 确保所有生成的 URL 使用 `https://` 协议
5. THE Page_Scanner SHALL 确保除根路径外的所有 URL 不包含 `.html` 后缀

### 需求 3: 页面优先级与更新频率

**用户故事:** 作为站点管理员，我希望 sitemap 中的页面具有合理的优先级和更新频率标注，以便搜索引擎合理分配抓取资源。

#### 验收标准

1. WHEN 处理 `index.html`, THE Page_Scanner SHALL 设置 priority 为 1.0，changeFreq 为 "weekly"
2. WHEN 处理 `article/` 目录下的文件, THE Page_Scanner SHALL 设置 priority 为 0.7，changeFreq 为 "monthly"
3. WHEN 处理 `landing-pages/` 目录下的文件, THE Page_Scanner SHALL 设置 priority 为 0.8，changeFreq 为 "monthly"
4. WHEN 生成 PageInfo 记录, THE Page_Scanner SHALL 读取文件的最后修改时间作为 lastModified 值

### 需求 4: Sitemap XML 生成

**用户故事:** 作为站点管理员，我希望系统生成符合 Sitemaps 0.9 协议的 XML 文件，以便搜索引擎能正确解析。

#### 验收标准

1. THE Sitemap_Generator SHALL 生成包含 XML 声明 `<?xml version="1.0" encoding="UTF-8"?>` 的文件
2. THE Sitemap_Generator SHALL 使用 `http://www.sitemaps.org/schemas/sitemap/0.9` 作为 urlset 的 xmlns 命名空间
3. WHEN 生成 sitemap XML, THE Sitemap_Generator SHALL 为每个 PageInfo 记录生成一个 `<url>` 元素，包含 `<loc>`、`<lastmod>`、`<changefreq>` 和 `<priority>` 子元素
4. THE Sitemap_Generator SHALL 将 lastmod 日期格式化为 `YYYY-MM-DD` 格式
5. THE Sitemap_Generator SHALL 按 priority 降序排列 URL 条目，同优先级按 URL 字母升序排列
6. THE Sitemap_Generator SHALL 将生成的文件写入项目根目录的 `sitemap.xml`

### 需求 5: Robots.txt 管理

**用户故事:** 作为站点管理员，我希望系统自动管理 robots.txt 文件并声明 sitemap 位置，以便搜索引擎能自动发现 sitemap。

#### 验收标准

1. WHEN `robots.txt` 文件不存在, THE Robots_Generator SHALL 创建包含 `User-agent: *`、`Allow: /` 和 `Sitemap: https://zhouzk.com/sitemap.xml` 的 robots.txt 文件
2. WHEN `robots.txt` 文件已存在但不包含 `Sitemap:` 声明, THE Robots_Generator SHALL 在文件末尾追加 `Sitemap: https://zhouzk.com/sitemap.xml`
3. WHEN `robots.txt` 文件已存在且包含 `Sitemap:` 声明, THE Robots_Generator SHALL 保持文件不变

### 需求 6: Skill 工作流程集成

**用户故事:** 作为站点管理员，我希望 sitemap 生成自动集成到现有的 Landing Page 创建流程中，以便无需额外手动操作。

#### 验收标准

1. WHEN Step 6（更新 index.html）完成后, THE Skill_Workflow SHALL 自动执行 Step 7（生成 sitemap.xml）
2. WHEN Step 7 完成后, THE Skill_Workflow SHALL 自动执行 Step 8（确保 robots.txt）
3. WHEN sitemap.xml 生成成功, THE Skill_Workflow SHALL 显示包含页面数量的确认信息
4. THE Skill_Workflow SHALL 在不引入任何外部依赖（npm 包、构建工具、外部脚本）的前提下完成 sitemap 生成

### 需求 7: 错误处理

**用户故事:** 作为站点管理员，我希望系统在遇到异常情况时能优雅处理，以便不影响 landing page 的正常创建流程。

#### 验收标准

1. IF 扫描过程中某个文件无法读取, THEN THE Page_Scanner SHALL 跳过该文件并记录警告信息，继续处理其余文件
2. IF sitemap.xml 写入失败, THEN THE Skill_Workflow SHALL 报告错误但不中断整个工作流程
3. IF 扫描完成后 pageList 为空, THEN THE Sitemap_Generator SHALL 不生成 sitemap.xml 并显示警告信息
4. IF 项目根目录已存在非本流程生成的 sitemap.xml, THEN THE Sitemap_Generator SHALL 完全覆盖重写该文件

### 需求 8: 输出验证

**用户故事:** 作为站点管理员，我希望系统在生成后自动验证输出文件的正确性，以便及时发现问题。

#### 验收标准

1. WHEN sitemap.xml 生成完成, THE Skill_Workflow SHALL 验证该文件是合法的 XML 文档（well-formed）
2. WHEN sitemap.xml 生成完成, THE Skill_Workflow SHALL 验证所有 URL 使用 `https://` 协议
3. WHEN sitemap.xml 生成完成, THE Skill_Workflow SHALL 验证除根路径外的所有 URL 不包含 `.html` 后缀
4. WHEN robots.txt 生成或更新完成, THE Skill_Workflow SHALL 验证文件包含 `Sitemap: https://zhouzk.com/sitemap.xml` 声明

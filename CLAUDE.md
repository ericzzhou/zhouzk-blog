# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 快速开始

这是一个**纯静态 HTML 网站**，无构建工具，直接编辑文件即可。

```bash
# 本地预览
python3 -m http.server 8000
```

## 核心原则

- **所有代码内联**：CSS/JS 直接写在 HTML 中，禁止独立文件
- **Apple 风格设计**：毛玻璃导航、药丸按钮、Inter 字体
- **SEO 优先**：每个着陆页必须有 Schema.org JSON-LD、Open Graph、产品名称作 H1

## 添加新着陆页

1. 使用 `.kiro/skills/seo-landing-page-generator/SKILL.md` 生成页面
2. 更新 `index.html` 中的 `LANDING-PAGE-LINKS` 区块
3. 更新 `sitemap.xml`
4. 验证：无控制台错误、响应式正常、Schema 通过 Google 测试

## 详细规范

参见 [**AGENTS.md**](AGENTS.md) 获取完整信息：
- 编码规范（缩进、命名、断点）
- 完整的 SEO 要求
- CSS 变量标准
- 测试清单
- 代码片段示例

## 文件速查

| 任务 | 位置 |
|------|------|
| 新建着陆页 | `.kiro/skills/seo-landing-page-generator/SKILL.md` |
| 着陆页模板 | `landing-pages/backup/template-kikkoman.html` |
| 更新主页索引 | `index.html` 中 `LANDING-PAGE-LINKS` 注释区间 |
| 更新站点地图 | `sitemap.xml` |

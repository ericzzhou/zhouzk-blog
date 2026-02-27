# 实施计划: Sitemap 自动生成

## 概述

修改 `.opencode/skills/seo-landing-page-generator/SKILL.md` 文件，在现有 Step 6（更新 index.html）之后新增 Step 7（生成 sitemap.xml）和 Step 8（确保 robots.txt），并将原 Step 7（Verify）重新编号为 Step 9 并增加 sitemap 相关验证项。所有变更均为 Markdown 文档编辑，不涉及代码文件创建。

## Tasks

- [x] 1. 在 SKILL.md 中新增 Step 7: 生成/更新 sitemap.xml
  - [x] 1.1 在 Step 6 (Update Blog Index) 之后插入 `### Step 7: Generate / Update Sitemap`
    - 编写页面扫描规则说明：扫描 `index.html`、`article/*.html`、`landing-pages/*.html`
    - 编写排除规则：排除 `landing-pages/backup/` 目录、`test-*.html` 文件、`kikkoman-fixed.html`
    - 编写 URL 映射规则：使用 `https://zhouzk.com` 域名，Clean URL 格式（无 `.html` 后缀）
    - 编写优先级与更新频率规则：index.html → priority 1.0 / weekly；article/ → 0.7 / monthly；landing-pages/ → 0.8 / monthly
    - 编写 lastmod 规则：读取文件最后修改时间，格式化为 `YYYY-MM-DD`
    - 编写排序规则：按 priority 降序，同优先级按 URL 字母升序
    - 提供 sitemap.xml 输出格式示例（包含 XML 声明、urlset 命名空间、url 元素结构）
    - 编写错误处理说明：文件读取失败时跳过并警告；pageList 为空时不生成文件；已存在 sitemap.xml 时完全覆盖
    - 编写生成后确认信息输出要求：显示包含页面数量的确认消息
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3, 3.4, 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 6.1, 6.4, 7.1, 7.2, 7.3, 7.4_

- [x] 2. 在 SKILL.md 中新增 Step 8: 确保 robots.txt 存在
  - [x] 2.1 在 Step 7 之后插入 `### Step 8: Ensure robots.txt`
    - 编写三种场景处理逻辑：文件不存在时创建默认内容；文件存在但无 Sitemap 声明时追加；文件已包含 Sitemap 声明时不修改
    - 提供 robots.txt 默认内容模板（`User-agent: *`、`Allow: /`、`Sitemap: https://zhouzk.com/sitemap.xml`）
    - _Requirements: 5.1, 5.2, 5.3, 6.2_

- [x] 3. 将原 Step 7 (Verify) 重新编号为 Step 9 并增加 sitemap 验证项
  - [x] 3.1 将 `### Step 7: Verify` 改为 `### Step 9: Verify`
    - 保留所有原有验证项不变
    - _Requirements: 6.3_
  - [x] 3.2 在验证清单中新增 sitemap 和 robots.txt 相关检查项
    - 新增：`sitemap.xml` 存在于项目根目录
    - 新增：`sitemap.xml` 是合法 XML（well-formed）
    - 新增：`sitemap.xml` 包含刚创建的新页面 URL
    - 新增：所有 URL 使用 `https://` 协议
    - 新增：所有 URL 不包含 `.html` 后缀
    - 新增：`robots.txt` 存在于项目根目录
    - 新增：`robots.txt` 包含 `Sitemap: https://zhouzk.com/sitemap.xml` 声明
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

- [x] 4. Checkpoint - 确认所有变更正确
  - 确保所有修改完成后 SKILL.md 步骤编号连续（Step 1-9）
  - 确保新增步骤的格式与现有步骤风格一致
  - 确保所有 URL 使用 `https://zhouzk.com` 域名
  - 如有疑问请询问用户

## Notes

- 所有任务均为修改 `.opencode/skills/seo-landing-page-generator/SKILL.md` 这一个 Markdown 文件
- 不涉及创建任何代码文件、构建工具或外部依赖
- 新增步骤的写作风格应与现有 SKILL.md 步骤保持一致（英文技术文档风格）
- 每个 task 引用了具体的 requirements 子条款以确保可追溯性

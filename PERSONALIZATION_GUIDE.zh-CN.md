# Navfolio 个性化页面改造指南

本文基于当前仓库的实际结构编写，目标是把这个 Navfolio starter 替换成自己的个人网站。日常个性化优先修改配置、内容和图片；只有要改变页面结构或视觉规则时，才修改 Astro 组件。

## 结论：先改哪些文件

| 优先级 | 文件或目录                                                  | 用途                                                           | 是否需要改代码                       |
| ------ | ----------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------ |
| P0     | `src/config/site.toml`                                      | 网站名称、个人资料、首页文案、导航、社交链接、主题、搜索与评论 | 否                                   |
| P0     | `src/content/about.mdx`                                     | About 页面正文                                                 | 否，当前工作区尚未提供该文件，需新建 |
| P0     | `src/content/blog/`                                         | 博客文章                                                       | 否，按 Markdown/MDX 新增             |
| P1     | `src/content/projects/`                                     | 项目作品和案例                                                 | 否，按 Markdown/MDX 新增             |
| P1     | `src/content/vibe/`、`src/content/media/`                   | 短动态、书影音记录（可选）                                     | 否，按 Markdown/MDX 新增             |
| P1     | `public/images/`                                            | 头像、Logo、站点展示图等静态图片                               | 否                                   |
| P1     | `public/favicon.*`、`public/*icon*`、`public/*.webmanifest` | 浏览器图标与 PWA 名称                                          | 否                                   |
| P2     | `src/pages/index.astro` 和 `src/components/cards/`          | 首页模块的排列与卡片样式                                       | 是                                   |
| P2     | `src/components/blog/BlogTopNav.astro`                      | 顶部导航的交互与外观                                           | 是                                   |
| P2     | `src/styles/global.css`                                     | 站点级额外样式                                                 | 是                                   |

> 不要把个人资料硬编码进组件。当前首页已经从 `src/config/site.toml` 读取数据，改 TOML 就能更新绝大多数内容。

## 1. 最重要的配置文件：`src/config/site.toml`

这是个人站的“控制台”。建议首次改造时，按以下顺序完成。

### 1.1 站点与 SEO 基础信息

修改 `[config.site]`：

```toml
[config.site]
title = "你的名字 / 品牌名"
description = "一句清晰的网站说明"
pageTitle = "你的名字｜个人主页"
pageDescription = "用于搜索引擎和社交分享的页面描述。"
url = "https://example.com"
repository = "https://github.com/your-account/your-repository"
footerNote = "© 2026 你的名字"
```

- `url` 应填写最终公开域名，包含 `https://`；它会影响 sitemap、RSS 和分享链接。
- `repository` 会被顶部导航的 GitHub 图标使用。若不想展示仓库，需要改 `src/components/blog/BlogTopNav.astro`，而不是填写示例仓库。
- `description` 和 `pageDescription` 不应继续保留 starter 的介绍文字。

### 1.2 主题、语言和字体

修改 `[config.theme]` 与 `[config.fonts]`：

```toml
[config.theme]
palette = "blue-soft" # 可选：green-soft、green-vivid、rose-soft、pink-soft、purple-soft、blue-soft、orange-soft、brown-soft
lang = "zh-CN"        # 可选：en、zh-CN、zh-TW

[config.fonts]
en = "Maple Mono"
code = "Monaco"
zh = "ChillRoundM"
file = "/fonts/ChillRoundM.ttf"
```

现有构建会按页面字符生成 UI 字体子集；替换 `file` 指向的中文字体后，应运行一次 `bun run build` 验证。字体文件应放在 `public/fonts/`，路径以 `/fonts/` 开头。

### 1.3 个人身份、头像和联系方式

修改 `[config.profile]`：

```toml
[config.profile]
name = "王小明"
handle = "@xiaoming"
role = "Product designer & front-end developer"
email = "hello@example.com"
website = "https://example.com"
github = "https://github.com/your-account"
avatar = "/images/avatar.webp"
```

将新头像放到 `public/images/avatar.webp`。`ProfileCard.astro` 会直接显示该路径；建议提供正方形、至少 352×352 px 的 WebP/PNG/JPEG 图片。

### 1.4 顶部导航和首页入口

顶部导航由 `[[config.topNav.links]]` 控制。保留 `module = "projects"`、`"vibe"`、`"media"` 时，路由会随模块配置自动解析；普通页面使用 `href`。

```toml
[[config.topNav.links]]
label = "首页"
href = "/"

[[config.topNav.links]]
label = "文章"
href = "/blog"

[[config.topNav.links]]
label = "作品"
module = "projects"
```

首页四张入口卡片由 `[[config.home.navigation]]` 控制。这里可以放站内路径（如 `/projects`）或外部链接。`icon` 必须是 `src/components/Icon.astro` 已注册的名称，例如 `compass`、`pen`、`briefcase`、`github`。

### 1.5 首页文案、动态和社交链接

- `[config.home.quote]`：首页引言；`text` 是字符串数组。
- `[config.home.intro]`：标题与自我介绍；`name` 应与标题中要强调的名字一致。
- `[config.home.latest]`：最新文章数量和写作热力图；没有博客内容时可先将 `showHeatmapLatest = false`。
- `[[config.home.doing]]`：近期在做的事情；可删减为真实的 3–5 条。
- `[[config.home.links]]`：社交入口。`tooltip` 为空的项目会隐藏；`copy = true` 适用于邮箱、微信号等需要复制的内容。

建议删除不使用的平台条目，而不是保留模板中的 QQ、微博、小红书等示例账号。

### 1.6 写作、搜索和评论

- `[config.pages.*]`：控制 Blog、Projects、Vibe、Media 页面标题和说明。
- `[config.search]`：静态全文搜索。生产构建后由 Pagefind 生成索引；开发服务器没有索引时是正常现象。
- `[config.comments]`：默认关闭。启用 Giscus、Utterances 或 Waline 前，先在相应平台完成配置；不要把访问密钥写进仓库。

## 2. 内容应该写到哪里

当前工作区的 `src/content/` 目录没有初始化，而 `src/pages/about.astro` 明确要求存在 `src/content/about.mdx`（或 `.md`）。因此，创建个人内容目录是让网站可作为个人站运行的首要改进项。

推荐的目录结构：

```text
src/content/
├── about.mdx
├── blog/
│   └── first-post.md
├── projects/
│   └── portfolio-site.md
├── vibe/
│   └── 2026-08-08.md
└── media/
    └── a-favorite-book.md
```

可使用项目已有脚本生成带正确 frontmatter 的文件：

```powershell
bun run post:new first-post
bun run project:new portfolio-site
bun run vibe:new 2026-08-08
bun run media:new a-favorite-book
```

About 页面需要手动创建。最小可用示例：

```md
---
title: '关于我'
description: '个人简介与联系方式。'
date: '2026-08-08'
draft: false
showHeroImage: false
tags: []
categories: []
series: []
comments: false
sidebar:
  enable: false
---

你好，我是……

这里可以写经历、擅长方向、合作方式，以及联系信息。
```

文章、项目和 About 共用 `title`、`description`、`date`、`draft`、`heroImage`、`tags` 等基础字段。完整校验规则在 `src/content.config.ts`；当构建报 frontmatter 错误时，应以这个 schema 为准。

## 3. 图片、Logo 和浏览器图标

### 只换图片，不改界面

将图片置于 `public/images/`，并在 TOML 或 Markdown 中使用 `/images/文件名.ext`。例如头像的 `avatar = "/images/avatar.webp"`。

内容文章的本地图可放在文章所在内容目录或 `src/assets/`，再在 frontmatter 里填写 `heroImage`。远程图只允许使用 `http://` 或 `https://`。

### 需要同步替换的品牌资源

除了头像和 Logo，也应替换以下模板残留资源：

- `public/favicon.svg`、`public/favicon.ico`、`public/favicon-*.png`
- `public/apple-*.png`、`public/android-*.png`、`public/ms-icon-*.png`
- `public/web-app-manifest-192x192.png`、`public/web-app-manifest-512x512.png`
- `public/manifest.json` 和 `public/site.webmanifest`

特别注意：当前 `public/site.webmanifest` 仍使用 `MyWebSite` / `MySite` 占位名称，应该改成自己的站点名；`public/manifest.json` 的名称、描述和图标也应与 `[config.site]` 保持一致。

## 4. 哪些情况才需要修改组件

| 想改什么                                   | 首选文件                                                      | 说明                                                                                        |
| ------------------------------------------ | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| 首页模块顺序、隐藏某个模块、增加自定义区块 | `src/pages/index.astro`                                       | 首页通过 `DashboardFlow` 组合 Profile、Intro、Latest、Navigation、Connect、Doing 六个插槽。 |
| 头像卡、个人身份卡的结构或外观             | `src/components/cards/ProfileCard.astro`                      | 资料内容仍来自 TOML。                                                                       |
| 自我介绍和引言的展示样式                   | `src/components/cards/IntroCard.astro`                        | 适合改排版，不适合存放个人文案。                                                            |
| 首页快捷入口卡                             | `src/components/cards/NavigationCard.astro`                   | 图标和文字通常只需改 TOML。                                                                 |
| 社交按钮、复制行为                         | `src/components/cards/ConnectCard.astro`、`ConnectIcon.astro` | 要添加新的图标名称时，还需改 `src/components/Icon.astro`。                                  |
| 顶部导航、移动端菜单、主题切换             | `src/components/blog/BlogTopNav.astro`                        | 导航项目本身仍应在 TOML 中维护。                                                            |
| 全局追加样式                               | `src/styles/global.css`                                       | 优先使用主题 CSS 变量，避免覆盖大量组件选择器。                                             |
| 新增独立页面                               | `src/pages/`                                                  | 文件即路由；例如 `src/pages/resume.astro` 会生成 `/resume`。                                |

`navfolio.config.ts` 用来注册 Projects、Vibe、Media 与 Markdown 能力；不要为了修改文案或颜色而编辑它。`src/content.config.ts` 是内容 schema，只有需要新增 frontmatter 字段或内容类型时才改。`src/docs/` 是 Git submodule 的文档站内容，不是个人站内容目录。

## 5. 建议的项目改进项

以下建议按当前仓库的可见状态排序，并区分“立刻影响个人站上线”和“后续体验优化”。

### P0：补齐可构建的个人内容

建立 `src/content/about.mdx`，并至少新增一篇非草稿博客。当前 `/about` 路由会在找不到 About 内容时抛出错误；没有文章时首页的“最新文章”和热力图也缺乏展示价值。

### P1：完成品牌与对外信息替换

替换 `site.toml` 中的 starter 域名、仓库、邮箱、头像、社交账号与首页链接，并同步更新 favicon、两份 manifest 和 `browserconfig.xml` 的图标/颜色。这样浏览器标签、添加到桌面、RSS、sitemap、社交分享和导航入口才会是同一套品牌。

### P1：收紧公开面与隐私信息

首页链接会直接暴露邮箱、微信号或其他复制内容。建议只保留愿意公开的联系方式；若启用评论系统，使用平台要求的公开配置字段，敏感 token 一律放在部署环境变量中，不提交到仓库。

### P2：按真实使用需求精简模块

项目默认启用了 Projects、Vibe、Media。如果近期只写博客和作品集，可以从 `navfolio.config.ts` 移除暂不使用的模块，并从 `[config.topNav]` 删除相应入口；反之，保留模块时应为每个模块至少准备一条真实内容，避免空页面。

### P2：补充内容治理

为文章设定统一的分类、标签和系列规则，例如“技术 / 设计 / 随笔”三类，以及固定的文章摘要长度。项目已支持 categories、tags、series、草稿、相关推荐和 RSS；内容命名规则统一后，这些能力才会产生稳定的归档与关联效果。

### P2：添加发布前质量检查

首次安装依赖并配置字体工具后，将以下命令作为发布前检查：

```powershell
bun run format:check
bun run build
```

`bun run build` 会同时检查 Astro 构建、生成字体子集并创建 Pagefind 搜索索引。当前工作区没有 `node_modules/`，首次执行前需要 `bun install`；README 还说明字体子集构建依赖 Python 3、FontTools 与 Brotli。

### P3：以真实数据再做体验优化

有一段时间的内容后，再根据数据调整首页：文章不多时关闭热力图；文章较多时提高 `postsPerPage`、增加系列；图片较多时统一封面比例。避免在内容为空时过早进行复杂动效或重构主题，这会增加维护成本而不提升信息价值。

## 6. 推荐的首次改造流程

1. 修改 `src/config/site.toml`：站点、个人资料、导航、首页文案和社交链接。
2. 在 `public/images/` 添加头像和 Logo，并更新 `avatar` 路径。
3. 创建 `src/content/about.mdx`，再用脚本创建至少一篇文章和一个项目。
4. 替换 favicon 与 manifest 中的站点名称、描述、图标。
5. 根据是否真的使用 Projects、Vibe、Media 决定保留或关闭对应模块。
6. 安装依赖并运行 `bun run format:check`、`bun run build`。
7. 使用 `bun run preview` 检查移动端导航、深色模式、搜索、RSS 与分享信息。

完成以上步骤后，模板已经会呈现为可维护的个人页面；后续再按需要调整首页布局和组件样式即可。

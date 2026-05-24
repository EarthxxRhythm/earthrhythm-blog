# EarthRhythm 干净重启 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将现有 EarthRhythm Hugo 博客整理为一个可构建、可部署、品牌表达清晰的 Vercel 临时域名站点。

**Architecture:** 保留 Hugo + `hugo-theme-reimu`，把 Hugo 源码树作为唯一维护对象。修复缺失的主题子模块映射，重写站点外壳配置和非文章页面，忽略生成产物，并用文档固定本地构建与 Vercel 部署流程。

**Tech Stack:** Hugo Extended、hugo-theme-reimu、TOML、YAML、Markdown、Vercel。

---

## 文件结构

- 修改 `.gitmodules`：恢复 `themes/hugo-theme-reimu` 的子模块来源。
- 新建 `.gitignore`：忽略 Hugo 生成物、本地临时文件和视觉辅助草稿目录。
- 修改 `hugo.toml`：清理站点主配置、菜单、输出格式、Vercel 临时域名。
- 修改 `config/_default/params.yml`：清理主题参数、品牌文本、社交链接和未启用集成。
- 修改 `content/_index.md`：重写首页欢迎语，不触碰文章列表实现。
- 修改 `content/about/_index.md`：重写关于页，替换部署检查清单式内容。
- 新建 `README.md`：说明项目定位、本地开发、构建和目录结构。
- 修改 `VERCEL_DEPLOYMENT_GUIDE.md`：保留 Vercel 临时域名部署路径。
- 修改 `vercel.json`：固定 Hugo 构建命令、输出目录和安装命令。
- 从 Git 跟踪中移除 `public/`、`resources/_gen/`、`.hugo_build.lock`：保留工作区文件，但不再维护生成物。

## Task 1: 修复主题子模块来源

**Files:**
- Create: `.gitmodules`
- Verify: `themes/hugo-theme-reimu`

- [ ] **Step 1: 写入子模块映射**

Create `.gitmodules` with:

```ini
[submodule "themes/hugo-theme-reimu"]
	path = themes/hugo-theme-reimu
	url = https://github.com/D-Sketon/hugo-theme-reimu.git
```

- [ ] **Step 2: 初始化主题**

Run:

```powershell
git submodule sync --recursive
git submodule update --init --recursive
```

Expected: `themes/hugo-theme-reimu` contains theme files such as `layouts`, `assets`, and `theme.toml`.

- [ ] **Step 3: 验证主题目录不再为空**

Run:

```powershell
Get-ChildItem -Force themes/hugo-theme-reimu | Select-Object -First 10 Name
git submodule status
```

Expected: the first command lists theme files; the second command shows commit `60235f72...` or a checked-out `hugo-theme-reimu` commit.

- [ ] **Step 4: 提交子模块修复**

Run:

```powershell
git add .gitmodules themes/hugo-theme-reimu
git commit -m "Fix Hugo theme submodule mapping"
```

Expected: commit succeeds with `.gitmodules` added.

## Task 2: 清理生成物跟踪和忽略规则

**Files:**
- Create: `.gitignore`
- Remove from tracking: `public/`, `resources/_gen/`, `.hugo_build.lock`

- [ ] **Step 1: 写入忽略规则**

Create `.gitignore` with:

```gitignore
# Hugo generated output
/public/
/resources/_gen/
.hugo_build.lock

# Local development
.superpowers/
.DS_Store
Thumbs.db

# Editor settings
.vscode/
.idea/
```

- [ ] **Step 2: 从 Git 跟踪中移除生成物**

Run:

```powershell
git rm -r --cached public resources/_gen .hugo_build.lock
```

Expected: Git stages deletions for generated files only. Files may remain on disk because `--cached` removes them from tracking, not from the workspace.

- [ ] **Step 3: 验证文章没有被改动**

Run:

```powershell
git diff -- content/post
```

Expected: no output.

- [ ] **Step 4: 提交仓库清理**

Run:

```powershell
git add .gitignore
git commit -m "Ignore Hugo generated output"
```

Expected: commit removes tracked generated output and adds `.gitignore`.

## Task 3: 重写 Hugo 与主题配置

**Files:**
- Modify: `hugo.toml`
- Modify: `config/_default/params.yml`
- Modify: `vercel.json`

- [ ] **Step 1: 替换 `hugo.toml`**

Replace `hugo.toml` with:

```toml
baseURL = "https://earthrhythm.vercel.app/"
title = "EarthRhythm"
languageCode = "zh-CN"
defaultContentLanguage = "zh-CN"
theme = "hugo-theme-reimu"
hasCJKLanguage = true
enableRobotsTXT = true

[languages.zh-CN]
languageName = "简体中文"
weight = 1
hasCJKLanguage = true

[outputs]
home = ["HTML", "RSS", "JSON"]
page = ["HTML"]
section = ["HTML", "RSS"]
taxonomy = ["HTML", "RSS"]
term = ["HTML", "RSS"]

[pagination]
pagerSize = 10

[[menu.main]]
identifier = "home"
name = "首页"
url = "/"
weight = 1

[[menu.main]]
identifier = "archives"
name = "归档"
url = "/archives/"
weight = 2

[[menu.main]]
identifier = "tags"
name = "标签"
url = "/tags/"
weight = 3

[[menu.main]]
identifier = "categories"
name = "分类"
url = "/categories/"
weight = 4

[[menu.main]]
identifier = "about"
name = "关于"
url = "/about/"
weight = 5

[markup.goldmark.renderer]
unsafe = true

[markup.goldmark.extensions.passthrough]
enable = true
delimiters.block = [["\\[", "\\]"], ["$$", "$$"]]
delimiters.inline = [["\\(", "\\)"], ["$", "$"]]
```

- [ ] **Step 2: 替换 `config/_default/params.yml`**

Replace `config/_default/params.yml` with:

```yaml
title: "EarthRhythm"
subtitle: "在知识、技术与好奇心之间寻找节律"
description: "EarthRhythm 是一个记录技术实践、知识观察与个人思考的中文博客。"
keywords: ["EarthRhythm", "个人博客", "Hugo", "技术", "知识记录"]
author: "Earth"
avatar: "avatar.jpg"
favicon: "favicon.ico"
banner: "images/banner.jpg"

themeMode: "auto"

sidebar:
  position: left
  menu: true

social:
  github: "https://github.com/EarthxxRhythm"
  email: "mailto:1930808767@qq.com"
  steam: "https://steamcommunity.com/profiles/76561198841604139/"
  rss: false

home:
  postsPerPage: 10
  showDescription: true
  showTags: true
  showCategories: true

preloader:
  enable: true
  text:
    zh-CN: "加载中..."
    zh-TW: "載入中..."
    en: "Loading..."
    ja: "読み込み中..."
    pt-BR: "Carregando..."
  icon:
  rotate: true

reimu_cursor:
  enable: true
  cursor:
    default: "../images/cursor/my-cursor-default.png"
    pointer: "../images/cursor/my-cursor-pointer.png"
    text: "../images/cursor/my-cursor-text.ani"

post:
  showToc: true
  tocDepth: 3
  showLicense: true
  license: "CC BY-NC-SA 4.0"
  showShare: true
  showRelated: true
  relatedCount: 5
  showComments: false
  showPagination: true

highlight:
  enable: true
  lineNos: true

math:
  enable: true

comment:
  enable: false

search:
  enable: true
  provider: "fuse"

analytics:
  enable: false

busuanzi:
  enable: false
  site_uv: false
  site_pv: false
  page_pv: false
```

- [ ] **Step 3: 替换 `vercel.json`**

Replace `vercel.json` with:

```json
{
  "buildCommand": "hugo --gc --minify",
  "outputDirectory": "public",
  "framework": "hugo",
  "installCommand": "npm install -g hugo-extended@0.161.1",
  "devCommand": "hugo server -D",
  "regions": ["hkg1"]
}
```

- [ ] **Step 4: 验证配置语法**

Run:

```powershell
git diff -- hugo.toml config/_default/params.yml vercel.json
```

Expected: the diff contains clean Chinese text and no mojibake strings such as `濡傛灉`, `閰嶇疆`, or `鍔犺浇`.

- [ ] **Step 5: 提交配置整理**

Run:

```powershell
git add hugo.toml config/_default/params.yml vercel.json
git commit -m "Clean Hugo and Vercel configuration"
```

Expected: commit succeeds.

## Task 4: 重写首页与关于页

**Files:**
- Modify: `content/_index.md`
- Modify: `content/about/_index.md`

- [ ] **Step 1: 替换首页内容**

Replace `content/_index.md` with:

```markdown
---
title: "首页"
date: 2026-05-25T00:00:00+08:00
draft: false
---

# EarthRhythm

欢迎来到 EarthRhythm。

这里记录技术实践、知识观察和一些慢慢成形的想法。它不是一个追求高频更新的地方，更像是一张持续扩展的笔记地图：把好奇心留下来，把问题整理清楚，也把解决问题的过程保存下来。

愿每一次记录，都能让下一次思考更有节律。
```

- [ ] **Step 2: 替换关于页内容**

Replace `content/about/_index.md` with:

```markdown
---
title: "关于"
type: "about"
---

# 关于 EarthRhythm

EarthRhythm 是一个个人知识博客，用来保存技术实践、阅读笔记、冷知识观察和偶尔冒出来的长问题。

我希望这里的内容有两种价值：一是给未来的自己留下一条清楚的路径，二是让路过的人能在某个问题上少绕一点弯。

这个站点会继续保持轻量、安静和可维护。文章内容不追求覆盖所有热点，更关注那些值得反复咀嚼的知识、经验和连接。

## 链接

- GitHub: [EarthxxRhythm](https://github.com/EarthxxRhythm)
- Email: [1930808767@qq.com](mailto:1930808767@qq.com)
```

- [ ] **Step 3: 验证文章正文未变**

Run:

```powershell
git diff -- content/post
```

Expected: no output.

- [ ] **Step 4: 提交页面文案整理**

Run:

```powershell
git add content/_index.md content/about/_index.md
git commit -m "Refresh EarthRhythm homepage and about page"
```

Expected: commit succeeds.

## Task 5: 更新项目与部署文档

**Files:**
- Create: `README.md`
- Modify: `VERCEL_DEPLOYMENT_GUIDE.md`

- [ ] **Step 1: 写入 `README.md`**

Create `README.md` with:

````markdown
# EarthRhythm

EarthRhythm 是一个基于 Hugo 和 `hugo-theme-reimu` 的个人知识博客，用来记录技术实践、知识观察和个人思考。

## 本地准备

1. 安装 Hugo Extended。
2. 初始化主题子模块：

```powershell
git submodule update --init --recursive
```

3. 启动本地预览：

```powershell
hugo server -D
```

4. 生成静态站点：

```powershell
hugo --gc --minify
```

## 目录说明

- `content/`：站点页面和文章内容。
- `config/_default/params.yml`：主题参数和站点品牌信息。
- `hugo.toml`：Hugo 主配置。
- `static/`：图片、头像、光标等静态资源。
- `themes/hugo-theme-reimu/`：主题子模块。
- `public/`：Hugo 生成目录，不作为源码维护。

## 部署

当前优先部署到 Vercel 临时域名。Vercel 设置：

- Framework Preset: Hugo
- Build Command: `hugo --gc --minify`
- Output Directory: `public`
- Install Command: `npm install -g hugo-extended@0.161.1`

部署后检查首页、文章页、导航、样式和静态图片是否正常加载。
````

- [ ] **Step 2: 替换 `VERCEL_DEPLOYMENT_GUIDE.md`**

Replace `VERCEL_DEPLOYMENT_GUIDE.md` with:

````markdown
# EarthRhythm Vercel 部署指南

本项目当前目标是部署到 Vercel 临时域名，例如 `https://earthrhythm.vercel.app/`。自定义域名不在本次重启范围内。

## Vercel 项目设置

- Framework Preset: Hugo
- Build Command: `hugo --gc --minify`
- Output Directory: `public`
- Install Command: `npm install -g hugo-extended@0.161.1`
- Development Command: `hugo server -D`

这些设置已经写入 `vercel.json`。

## 首次导入

1. 登录 Vercel。
2. 从 GitHub 导入 `EarthxxRhythm/earthrhythm-blog`。
3. 确认框架为 Hugo。
4. 确认构建命令、输出目录和安装命令与本文件一致。
5. 点击 Deploy。

## 部署后检查

- 首页可以访问。
- 文章详情页可以访问。
- CSS 和 JavaScript 正常加载。
- 顶部或侧边导航可以跳转。
- 头像、横幅图和文章配图可以加载。
- 页面源代码中的 canonical/base URL 指向 Vercel 临时域名。

## 本地验证

```powershell
git submodule update --init --recursive
hugo --gc --minify
hugo server -D
```

如果本地没有安装 Hugo，可以先依赖 Vercel 构建日志确认结果；本地开发仍建议安装 Hugo Extended。
````

- [ ] **Step 3: 提交文档更新**

Run:

```powershell
git add README.md VERCEL_DEPLOYMENT_GUIDE.md
git commit -m "Document EarthRhythm build and deployment"
```

Expected: commit succeeds.

## Task 6: 最终验证

**Files:**
- Verify: repository state and generated output

- [ ] **Step 1: 检查 Hugo 是否可用**

Run:

```powershell
hugo version
```

Expected if Hugo is installed: a Hugo Extended version string.

Expected if Hugo is missing: PowerShell reports that `hugo` is not recognized; record this in the final summary and use Vercel configuration review as the fallback.

- [ ] **Step 2: 构建站点**

Run if Hugo is available:

```powershell
hugo --gc --minify
```

Expected: build exits with code 0 and writes generated files into `public/`.

- [ ] **Step 3: 验证生成目录仍被忽略**

Run:

```powershell
git status --short --ignored public resources/_gen .hugo_build.lock
```

Expected: generated paths are ignored, not staged as source changes.

- [ ] **Step 4: 验证文章内容未改**

Run:

```powershell
git diff HEAD -- content/post
```

Expected: no output.

- [ ] **Step 5: 最终状态检查**

Run:

```powershell
git status --short --branch
git log --oneline -8
```

Expected: working tree is clean except ignored generated files, and recent commits show the design and relaunch implementation commits.

## 自检记录

- Spec 覆盖：主题来源、构建配置、Vercel 临时域名、首页、关于页、文档、生成物清理和文章不动均有对应任务。
- 占位检查：计划不包含需要后续猜测的占位词或未落地的实现步骤。
- 一致性检查：文件路径与当前仓库结构一致；所有命令使用 PowerShell 语法；文章目录只做验证，不做修改。

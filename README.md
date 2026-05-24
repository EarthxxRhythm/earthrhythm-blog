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

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

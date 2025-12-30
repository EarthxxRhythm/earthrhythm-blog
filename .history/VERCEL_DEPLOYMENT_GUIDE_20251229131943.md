# Vercel部署指南

## 概述
本指南将帮助您将EarthRhythm Hugo博客部署到Vercel平台，并配置自定义域名`earthrhythm.top`。

## 步骤1：准备Hugo项目配置

### 1.1 更新baseURL
在部署前，需要更新`hugo.toml`文件中的`baseURL`。由于Vercel会提供动态域名，我们可以使用环境变量：

```toml
baseURL = "https://earthrhythm.vercel.app/"  # 临时使用Vercel域名
# 或者使用环境变量：baseURL = "https://${VERCEL_URL}/"
```

### 1.2 创建vercel.json配置文件
在项目根目录创建`vercel.json`文件：

```json
{
  "buildCommand": "hugo --gc --minify",
  "outputDirectory": "public",
  "framework": "hugo",
  "installCommand": "npm install -g hugo-extended",
  "devCommand": "hugo server -D",
  "regions": ["hkg1"]
}
```

## 步骤2：部署到Vercel

### 2.1 登录Vercel
1. 访问 https://vercel.com
2. 使用GitHub账户登录
3. 授权Vercel访问您的GitHub账户

### 2.2 导入GitHub仓库
1. 点击"Add New..." → "Project"
2. 从GitHub仓库列表中选择`earthrhythm-blog`
3. 点击"Import"

### 2.3 配置项目设置
在项目配置页面：

1. **项目名称**: `earthrhythm-blog` (或您喜欢的名称)
2. **框架预设**: 选择"Hugo"
3. **构建设置**:
   - Build Command: `hugo --gc --minify`
   - Output Directory: `public`
   - Install Command: `npm install -g hugo-extended`
4. **环境变量** (可选):
   - `HUGO_VERSION`: `0.140.0` (或最新版本)
5. 点击"Deploy"

### 2.4 等待部署完成
Vercel将自动：
1. 克隆您的GitHub仓库
2. 安装Hugo
3. 构建项目
4. 部署到全球CDN

部署完成后，您将获得一个Vercel域名，如：`https://earthrhythm-blog.vercel.app`

## 步骤3：配置自定义域名 `earthrhythm.top`

### 3.1 在Vercel中添加域名
1. 进入项目仪表板
2. 点击"Settings" → "Domains"
3. 在输入框中输入 `earthrhythm.top`
4. 点击"Add"

### 3.2 配置DNS记录

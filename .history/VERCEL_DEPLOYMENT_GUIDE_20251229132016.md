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
Vercel会显示需要配置的DNS记录。您需要到您的域名注册商（如GoDaddy、Namecheap、阿里云等）配置以下记录：

#### 选项A：使用A记录（推荐）
```
类型: A
名称: @ 或 earthrhythm.top
值: 76.76.21.21
TTL: 自动
```

#### 选项B：使用CNAME记录
```
类型: CNAME
名称: www
值: cname.vercel-dns.com
TTL: 自动
```

### 3.3 验证域名所有权
1. 按照Vercel的指示在域名注册商处添加DNS记录
2. 返回Vercel控制台，点击"Verify"
3. 等待DNS传播（通常需要几分钟到几小时）

### 3.4 配置HTTPS
Vercel会自动为您的域名配置SSL证书（Let's Encrypt），启用HTTPS。

## 步骤4：更新Hugo配置使用自定义域名

部署成功后，更新`hugo.toml`中的`baseURL`：

```toml
baseURL = "https://earthrhythm.top/"
```

提交并推送到GitHub：
```bash
git add hugo.toml
git commit -m "Update baseURL for production domain"
git push origin main
```

Vercel会自动检测更改并重新部署。

## 步骤5：验证部署

### 5.1 测试访问
访问以下URL确保都能正常工作：
- https://earthrhythm.top
- https://www.earthrhythm.top (如果配置了)
- https://earthrhythm-blog.vercel.app (Vercel原始域名)

### 5.2 检查功能
1. 首页加载正常
2. 文章页面可访问
3. 图片和资源加载正常
4. 导航菜单工作正常

## 常见问题及解决方案

### 问题1：构建失败
**症状**: Vercel部署失败，错误提示Hugo未找到
**解决方案**:
1. 确保`vercel.json`中的`installCommand`正确
2. 在Vercel项目设置中明确指定HUGO_VERSION环境变量

### 问题2：CSS/JS资源404
**症状**: 页面样式丢失，控制台显示资源404
**解决方案**:
1. 检查`baseURL`配置是否正确
2. 确保主题子模块已正确初始化
3. 运行`git submodule update --init --recursive`

### 问题3：域名验证失败
**症状**: Vercel显示域名验证失败
**解决方案**:
1. 检查DNS记录是否正确配置
2. 等待DNS传播（最多72小时）
3. 使用`dig earthrhythm.top`或`nslookup earthrhythm.top`检查DNS解析

### 问题4：HTTPS证书问题
**症状**: 浏览器显示"不安全"警告
**解决方案**:
1. 在Vercel域名设置中重新颁发证书
2. 确保域名指向正确的Vercel IP地址
3. 等待证书自动续订（Vercel会自动处理）

## 高级配置

### 自动部署
每次推送到GitHub的`main`分支，Vercel会自动重新部署。

### 预览部署
推送到其他分支会创建预览部署，可用于测试。

### 环境变量
可以在Vercel中设置环境变量，用于不同环境（开发、预览、生产）。

## 支持资源
- Vercel文档: https://vercel.com/docs
- Hugo文档: https://gohugo.io/documentation/
- Vercel支持: https://vercel.com/support

## 下一步
完成部署后，您可以：
1. 配置自定义404页面
2. 添加分析工具（Google Analytics, Umami等）
3. 配置图片优化
4. 设置缓存策略
5. 启用自动备份

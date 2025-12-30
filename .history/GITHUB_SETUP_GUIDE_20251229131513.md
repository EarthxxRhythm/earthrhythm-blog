# GitHub仓库创建指南

## 步骤1：创建GitHub仓库

1. 访问 https://github.com/new
2. 填写仓库信息：
   - **Repository name**: `earthrhythm-blog`
   - **Description**: `EarthRhythm - 一个使用Hugo构建的个人博客`
   - **Public** (公开仓库)
   - **不要**勾选"Initialize this repository with a README"（因为我们已经有了本地仓库）
   - **不要**添加.gitignore或license（Hugo项目已经有合适的配置）

3. 点击"Create repository"按钮

## 步骤2：获取远程仓库URL

创建成功后，您会看到类似以下的页面：
```
Quick setup — if you've done this kind of thing before

…or create a new repository on the command line
echo "# earthrhythm-blog" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/EarthxxRhythm/earthrhythm-blog.git
git push -u origin main

…or push an existing repository from the command line
git remote add origin https://github.com/EarthxxRhythm/earthrhythm-blog.git
git branch -M main
git push -u origin main
```

您需要的是这个URL：`https://github.com/EarthxxRhythm/earthrhythm-blog.git`

## 步骤3：配置本地仓库的远程地址

在终端中执行以下命令：

```bash
git remote add origin https://github.com/EarthxxRhythm/earthrhythm-blog.git
git branch -M main
git push -u origin main
```

## 步骤4：验证推送成功

执行以下命令验证：
```bash
git remote -v
git status
```

您应该看到：
```
origin  https://github.com/EarthxxRhythm/earthrhythm-blog.git (fetch)
origin  https://github.com/EarthxxRhythm/earthrhythm-blog.git (push)
```

## 步骤5：继续Vercel部署

完成GitHub仓库创建和代码推送后，请告诉我，我将指导您进行Vercel部署。

## 注意事项

1. 如果您遇到认证问题，可能需要输入GitHub用户名和密码（或访问令牌）
2. 如果使用HTTPS推送遇到问题，可以尝试使用SSH方式：
   ```bash
   git remote set-url origin git@github.com:EarthxxRhythm/earthrhythm-blog.git
   ```

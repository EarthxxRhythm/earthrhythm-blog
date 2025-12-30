# Vercel部署检查清单

## 部署状态跟踪

### 步骤1：Vercel部署进度
- [ ] 已访问 https://vercel.com
- [ ] 已使用GitHub账户登录
- [ ] 已授权Vercel访问GitHub
- [ ] 已导入 `EarthxxRhythm/earthrhythm-blog` 仓库
- [ ] 已确认构建设置（Hugo框架）
- [ ] 已点击"Deploy"按钮
- [ ] 部署正在进行中...
- [ ] 部署完成，获得Vercel域名: ____________________

### 步骤2：验证部署成功
- [ ] 访问Vercel提供的临时域名（如：`https://earthrhythm-blog.vercel.app`）
- [ ] 确认首页加载正常
- [ ] 确认文章页面可访问
- [ ] 确认CSS/JS资源加载正常
- [ ] 确认导航菜单工作正常

### 步骤3：配置自定义域名 `earthrhythm.top`

#### 3.1 在Vercel中添加域名
1. 进入项目仪表板 → Settings → Domains
2. 输入 `earthrhythm.top`
3. 点击"Add"

#### 3.2 DNS配置（根据您的域名注册商选择一种方式）

**选项A：使用A记录（根域名）**
```
记录类型: A
主机/名称: @ 或 earthrhythm.top
值/指向: 76.76.21.21
TTL: 自动 或 3600
```

**选项B：使用CNAME记录（www子域名）**
```
记录类型: CNAME
主机/名称: www
值/指向: cname.vercel-dns.com
TTL: 自动 或 3600
```

**选项C：同时配置根域名和www（推荐）**
1. 为根域名配置A记录（如上）
2. 为www配置CNAME记录（如上）

#### 3.3 常见域名注册商DNS配置位置
- **阿里云/万网**: 控制台 → 域名 → 域名列表 → 解析设置
- **腾讯云/DNSPod**: 控制台 → 域名注册 → 我的域名 → 解析
- **GoDaddy**: 我的产品 → Domains → DNS Management
- **Namecheap**: Dashboard → Domain List → Manage → Advanced DNS

#### 3.4 验证域名
1. 在域名注册商处添加DNS记录后
2. 返回Vercel Domains页面
3. 点击"Verify"
4. 等待DNS传播（通常5-60分钟，最多72小时）

### 步骤4：更新Hugo配置
域名验证成功后，更新`hugo.toml`：
```toml
baseURL = "https://earthrhythm.top/"
```

提交更改：
```bash
git add hugo.toml
git commit -m "Update baseURL to custom domain earthrhythm.top"
git push origin main
```

### 步骤5：最终验证
- [ ] 访问 https://earthrhythm.top (HTTPS自动启用)
- [ ] 确认所有页面功能正常
- [ ] 检查控制台无错误
- [ ] 测试移动端响应式布局

## 故障排除

### 部署失败
1. 检查Vercel部署日志
2. 确认Hugo版本兼容性
3. 验证主题子模块是否正确初始化

### 域名无法访问
1. 使用 `ping earthrhythm.top` 检查DNS解析
2. 使用 `dig earthrhythm.top` 或 `nslookup earthrhythm.top` 查看详细DNS信息
3. 确认DNS记录已正确配置
4. 等待DNS传播完成

### HTTPS证书问题
1. Vercel自动配置Let's Encrypt证书
2. 如果证书未签发，在Vercel Domains页面重新验证
3. 证书通常需要几分钟到几小时签发

## 支持资源
- Vercel部署状态: 项目仪表板 → Deployments
- Vercel文档: https://vercel.com/docs
- Hugo文档: https://gohugo.io/documentation/

## 部署完成确认
当所有步骤完成后，您的博客将通过以下地址访问：
- 主域名: https://earthrhythm.top
- Vercel域名: https://earthrhythm-blog.vercel.app (备用)

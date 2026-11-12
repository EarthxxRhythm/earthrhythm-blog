# 从 Vercel 迁移到 EdgeOne Pages

本文记录 EarthRhythm 博客从 Vercel 迁往腾讯云 EdgeOne Pages（Makers）的方案、实测依据与操作步骤。

## 为什么选 EdgeOne Pages

在「免费 + 国内可访问」的约束下，实测过的候选平台（本机为国内网络，2026-11-12 测得）：

| 目标 | 实测结果 | 结论 |
| --- | --- | --- |
| `earthrhythm.cn`（Vercel + 自有域名，缓存命中） | 200，connect 82ms，TTFB 295ms | 现状，可用 |
| `earthrhythm-blog.vercel.app`（Vercel 共享域） | **连接失败 code=000** | 国内不可用 |
| `teams.pages.dev`（Cloudflare Pages 真实站点） | **连接失败 code=000** | 国内不可用 |
| `www.netlify.app`（Netlify 共享域） | **连接失败 code=000** | 国内不可用 |
| `www.cloudflare.com`（Cloudflare 边缘） | TTFB 570–667ms | 可达但更慢 |
| `pages.edgeone.ai`（EdgeOne Pages 站点） | 200，connect 277ms，TTFB 412ms | 可达，腾讯系 |

关键结论：**各平台的共享二级域名在国内普遍不可用，迁移后必须继续使用自有域名 `earthrhythm.cn`。**

EdgeOne Pages 的优势（来自官方文档，2026-11 核对）：

- 有**免费版套餐**，定位为「个人创意探索与功能验证等非商业用途」。
- 官方宣称不按流量计费：「无论流量如何，您的网站都能保持快速响应」。
- 提供 **Hugo 框架指南**、Git 仓库导入、直接上传三种部署方式。
- 支持自定义域名，并自动签发 SSL 证书。
- 提供 **EdgeOne CLI**（`npm install -g edgeone`）与 **API Token**，部署可脚本化。
- 官方文档含「从 Vercel 迁移至 EdgeOne」章节。

## 待确认项（不要当成已确认事实）

这三点尚未取得确证，执行时以控制台/CLI 实际表现为准：

1. **自定义域名是否需要 ICP 备案。** 若走中国大陆加速节点，按腾讯云惯例需要备案；当前站点页脚无备案号，2026-11-10 还收到过腾讯云 ICP 整改通知，说明网站备案大概率仍缺失。
2. **Git 导入是否自动初始化 git submodule。** 见下节风险。
3. **CLI 的具体部署子命令。** 已确认命名空间为 `edgeone makers`（旧 `edgeone pages` 过渡期仍可用但会下线），子命令以 `edgeone makers --help` 实测为准。

## 头号技术风险：主题是 git submodule

`themes/hugo-theme-reimu` 在主仓库索引中是 **gitlink（mode `160000`）**，主题文件本身未被主仓库跟踪：

```text
160000 60235f72b603c0e87f44f0af15472b766a1476de 0	themes/hugo-theme-reimu
```

也就是说：**任何平台做全新 clone，若不执行 `git submodule update --init --recursive`，主题目录会是空的，Hugo 构建必然失败。** Vercel 会自动初始化子模块；EdgeOne 是否如此尚未确认。

两条应对路径：

- **路径 A（推荐，绕开该风险）**：本地构建后直接上传 `public/`，或使用 EdgeOne CLI 部署构建产物。完全不依赖平台的 submodule 行为。
- **路径 B（Git 自动构建）**：把主题 vendor 进主仓库（删除 gitlink 与 `.gitmodules`，将主题文件作为普通文件提交）。仓库体积增加约 14 MB，主题升级需手动同步。

## 构建配置（已本地验证）

构建命令与输出目录沿用现有配置，已在本地实测通过：

```text
Build Command:    npx -y hugo-extended@0.161.1 --gc --minify
Output Directory: public
Root Directory:   /（仓库根）
```

本地验证记录：`hugo v0.161.1 extended` 构建 exit 0，43 pages / 214 static files，耗时 1028 ms。

## 迁移步骤

### 第 1 步：平台侧准备（需要腾讯云账号）

1. 登录腾讯云控制台，开通 **Makers** 服务。
2. 创建 Makers 项目（记录项目名与所属空间）。
3. 在「API Token」页面创建一枚 Token，供 CLI 使用。

### 第 2 步：部署

**方式一：CLI（推荐，可绕开 submodule 风险）**

```bash
npm install -g edgeone
npx -y hugo-extended@0.161.1 --gc --minify   # 本地构建
edgeone makers --help                         # 查看当前可用子命令后再执行部署
```

**方式二：控制台直接上传**

把本地 `public/` 目录打包上传，绑定域名后即可访问。

**方式三：Git 仓库导入**

- 仓库：`https://github.com/EarthxxRhythm/earthrhythm-blog`
- 构建命令与输出目录见上一节
- 若构建日志报 `found no layout file` 或主题目录为空，说明子模块未初始化，改用方式一并按需执行路径 B

### 第 3 步：绑定域名

1. 在 EdgeOne 项目的域名管理中添加 `earthrhythm.cn` 与 `www.earthrhythm.cn`。
2. 按控制台提示，在**阿里云 DNS（当前 NS 为 `dns15/dns16.hichina.com`）**中把 `earthrhythm.cn` 的 A 记录从 `76.76.21.21` 改为 EdgeOne 分配的目标（通常为 CNAME）。
3. `www` 同样指向 EdgeOne，并保留 `www` → 裸域 的 301 跳转。
4. 等待证书签发（自动申请免费证书）。

### 第 4 步：验证

```bash
curl.exe -sSI https://earthrhythm.cn/
curl.exe -sSI https://www.earthrhythm.cn/
curl.exe -sS -o NUL -w "dns=%{time_namelookup} connect=%{time_connect} tls=%{time_appconnect} ttfb=%{time_starttransfer}\n" https://earthrhythm.cn/
```

检查项：

- 首页、文章页、导航、样式、图片正常加载。
- `www` 返回 301 到裸域。
- 页面 `og:url` 与 canonical 仍指向 `https://earthrhythm.cn/`。
- 控制台无报错，TTFB 不劣于迁移前基线（缓存命中 295ms）。

## 回滚方案

迁移期间保留 Vercel 项目与其部署配置（`vercel.json` 不删除）：

1. 将阿里云 DNS 中的 `earthrhythm.cn` A 记录改回 `76.76.21.21`。
2. 等待 DNS TTL 生效（建议迁移前先把 TTL 调到 600 秒或更低）。
3. 确认 Vercel 项目仍持有该域名。

## 与本次迁移无关但影响体验的既有问题

首页引用的本域资源合计 **23.26 MB**，是访问体感的主要瓶颈，与托管平台无关：

| 资源 | 体积 |
| --- | --- |
| 4 张 funfact PNG 文章封面 | 18.4 MB |
| `avatar.jpg` | 3.3 MB |
| `banner.jpg`（`fetchpriority=high`，LCP 必载） | 1.5 MB |
| CSS + JS | 61 KB |

迁到任何平台都需要先压缩这些资源；若改用按流量计费的免费额度，未压缩的 23 MB 会让额度迅速耗尽。

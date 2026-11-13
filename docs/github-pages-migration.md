# 迁移到 GitHub Pages

把博客从 Vercel 迁到 GitHub Pages + GitHub Actions，全程免费，且不需要新账号（仓库已在 GitHub）。

## 实测依据

同一时刻、本机国内网络实测（2026-11-12）：

| 目标 | connect | TLS | TTFB | 结论 |
| --- | --- | --- | --- | --- |
| `necolas.github.io`（真实 Pages 站点） | 147ms | 246ms | **333ms** | 可用，快 |
| `earthrhythm.cn`（Vercel 现状） | 116ms | 264ms | 940ms | 现状 |
| `185.199.108–111.153`（Pages 官方 IP） | 55–66ms | 成功 | — | 四个 IP 全部可达 |
| `github.io` DNS | — | — | — | 解析到 4 个 185.199.x.x，无污染 |

对照此前测过的平台：`vercel.app` / `teams.pages.dev` / `netlify.app` 共享域全部 `code=000` 不可达，而 `github.io` 可达。**在本次实测中 GitHub Pages 明显快于当前 Vercel。**

前提核实（GitHub API）：

- `EarthxxRhythm/earthrhythm-blog` 的 `private: false` → 免费 Pages 可用。
- `default_branch: main` → 与工作流触发分支一致。
- `has_pages: false` → 尚未开启，需在仓库设置里启用。

## 已完成的仓库改动

| 文件 | 作用 |
| --- | --- |
| `.github/workflows/hugo.yml` | 推送 `main` 时自动构建并部署到 Pages |
| `static/CNAME` | 声明自定义域名 `earthrhythm.cn` |

工作流要点：

- 安装 `hugo_extended_0.161.1`（与 `vercel.json` 中的版本一致；该 release 资产已核实存在）。
- `actions/checkout` 带 `submodules: recursive` —— 解决主题是 gitlink 子模块、全新 clone 后目录为空的问题。
- 用 `actions/configure-pages@v5` 的输出作为 `--baseURL`，因此**无论绑定自定义域名与否都能正确生成资源路径**。
- 分 build / deploy 两个 job，权限最小化（`contents: read`、`pages: write`、`id-token: write`）。

本地验证记录：

- YAML 解析通过（js-yaml），jobs/steps/permissions/triggers 结构正确。
- 子路径构建通过：`--baseURL https://earthxxrhythm.github.io/earthrhythm-blog/` → 848ms，资源正确带前缀（`/earthrhythm-blog/css/main.min.*.css`）。
- `static/CNAME` 会被复制进产物（`public/CNAME` = `earthrhythm.cn`）。

## 实际部署记录（2026-11-13）

### 坑：GitHub 自动生成的 `static.yml` 会覆盖 Hugo 产物

在 Settings → Pages 里把 Source 选成 “GitHub Actions” 时，GitHub 会**自动创建** `.github/workflows/static.yml`（提交 `b202f55`）。该模板是通用静态站方案，用 `path: '.'` 上传**整个仓库根目录**；对 Hugo 项目来说，根目录没有 `index.html`，Hugo 产物在 `public/`，因此站点必然 404。

实测时间线：

| 时间 | 事件 |
| --- | --- |
| 03:30:25 | `Deploy Hugo site to Pages`（`hugo.yml`）部署完成 |
| 03:30:45 | `Deploy static content to Pages`（`static.yml`）部署完成，**晚 20 秒覆盖前者** |
| — | `github.io` 访问返回 404 |

修复：删除 `.github/workflows/static.yml`（提交 `ec07268`）。删除前做过零引用检查——`static.yml` 文本、工作流名、`workflow_run` 交叉引用均为 0 命中。修复后 run#3 success，站点恢复 200。

**结论：一旦启用 Pages，务必立即删除 GitHub 自动生成的 `static.yml`，否则它会周期性覆盖真实产物。**

### 当前线上状态（已验证）

- `https://earthxxrhythm.github.io/earthrhythm-blog/` → 200，TTFB 401ms
- 新文章页 `.../post/fittracker-devlog/` → 200，TTFB 492ms
- 资源路径正确带子路径前缀：`/earthrhythm-blog/css/main.min.*.css`（200，54377 B）
- `js/pjax_main.js` 200（7695 B）、`images/banner.jpg` 200（1565840 B）
- `og:url` = `https://earthxxrhythm.github.io/earthrhythm-blog/`

### 待完成

自定义域名 `earthrhythm.cn` 尚未在 Pages Settings 中配置，阿里云 DNS 也未切换，因此线上仍走 `github.io` 子路径。Vercel 继续服务 `earthrhythm.cn`。

## 需要人工完成的两步

### 第 1 步：开启 Pages（GitHub 网页）

仓库 → **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**。

在此之前，工作流的 deploy 步骤会失败（build 步骤仍能通过），这是预期行为，不是配置错误。

### 第 2 步：切换 DNS（阿里云，当前 NS 为 `dns15/dns16.hichina.com`）

先不要动 DNS，等 Pages 部署成功、用 `github.io` 域名验证通过再切。

绑定 apex 域名需要 4 条 A 记录：

```text
earthrhythm.cn   A   185.199.108.153
earthrhythm.cn   A   185.199.109.153
earthrhythm.cn   A   185.199.110.153
earthrhythm.cn   A   185.199.111.153
www              CNAME  earthxxrhythm.github.io
```

切换前建议把 TTL 调低（600 秒或更低），便于回滚。

然后在仓库 Settings → Pages → Custom domain 填 `earthrhythm.cn`，等待校验通过，再勾选 **Enforce HTTPS**（证书由 GitHub 自动签发，通常几分钟）。

## 验证

```bash
# 首次部署后，先用 project 域名验证（路径前缀为仓库名）
curl.exe -sSI https://earthxxrhythm.github.io/earthrhythm-blog/

# DNS 切换后验证自定义域名
curl.exe -sSI https://earthrhythm.cn/
curl.exe -sS -o NUL -w "dns=%{time_namelookup} connect=%{time_connect} tls=%{time_appconnect} ttfb=%{time_starttransfer}\n" https://earthrhythm.cn/
```

检查项：

- 首页、文章页、导航、样式、图片正常加载（重点看 CSS 是否 404 —— 子路径 baseURL 已验证，但域名切换后要复验）。
- 页面 `og:url` / canonical 指向 `https://earthrhythm.cn/`。
- `www` 跳转到裸域。
- HTTPS 无警告。

## 回滚

Vercel 项目与 `vercel.json` 保持不动，DNS 改回即可：

```text
earthrhythm.cn   A   76.76.21.21
```

## 风险

1. **国内可达性会波动。** 本次实测 `github.io` 与 4 个 IP 均可达且低延迟，但这是单网络单时刻的样本，历史上 GitHub Pages 在国内出现过局部不可达。若某天出现访问异常，DNS 回切 Vercel 是最快的退路。
2. **没有 Vercel 那样的缓存头配置。** Pages 由 Fastly 承载，缓存行为不可自定义，`vercel.json` 里那套 `<immutable>` 头不会生效。
3. **apex 域名依赖 4 条 A 记录**，不像 CNAME 可随平台 IP 变化自动跟随；GitHub 若更换 IP 需要手动改 DNS。
4. **仓库存量问题不因此消失**：首页仍引用 23.26 MB 资源（4 张封面 18.4 MB + avatar 3.3 MB + banner 1.5 MB）。换托管平台不改变这一点。

## 与 EdgeOne Pages 方案的关系

`docs/edgeone-pages-migration.md` 保留为备选。两者对比：

| 维度 | GitHub Pages | EdgeOne Pages |
| --- | --- | --- |
| 需要新账号/开通 | 不需要 | 需要腾讯云账号 + 开通 Makers |
| 实测 TTFB | 333ms | 412ms（接入站点） |
| 自动部署 | 推送即部署 | 需 CLI 或控制台操作 |
| 国内节点 | 无（Fastly 境外） | 免费版实际亦为境外/边缘 |
| 备案要求 | 不需要 | 大陆加速需备案 |

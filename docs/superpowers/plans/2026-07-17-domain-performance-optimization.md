# Domain Performance Optimization Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Improve `earthrhythm.cn` access speed from the domain and edge-delivery layer before touching broader theme or content performance work.

**Architecture:** Keep Hugo and Vercel as the origin, normalize traffic onto the apex domain, and set explicit cache headers so a CDN in front of Vercel can cache HTML and static responses more effectively. Pair the repo change with a Cloudflare cutover checklist because DNS and proxy activation live outside the repository.

**Tech Stack:** Hugo, Vercel static hosting, `vercel.json`, Cloudflare DNS/proxy

## Global Constraints

- Preserve the existing Hugo build and output directories.
- Do not revert unrelated in-flight user changes in the worktree.
- Keep the canonical public domain as `https://earthrhythm.cn/`.
- Make repo-side changes small enough to ship independently of the broader theme optimization work.

---

### Task 1: Normalize Hostname and Edge Caching

**Files:**
- Modify: `C:\.CodeSpace\MyProjects\https-github-com-earthxxrhythm-earthrhythm-blog\vercel.json`

**Interfaces:**
- Consumes: Existing Vercel static deployment configuration
- Produces: One apex-domain redirect rule and explicit cache headers for CDN/browser behavior

- [ ] **Step 1: Inspect current Vercel config**

Run: `Get-Content vercel.json`

Expected: A minimal config with build, output, dev command, and `regions`.

- [ ] **Step 2: Add a failing verification target**

Run: `curl.exe -I -sS https://earthrhythm.cn/`

Expected before change: `Cache-Control: public, max-age=0, must-revalidate` and no repository-defined redirect behavior for `www`.

- [ ] **Step 3: Add the minimal config change**

Add:

```json
{
  "redirects": [
    {
      "source": "/:path*",
      "has": [{ "type": "host", "value": "www.earthrhythm.cn" }],
      "destination": "https://earthrhythm.cn/:path*",
      "permanent": true
    }
  ],
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "public, max-age=120" },
        { "key": "CDN-Cache-Control", "value": "public, max-age=3600, stale-while-revalidate=86400" },
        { "key": "Vercel-CDN-Cache-Control", "value": "public, max-age=3600, stale-while-revalidate=86400" }
      ]
    }
  ]
}
```

- [ ] **Step 4: Verify the config builds**

Run: `cmd /c npx -y hugo-extended@0.161.1 --gc --minify`

Expected: Exit code `0` and refreshed `public/` output.

- [ ] **Step 5: Commit**

```bash
git add vercel.json
git commit -m "perf: normalize domain routing and cache headers"
```

### Task 2: Document the External Cloudflare Cutover

**Files:**
- Create: `C:\.CodeSpace\MyProjects\https-github-com-earthxxrhythm-earthrhythm-blog\docs\cloudflare-cutover.md`

**Interfaces:**
- Consumes: Current domain state (`earthrhythm.cn` resolves, `www.earthrhythm.cn` missing)
- Produces: A deployment checklist the operator can follow outside the repo

- [ ] **Step 1: Capture current DNS state**

Run: `Resolve-DnsName earthrhythm.cn` and `Resolve-DnsName www.earthrhythm.cn`

Expected: Apex resolves to Vercel, `www` currently does not resolve.

- [ ] **Step 2: Write the cutover checklist**

Document:

```md
- Add both `earthrhythm.cn` and `www.earthrhythm.cn` in Vercel Domains.
- In Cloudflare DNS, proxy `www` to `cname.vercel-dns.com`.
- Prefer a proxied apex CNAME with Cloudflare flattening; use `76.76.21.21` only as fallback.
- Turn on Full (strict) SSL/TLS and HTTP/3.
- Re-test `curl -I` and DNS after propagation.
```

- [ ] **Step 3: Verify the document is readable**

Run: `Get-Content docs\\cloudflare-cutover.md`

Expected: A concise, operator-ready checklist with no placeholders.

- [ ] **Step 4: Commit**

```bash
git add docs/cloudflare-cutover.md
git commit -m "docs: add cloudflare cutover checklist"
```

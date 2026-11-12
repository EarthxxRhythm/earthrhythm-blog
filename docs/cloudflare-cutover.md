# Cloudflare Cutover Checklist

This checklist covers the external steps that cannot be completed from the repository alone.

## Current State on July 17, 2026

- `earthrhythm.cn` resolves to `76.76.21.21` and is served by Vercel.
- `www.earthrhythm.cn` does not currently resolve.
- The site is already returning `x-vercel-cache: HIT`, so the main bottleneck is not DNS lookup itself. It is the end-to-end path to the edge.

## Target Topology

- Cloudflare sits in front of Vercel as the public edge.
- Vercel remains the origin that builds and serves the Hugo output.
- Both apex and `www` are valid in DNS, but `www` permanently redirects to `https://earthrhythm.cn/`.

## Vercel Dashboard

1. Add `earthrhythm.cn` as a production domain.
2. Add `www.earthrhythm.cn` as a production domain.
3. Keep the repository `vercel.json` redirect so `www` normalizes to the apex domain.

## Cloudflare DNS

1. Move the domain to Cloudflare nameservers if it is not already there.
2. Create a proxied `CNAME` for `www` pointing to `cname.vercel-dns.com`.
3. Prefer a proxied apex `CNAME` to `cname.vercel-dns.com` with Cloudflare flattening enabled.
4. If apex `CNAME` is not available in your current DNS workflow, use `A 76.76.21.21` as the fallback for `earthrhythm.cn`.

## Cloudflare SSL and Transport

1. Set SSL/TLS mode to `Full (strict)`.
2. Turn on `Always Use HTTPS`.
3. Turn on `HTTP/3`.
4. Leave automatic compression enabled.

## Post-Cutover Verification

Run these after DNS propagation:

```powershell
Resolve-DnsName earthrhythm.cn
Resolve-DnsName www.earthrhythm.cn
curl.exe -I https://earthrhythm.cn/
curl.exe -I https://www.earthrhythm.cn/
curl.exe -sS -w "dns=%{time_namelookup} connect=%{time_connect} tls=%{time_appconnect} ttfb=%{time_starttransfer} total=%{time_total}`n" -o NUL https://earthrhythm.cn/
```

Healthy signs:

- `www.earthrhythm.cn` resolves successfully.
- `https://www.earthrhythm.cn/` returns a permanent redirect to `https://earthrhythm.cn/`.
- The response is served through Cloudflare instead of going directly from the client to Vercel.
- TLS and TTFB improve compared with the July 17, 2026 baseline.

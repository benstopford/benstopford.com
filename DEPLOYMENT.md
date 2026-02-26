# Deployment Notes — benstopford.com

## Current State
- Site hosted on GitHub Pages at: https://benstopford.github.io/benstopford.com/
- Domain registrar: (your registrar — check where benstopford.com is registered)
- Cloudflare account set up as CDN/DNS proxy

## Cloudflare Nameservers
Point benstopford.com DNS to these nameservers at your registrar:

```
edna.ns.cloudflare.com
quinton.ns.cloudflare.com
```

## Steps to Complete Migration to benstopford.com

### 1. Update registrar nameservers
Log in to your domain registrar and replace the current nameservers with the two Cloudflare nameservers above. DNS propagation can take up to 48 hours.

### 2. Add DNS record in Cloudflare
In Cloudflare dashboard, add a CNAME record:
- Type: `CNAME`
- Name: `@` (or `www`)
- Target: `benstopford.github.io`
- Proxy status: Proxied (orange cloud) — this enables CDN/HTTPS

### 3. Add CNAME file to repo
Create `static/CNAME` containing just:
```
benstopford.com
```
This tells GitHub Pages to serve the site on the custom domain.

### 4. Update hugo.toml
Change:
```toml
baseURL = "https://benstopford.github.io/benstopford.com/"
```
To:
```toml
baseURL = "https://www.benstopford.com/"
```

### 5. Configure GitHub Pages custom domain
In GitHub repo Settings > Pages, set custom domain to `benstopford.com`. GitHub will add the CNAME file automatically if not already present.

### 6. Enable HTTPS in GitHub Pages
Once DNS propagates, tick "Enforce HTTPS" in GitHub Pages settings.

## Notes
- Cloudflare proxied mode gives CDN caching and speeds up the site significantly
- GitHub Pages enforces HTTPS via Let's Encrypt; Cloudflare adds an additional SSL layer
- The old WordPress site can be retired once traffic is confirmed on the new site

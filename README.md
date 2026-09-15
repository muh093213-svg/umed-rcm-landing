# UMed RCM — Landing Page

Static landing page for UMed RCM Solutions, built to deploy on Cloudflare Pages with no build step.

```
umed-rcm-landing/
├── index.html          Main landing page (design unchanged, logo + VSL added)
├── 404.html            Not-found page, same styling
├── _headers            Security + caching rules for Cloudflare Pages
├── _redirects          Short links: /audit, /book, /video → booking section
├── robots.txt          Search engine rules
├── sitemap.xml         Single-page sitemap
├── .gitignore
└── assets/
    ├── logo-mark.png   Circle mark used in the top-left header
    ├── logo-full.png   Full stacked logo (404 page + social preview image)
    └── favicon.png     Browser tab icon
```

There is no npm install, no build command, nothing to compile. Cloudflare serves these files as-is.

---

## 1. Put it on GitHub

1. Create a new **private** repository on GitHub named `umed-rcm-landing`. Don't add a README or .gitignore — this folder already has them.
2. Upload this folder's contents. Two ways:

**Drag and drop (easiest):** on the empty repo page click **uploading an existing file**, then drag in `index.html`, `404.html`, `_headers`, `_redirects`, `robots.txt`, `sitemap.xml`, `README.md`, and the whole `assets` folder. Commit.

**Command line:**
```bash
cd umed-rcm-landing
git init
git add .
git commit -m "UMed RCM landing page"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/umed-rcm-landing.git
git push -u origin main
```

> Important: the files must sit at the **root** of the repo, not inside a nested `umed-rcm-landing/` folder. If they end up nested, set the build output directory in step 2 to that folder name instead of `/`.

---

## 2. Connect Cloudflare Pages

1. Go to [dash.cloudflare.com](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
2. Authorize GitHub, pick the `umed-rcm-landing` repo.
3. Build settings — this is where most people get tripped up. Set:
   - **Framework preset:** `None`
   - **Build command:** *leave completely empty*
   - **Build output directory:** `/`
   - **Root directory:** leave as-is
4. **Save and Deploy.** It takes about 30 seconds and gives you a live URL like `umed-rcm-landing.pages.dev`.

Every push to `main` after this redeploys automatically. Pull requests get their own preview URLs.

---

## 3. Attach umedrcm.com

In your Pages project → **Custom domains** → **Set up a domain**.

- Add `www.umedrcm.com` and `umedrcm.com`.
- If the domain's nameservers are already on Cloudflare, the DNS records are created for you — nothing else to do.
- If the domain is registered elsewhere, Cloudflare shows you the exact CNAME to add at your registrar.

HTTPS is issued automatically, usually within a few minutes.

---

## 4. Drop in your VSL

Open `index.html` and search for `VSL EMBED`. Replace `VIDEO_ID_HERE` in the iframe `src`.

**YouTube** — if the video URL is `https://www.youtube.com/watch?v=abc123XYZ`, the ID is `abc123XYZ`:
```html
src="https://www.youtube-nocookie.com/embed/abc123XYZ?rel=0&amp;modestbranding=1"
```

**Vimeo** — replace the whole `src` with:
```html
src="https://player.vimeo.com/video/123456789"
```

**Cloudflare Stream** (best option if the video is confidential or you want watch-time analytics — upload it under Stream in the same dashboard):
```html
src="https://customer-CODE.cloudflarestream.com/VIDEO_ID/iframe"
```

**Wistia:**
```html
src="https://fast.wistia.net/embed/iframe/VIDEO_ID"
```

The wrapper around the iframe already keeps a 16:9 ratio on every screen size, so you don't need to touch any other line.

Want autoplay? Browsers only allow it muted, which defeats the purpose for a sales video — leave it click-to-play.

---

## 5. Swapping the logo later

Replace `assets/logo-mark.png` with a new file of the same name and push. If your new logo is wide rather than square, change `h-14` to `h-10` on the `<img>` tag in the header so it doesn't overpower the row.

---

## Things worth knowing

**The audit form doesn't email you anything.** Submitting it reveals the Calendly scheduler and passes the practice name, email, and phone into it, so the lead is captured when they book. If someone fills the form and then abandons before booking, that lead is lost. If you want every submission captured regardless, you'd add a Cloudflare Pages Function or point the form at a form service.

**Calendly phone prefill:** the phone number is passed as custom answer `a1`. In Calendly, open your "UMed RCM Free 48-Hour AR Audit Consultation" event → Invitee Questions, and make sure the **first** custom question is the phone number field. Otherwise the number lands in the wrong box.

**Tailwind loads from a CDN and compiles in the browser.** It works, but it adds roughly a second before styles settle on slow connections. Fine for launching now; if you later want a faster page, the fix is to compile Tailwind into a static CSS file — the design wouldn't change at all.

**Testing locally:** open `index.html` directly in a browser and the logo won't appear, because paths start with `/`. Run a local server instead:
```bash
python3 -m http.server 8000
```
then visit `http://localhost:8000`.

**HIPAA note:** the form collects a practice name, email, and phone — no patient data — so nothing here is PHI. Keep it that way. AR aging exports should move over the BAA-covered channel, never through this page.

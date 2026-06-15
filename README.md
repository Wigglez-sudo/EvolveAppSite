# Evolve — landing site

A mobile-first landing page for the Evolve app: what it is, the feature set,
install instructions for iPhone and Android, food packs, and an FAQ. It matches
the app's brand (same dark palette, gradient "E", Inter type) so the two read as
one product.

## Files

```
index.html            # the whole site (self-contained: HTML + CSS + a little JS)
icon-512.png          # Evolve logo (used in hero, header, footer, social preview)
apple-touch-icon.png  # home-screen icon if someone installs the *site* too
favicon.png           # browser tab icon
```

No build step, no dependencies — it's plain static files. The only external
request is the Google Fonts stylesheet for Inter.

---

## 1) Point the "Open Evolve" buttons at your app

Open `index.html` and find this line near the bottom (inside `<script>`):

```js
var APP_URL = "/app/";
```

Set it to wherever the **actual Evolve app** is hosted. Every "Open Evolve"
button on the page uses this one value. Pick the option that matches your setup
(see section 3):

| Your setup | Set APP_URL to |
| --- | --- |
| App in an `/app/` folder on the **same** domain | `"/app/"` |
| App on a **subdomain** | `"https://app.evolveapp.cc/"` |
| App on GitHub Pages | `"https://wigglez-sudo.github.io/EvolveApp/"` |

---

## 2) Host it on Cloudflare Pages (free)

1. Push these files to a GitHub repo (e.g. `EvolveSite`, or a `site/` folder in
   an existing repo).
2. Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git**.
3. Pick the repo. **Framework preset: None.** Build command: leave **empty**.
   Build output directory: `/` (or `site` if you put the files in a subfolder).
4. **Save and Deploy.** You'll get a `*.pages.dev` URL to test.

## 2b) Connect your domain (evolveapp.cc)

1. In Cloudflare, add `evolveapp.cc` as a site (if it isn't already) and point
   your registrar's nameservers at Cloudflare. (Skip if the domain already lives
   in this Cloudflare account.)
2. In the Pages project → **Custom domains → Set up a custom domain** →
   `evolveapp.cc` (and add `www.evolveapp.cc` too if you want).
3. Cloudflare adds the DNS records automatically and issues HTTPS. Give it a few
   minutes; then `https://evolveapp.cc` serves this site.

> You mentioned "redirecting" the domain. You don't need a redirect — pointing
> the domain straight at the Pages project (above) is cleaner and gives you
> proper HTTPS on `evolveapp.cc` itself. A redirect would just bounce visitors to
> an ugly URL.

---

## 3) Where should the *app* live? (the important decision)

The landing page is the front door; the app is the room behind it. For a PWA,
**the domain the app is served from is the domain it installs under** — so this
choice affects what users end up with on their home screen.

**Recommended — one domain, app in `/app/`:**
Host the app on the **same** Cloudflare Pages project, in an `app/` folder.

```
/ (repo root)
  index.html        ← this landing page
  icon-512.png ...
  app/              ← the ENTIRE Evolve app goes here
    index.html
    app.js
    sw.js
    manifest.json
    food-db/  tools/  ...
```

Then set `APP_URL = "/app/"`. Result: the site is `evolveapp.cc`, the app is
`evolveapp.cc/app/`, and the PWA installs under your own domain. The app uses
relative paths and a relative service-worker scope, so moving it into `/app/`
needs **no code changes** — just keep every app file together inside the folder.

**Alternative — subdomain:**
Keep the app as its own Pages project at `app.evolveapp.cc`, set
`APP_URL = "https://app.evolveapp.cc/"`. Cleaner separation, but you manage two
projects and one extra DNS record.

**Quickest — link to GitHub Pages:**
Leave the app on GitHub Pages and set
`APP_URL = "https://wigglez-sudo.github.io/EvolveApp/"`. Works immediately, but
the app then installs under the github.io address, not your nice domain.

---

## 4) Heads-up: renaming the repo changed the app's URL

You renamed the repo to **EvolveApp**, so the GitHub Pages URL is now
`https://wigglez-sudo.github.io/EvolveApp/` (was `…/EvolveAppTest/`). GitHub
usually redirects the old address, but don't rely on it forever. Anyone who
installed the PWA from the old URL should re-add it from the new one.

The app itself works at any URL because everything in it is relative — the
rename doesn't break the app, food packs, or backups.

---

## Editing the copy

Everything is in `index.html` as plain text — feature names, FAQ answers, the
shop list, the footer version string ("Version 1.0"). Change wording directly;
there's no template layer. Keep the brand colours as-is (they're CSS variables at
the top, copied from the app) so the site and app stay visually in sync.

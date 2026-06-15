# Evolve website — deploy & maintenance guide

> This is the **developer/deploy** guide (for the site owner). The public-facing
> `README.md` is written for end users. Keep them separate.

The site is the marketing front door for the Evolve app. It now has **two pages**
plus a **shared stylesheet**, all matching the app's brand (same dark palette,
gradient "E", Inter type) so the two read as one product.

- Live: **https://evolveapp.cc**
- The actual app it links to: **https://wigglez-sudo.github.io/EvolveApp/**
  (a SEPARATE project/repo — see "App vs website" below).

---

## Files (upload ALL of these together)

```
index.html            # landing page (hero w/ animated phone, pillars, features,
                      #   privacy, food packs, FAQ, contact, final CTA)
install.html          # dedicated install page (iOS/Android steps + Open-the-app)
site.css              # shared stylesheet used by BOTH pages  ← required
icon-512.png          # Evolve logo (hero, header, footer, social preview)
apple-touch-icon.png  # home-screen icon if someone adds the site itself
favicon.png           # browser tab icon
README.md             # end-user facing readme
DEPLOY.md             # this guide
```

No build step, no dependencies — plain static files. The only external request is
the Google Fonts stylesheet (Inter). **Common mistake:** uploading `index.html`
alone. The new pages need `site.css` and the icons in the SAME folder, and
`install.html` must be present or "How to install" 404s. Always upload the whole
set.

---

## How the "Open the app" links work (no APP_URL anymore)

Earlier versions used a single `APP_URL` variable. That's GONE. The redesign
hard-codes the app link directly in the HTML. Every "Open the app" button points
to:

```
https://wigglez-sudo.github.io/EvolveApp/
```

To change where the app is hosted, find-and-replace that URL across `index.html`
and `install.html` (it appears a handful of times). That's the only place the app
address lives now.

Current hero CTA order (landing): **How to install** (primary/orange) first,
**Open the app** (ghost/black) second. The final CTA at the page bottom matches.

---

## Deploy (Cloudflare)

The site is served from Cloudflare. Two common setups:

**A) Pages connected to a GitHub repo (recommended):**
1. Push these files to the site repo.
2. Cloudflare → Workers & Pages → the project → it rebuilds on push.
3. Custom domain `evolveapp.cc` is already attached (see below).

**B) Direct upload:** replace the files in the Cloudflare dashboard for the
project, then it redeploys.

After deploy, give it a minute and hard-refresh. `install.html` is reachable at
both `/install.html` and `/install` (Cloudflare pretty URLs).

### Custom domain (already done, for reference)
- The custom domain `evolveapp.cc` is attached via Workers & Pages → the project
  → Custom domains. Cloudflare created the correct DNS record automatically.
- IMPORTANT past gotcha (resolved): a leftover Namecheap **parking A-record**
  (192.64.119.205, proxied) caused a 522 error. Fix was to DELETE that A-record;
  attaching the custom domain creates the right record itself. Leave the email
  MX/TXT records (eforward*.registrar-servers.com, spf) untouched.
- Cloudflare account: Evolveappcc@proton.me.

---

## App vs website — TWO separate projects

This trips people up, so to be explicit:

| | App | Website |
| --- | --- | --- |
| What | the Evolve PWA itself | the marketing/landing + install site |
| Repo | github.com/Wigglez-sudo/EvolveApp | the site repo |
| Live | wigglez-sudo.github.io/EvolveApp/ | evolveapp.cc |
| Host | GitHub Pages | Cloudflare |
| Installs PWA? | YES (has manifest + service worker) | NO (links to the app) |

Because a PWA installs under whatever URL it's served from, installing from the
landing page would only bookmark the landing page. That's why the install steps
send people to the real app URL first, THEN tell them to Add to Home Screen.
(Decision on record: "Option A" — point install at the github.io app for now;
the installed app wears the github.io address. "Option B" — host the app at
evolveapp.cc/app so it installs under the custom domain — was deferred.)

---

## Editing copy

All text is plain in `index.html` / `install.html` — feature names, FAQ answers,
shop list, the footer "Version 1.0". Edit directly; no template layer. Brand
colours/fonts live as CSS variables at the top of `site.css` (copied from the
app), so keep them in sync to keep site and app looking like one product.

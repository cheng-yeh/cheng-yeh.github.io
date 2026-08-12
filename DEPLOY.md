# Deploying to cheng-yeh.github.io

Full replace: the al-folio Jekyll site comes out, the static site goes in.
Everything in `deploy/` becomes the repo root.

## What's in `deploy/`

| File | Purpose |
| --- | --- |
| `index.html` | The whole site, self-contained (~965 KB, gzips to far less) |
| `404.html` | Dark-themed not-found page pointing home |
| `og.png` | 1200×630 social preview card |
| `apple-touch-icon.png` | Home-screen icon on iOS |
| `robots.txt` | Allows all, points at the sitemap |
| `sitemap.xml` | Single-URL sitemap |
| `.nojekyll` | Tells Pages to serve files as-is, no Jekyll build |
| `assets/Cheng-Yeh-Chen-CV.pdf` | Real CV URL people can link to |

## Steps

```bash
git clone git@github.com:cheng-yeh/cheng-yeh.github.io.git
cd cheng-yeh.github.io

# 1. Archive the old site so nothing is lost
git push origin master:al-folio-archive

# 2. Clear the working tree (keeps .git)
git rm -rf .

# 3. Copy in the new site — the CONTENTS of deploy/, not the folder
cp -R /path/to/deploy/. .
cp /path/to/deploy/.nojekyll .          # cp -R skips dotfiles on some shells

# 4. Commit and push
git add -A
git commit -m "Replace al-folio Jekyll site with static site"
git push origin master
```

Then in **Settings → Pages**:

- Source: **Deploy from a branch**
- Branch: **`master`** / **`/ (root)`** — it is currently `gh-pages`

Finally, clean up the old build branch:

```bash
git push origin --delete gh-pages
```

## Why this order matters

Step 2 removes `.github/workflows/deploy.yml`, so the Jekyll Action no longer runs. Workflows execute from the commit you push, and that commit no longer contains it. Do not switch the Pages source before pushing, or the site is briefly empty.

## After it's live

- `https://cheng-yeh.github.io/` — the new site
- `https://cheng-yeh.github.io/assets/Cheng-Yeh-Chen-CV.pdf` — CV
- Old URLs (`/publications/`, `/assets/pdf/cheng_yeh_chen_202605.pdf`, `/news/`, `feed.xml`) will 404 by choice. They stay reachable on the `al-folio-archive` branch if you ever want them back.

## Analytics

The head loads Plausible for `cheng-yeh.github.io`. It does nothing until you create the site at plausible.io — no error, no console noise, just no data. To use Google Analytics instead, replace this line in the source `.dc.html` head:

```html
<script defer data-domain="cheng-yeh.github.io" src="https://plausible.io/js/script.js"></script>
```

with your GA4 snippet, then rebuild the standalone file.

## Changing the site later

Edit `Cheng-Yeh Chen - Site v2.dc.html` in this project, rebuild the standalone bundle, and replace `index.html` in the repo. The bundle is a compiled artifact — never edit `deploy/index.html` by hand.

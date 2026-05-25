# Deploy fresco.cloud landing page

Goal: publish the static landing page live at **https://fresco.cloud** via GitHub Pages
using a custom apex domain. No build step, no dependencies — `index.html` is a single
self-contained file (inline SVG flame + inline CSS).

## Facts
- Working dir: `~/code/fresco.cloud`
- GitHub account: `perazz`  ·  target repo: `fresco.cloud` (PUBLIC)
- Files (repo root): `index.html` (the page) and `CNAME` (contains the single line `fresco.cloud`)
- Source of truth for the files: `/Users/federico/fresco/fresco/resources/branding/site/`
- Domain registrar + DNS: **Aruba**, plan "Dominio con email" (has a DNS management panel;
  email mailboxes live on this domain — **MX records must be preserved**)
- Public repo is intentional and safe: it contains ONLY the marketing page, zero FRESCO source/IP.

## Steps

1. Ensure repo contents. In `~/code/fresco.cloud`, confirm `index.html` and `CNAME` are at the
   repo root. If missing, copy them:
   ```bash
   cp "/Users/federico/fresco/fresco/resources/branding/site/index.html" .
   cp "/Users/federico/fresco/fresco/resources/branding/site/CNAME" .
   ```

2. Commit and create the public repo + push:
   ```bash
   git init 2>/dev/null; git add -A
   git commit -m "fresco.cloud landing page"
   gh repo create fresco.cloud --public --source=. --remote=origin --push
   # if the repo already exists:
   #   git remote add origin git@github.com:perazz/fresco.cloud.git
   #   git push -u origin main
   ```

3. Enable GitHub Pages from the default branch root (the CNAME file auto-sets the custom domain):
   ```bash
   gh api -X POST repos/perazz/fresco.cloud/pages \
     -f 'source[branch]=main' -f 'source[path]=/' 2>/dev/null \
     || echo "Set manually: repo Settings -> Pages -> Deploy from branch: main, folder /(root)"
   ```

4. DNS at Aruba — MANUAL (web UI, hand back to Federico). In the fresco.cloud DNS panel:
   - Apex `@` → replace the parking **A** record with GitHub's four:
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - `www` → **CNAME** → `perazz.github.io`
   - (optional IPv6 AAAA: `2606:50c0:8000::153`, `:8001::153`, `:8002::153`, `:8003::153`)
   - **Leave the MX records untouched** — that is what keeps the fresco.cloud mailbox working.

5. After DNS propagates (minutes–hours): repo Settings → Pages → tick **Enforce HTTPS**
   (GitHub issues a Let's Encrypt cert for the apex automatically once DNS resolves).

6. Verify:
   ```bash
   curl -sI https://fresco.cloud | head -1        # expect: HTTP/2 200
   curl -sI https://www.fresco.cloud | head -1     # should resolve/redirect to apex
   ```

## Cautions
- Never modify/remove the **MX** records — breaks email on fresco.cloud.
- Do not use an Aruba redirect or a masked/frame redirect — GitHub Pages sends
  `X-Frame-Options` and it will break; the custom-domain (CNAME) approach above serves the
  real `fresco.cloud` URL directly with HTTPS.
- Repo name containing a dot (`fresco.cloud`) is valid; with the custom domain the repo name
  is cosmetic only.

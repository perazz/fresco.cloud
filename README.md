# fresco.cloud

Temporary "coming soon" landing page for the **FRESCO** CFD project, served from
GitHub Pages at <https://fresco.cloud>.

The page is a single self-contained `index.html` (inline SVG flame + inline CSS,
no build step, no dependencies). `CNAME` pins the custom apex domain so GitHub
Pages serves the site at `fresco.cloud` rather than `perazz.github.io/fresco.cloud`.

See [`DEPLOY.md`](DEPLOY.md) for the full deployment / DNS runbook.

## Local preview

Open `index.html` directly in a browser, or:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## License

All rights reserved — see [`LICENSE`](LICENSE).

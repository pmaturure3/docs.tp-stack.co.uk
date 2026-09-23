# TP Stack Documentation

Documentation site for TP Stack, built with [MkDocs](https://www.mkdocs.org/) and the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

Published at [https://docs.tp-stack.co.uk](https://docs.tp-stack.co.uk)

## Local development

### Prerequisites

- Python 3.13

### Setup

```bash
python3 -m venv venv
source venv/bin/activate
pip install mkdocs-material
```

### Serve locally

```bash
mkdocs serve
```

Site will be available at http://127.0.0.1:8000

## Project structure

```
docs/
├── index.md
├── tp-stack/
├── connecting/
├── running-jobs/
├── environments/
├── software/
└── stylesheets/
    └── custom.css
mkdocs.yml
```

## Deployment

Deployment is automated via GitHub Actions. Pushing to `main` triggers a build and deploys to GitHub Pages.

The custom domain `docs.tp-stack.co.uk` is configured via a Cloudflare CNAME record pointing to GitHub Pages.

## Contributing

1. Create a branch
2. Add or edit pages under `docs/`
3. Update `nav` in `mkdocs.yml` if adding new pages
4. Test locally with `mkdocs serve`
5. Open a pull request
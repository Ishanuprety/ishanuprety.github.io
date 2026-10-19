# website/ — Ishan Uprety researcher site

Plain static site. **No build step.** HTML + one CSS file (`assets/style.css`).

## Structure
```
website/
├── index.html         Home / hero
├── research.html      Research & About
├── projects.html      Projects (Warren as a research system)
├── blog.html          Blog index
├── contact.html       Contact (placeholders to fill before launch)
├── blog/
│   ├── safety-rails-not-warnings.html
│   └── cost-of-being-safe.html
└── assets/
    └── style.css      Theme (dark + light auto), all styling
```

## Preview locally
```sh
cd website && python3 -m http.server 8000
# http://localhost:8000
```

## Before deploying
1. Fill the placeholders in `contact.html` (email + GitHub/LinkedIn/X links).
2. Deploy per `../DEPLOY.md` (GitHub Pages / Cloudflare Pages / Netlify — all free).

## Notes
- All internal links are **relative**, so the site works both at a domain root and in a subpath
  (e.g. a GitHub Pages project URL).
- Respects `prefers-color-scheme` — looks right in dark and light.
- Brand: serious cybersecurity researcher — automated vulnerability discovery with safety rails.
- Content is public-safe: no private hunt findings, no target-specific detail.

## Blog authoring pipeline (offline, stdlib only)

The blog index, RSS feed, and sitemap are generated from one source of truth:
`content/posts.json`. Never hand-edit the `<ul class="post-list">` in `blog.html` — run the
builder instead.

```sh
# Add a new post (scaffolds the HTML, registers it, rebuilds the index/feed/sitemap):
python3 content/blog.py new --slug my-post --title "My Post Title" --date 2026-10-20 --read-time 8
#   -> writes website/blog/my-post.html (fill in the TODOs), prepends to content/posts.json

# After editing posts.json by hand, or any time, regenerate derived files:
python3 content/blog.py build        # -> blog.html post-list, feed.xml, sitemap.xml

# Verify posts.json and the files on disk agree (use in CI or pre-commit):
python3 content/blog.py check
```

- `content/posts.json` — ordered list of posts (newest first): title, href, date, read_time.
- `content/blog.py` — the pipeline (build / new / check). No network, no third-party deps.
- `website/feed.xml`, `website/sitemap.xml` — generated; do not edit by hand.
- Canonical domain for feed/sitemap URLs is the `BASE_URL` constant at the top of `content/blog.py`.
- The older `content/new_post.py` still works for a bare HTML scaffold, but `content/blog.py new`
  is preferred because it also updates the index.

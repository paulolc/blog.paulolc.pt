# Migration Notes — paulolc.pt: Gatsby/Lumen → Hugo + Decap (prototype, local only)

Status: **local prototype only. Nothing pushed to GitHub, Netlify untouched.**
Date: 2026-11-25

## Hugo version

- Hugo **v0.170.1 extended** (latest stable at build time), official GitHub release
  tarball `hugo_extended_0.170.1_linux-amd64.tar.gz`, binary at `.bin/hugo`
  (`hugo v0.170.1-c8b03de9564cf846a3986e879727d638bb96ab33 linux/amd64`).
- Build: `./.bin/hugo --gc --minify` → **success, ~35–65 ms** (Gatsby took 1m 56s
  on Netlify). 36 pages, 2 paginator pages, 0 errors, 0 warnings after fixes.

## Site configuration (`hugo.toml`)

- Title "The Aspiring Software Craftsman", subtitle "From someone who loves to
  write software", baseURL `https://paulolc.pt/`, author Paulo Carvalho with bio
  and contacts (email plc@sdf.org, twitter paulolc, github paulolc) from
  `config.js` / `content/config.json`.
- Menu: Articles `/`, About me `/pages/about`, Contact me `/pages/contacts`.
- Google Analytics `G-P5VY49XL4L` (from config.js; content/config.json still had
  the older UA-43022919-1 — config.js value used) via a gtag snippet in the base layout.
- Pagination: pagerSize 4 (= postsPerPage).
- RSS: built-in RSS output format renamed with `baseName = "rss"` so the feed is
  **`/rss.xml`** (Hugo default would be `/index.xml`); custom `layouts/_default/rss.xml`
  lists all published posts. Sitemap at `/sitemap.xml` (Hugo default; live Gatsby
  site served it at `/sitemap/sitemap-index.xml` — a redirect for that path can be
  added on Netlify later if desired).
- Taxonomy URLs: Hugo defaults are `/categories/…` and `/tags/…`. Configured via
  `[permalinks.taxonomy]` / `[permalinks.term]` to the live site's singular
  `/category/:slug` and `/tag/:slug`. The old list URLs `/categories` and `/tags`
  are served by two static meta-refresh redirect pages in `static/`.

## Theme / layouts (hand-written, no external theme)

- `layouts/_default/baseof.html` — head, GA, theme colour `hsl(31, 92%, 62%)`.
- `layouts/partials/sidebar.html` — author photo (`/photo.png`), name, bio,
  contacts, menu, copyright — Lumen-like.
- `layouts/index.html` — post list, 4 per page, pagination.
- `layouts/_default/list.html` — section / taxonomy-list / term lists with pagination.
- `layouts/_default/single.html` — post (date, category, tag links) and page rendering.
- `layouts/404.html`, `static/css/style.css` — plain CSS, responsive, orange accent.
- `content/photo.jpg|png` from the old site now live in `static/` so `/photo.jpg`
  and `/photo.png` resolve; `content/pages/book.jpg` also copied to `static/book.jpg`
  (and Hugo publishes it at `/pages/book.jpg` as a section file, as before).

## Content changes (and why)

Content was copied from `github.com/paulolc/blog.paulolc.pt` (`/tmp/blogcheck`)
unchanged in body text, except:

1. **Frontmatter re-serialised as YAML** (PyYAML dump). Cosmetic effect: dates
   render as `2021-01-31 16:40:32.101000+00:00` instead of `…T16:40:32.101Z`;
   Hugo parses both identically.
2. **`category: X` → `categories: [X]`** — Hugo's taxonomy frontmatter key is the
   plural `categories` (same for the existing `tags`, already plural).
3. **Explicit `url:` added to every post** — the old `slug:` values are
   inconsistent (some `/posts/…/`, some bare, two are descriptive sentences).
   `url:` pins each post to its exact live URL (table below). Draft posts keep
   their old slug-derived URLs; they remain `draft: true` and are not published
   (verified: no draft titles in output or sitemap).
4. **Raspberry Pi 4 post:** its `url:` ends with a trailing slash
   (`…Just buy it./`). Without it Hugo treats a URL ending in `.` as a file and
   emits a bare file instead of a directory `index.html`. The path segment is
   otherwise character-for-character the live one.
5. **Image references in published posts:** `](/media/<file>)` → `](media/<file>)`.
   The files live in each post's `media/` subfolder; as Hugo leaf-bundle
   resources they publish to `<post-url>/media/<file>`, so the relative
   reference resolves. Draft starter posts still reference `/media/image-*.jpg`
   files that never existed in the repo — unchanged, and unpublished.
6. **Pages** (`about.md`, `contacts.md`): explicit `url: /pages/about/` /
   `/pages/contacts/` added; bodies unchanged.

Both leaf bundles (`content/posts/<folder>/index.md`) and flat files
(`content/posts/<name>.md`) render — verified with a temporary flat post
(created, built to a scratch destination, confirmed at its URL, then removed).

## URL verification (clean build, `public/` vs live sitemap)

| Live URL | Generated | Result |
|---|---|---|
| `/` | `/index.html`, 4 posts, pager | ✅ |
| `/page/1` | alias redirect page | ✅ |
| (2nd list page) | `/page/2/index.html`, 3 posts | ✅ (live called this `/page/1`) |
| `/geeks-dream-festival` | ✅ + media image | ✅ |
| `/cascade-studio` | ✅ + media image | ✅ |
| `/wslpath` | ✅ | ✅ |
| `/use-tabs-for-indentation-instead-of-spaces` | ✅ | ✅ |
| `/create-a-qr-code-with-your-wifi-config-for-your-guests` | ✅ | ✅ |
| `/HPDL1414 Bubble 4 Digit 16 Segment Display Simplest Breadboard Test Circuit` | ✅ + all 5 media files | ✅ |
| `/If you plan to have an informed decision … Just buy it.` | ✅ (trailing slash, see note 4) | ✅ |
| `/pages/about/`, `/pages/contacts/` | ✅ | ✅ |
| `/category/electronics|jobs|opinion|coding|software|tips` | ✅ all six (+ `/category/` list) | ✅ |
| `/tag/raspberry-pi` | ✅ (+ `/tag/` list) | ✅ |
| `/categories`, `/tags` (live list pages) | static redirects to `/category/`, `/tag/` | ⚠️ redirect, not a native list page |
| `/rss.xml` | ✅, 7 items | ✅ |
| sitemap | `/sitemap.xml`, 21 URLs | ✅ (live path was `/sitemap/sitemap-index.xml`) |
| `/admin/` (new) | Decap `index.html` + `config.yml` | ✅ |

All 7 published posts verified in output HTML: correct title fragment, body
text present, and every `<img>` source resolves to an existing file in
`public/` (HPDL1414: 5 images; Cascade Studio: 1; GIT PUSH LIVE: 1; author
photo on all pages). No broken `/media/…` references. Drafts: 7 draft posts
correctly absent.

Extra generated URLs (harmless, not on the live site): `/posts/` section list,
`/pages/` section list, `/robots.txt`, `/404.html`, `/photo.jpg`, `/book.jpg`.

## Decap CMS (`static/admin/`)

- `static/admin/index.html` — Decap CMS 3 CDN + Netlify Identity widget → `/admin/`.
- `static/admin/config.yml` — backend `git-gateway`, branch `master`,
  `publish_mode: simple`, `local_backend: true` for local testing.
- **Posts collection:** folder `content/posts`, `path: "{{slug}}/index"`,
  `extension: md`, `format: frontmatter`, slug template
  `{{year}}-{{month}}-{{day}}-{{fields.slug}}`, fields: title, slug, url,
  date, description, category (string, kept for parity), categories (list),
  tags (list), draft (boolean, default true), body.
- **Pages collection:** file collection for `content/pages/about.md` and
  `content/pages/contacts.md` (title, url, body).

### Decap limitations (known, documented in config.yml)

1. **Uploads cannot target a per-post leaf-bundle `media/` folder.** Decap has
   one global `media_folder`; configured as `static/media` → `/media/<file>`.
   New images uploaded in /admin land there, not inside the post's bundle.
   Existing bundle media is unaffected. (This incidentally matches the old
   Gatsby posts' original `/media/…` reference style.)
2. **Leaf-bundle creation relies on Decap's `path` option** (`{{slug}}/index`).
   If a future Decap version drops/ignores it, new posts would be created as
   flat `.md` files — Hugo here handles both forms identically, so this is a
   cosmetic difference, not a breakage.
3. Editors must keep the `slug` and `url` fields in sync for new posts
   (hint text is in the form); Hugo uses `url` as authoritative.
4. Decap does not edit site config, menus, or CSS — Git only, by design.

## Exact Netlify changes needed later (NOT done)

In the existing Netlify project `paulolc`
(Site ID 465af81c-02e1-4f05-acee-cc27d4860448, repo
`github.com/paulolc/blog.paulolc.pt`, production branch `master`):

1. Push this Hugo tree to the repo (replacing the Gatsby files on `master`,
   or merge it in), including `netlify.toml` in the repo root.
2. Build settings (or rely on `netlify.toml`, which already encodes them):
   - Build command: `hugo --gc --minify` (was `gatsby build`)
   - Publish directory: `public` (unchanged)
   - Environment: `HUGO_VERSION = 0.170.1`
   - Remove/ignore the old Node.js 16 runtime setting — Hugo needs no Node.
3. For Decap: Netlify UI → Identity: **Enable Identity** (it showed as
   activated 2026-11-25 09:24 in the earlier check — confirm), set registration
   to **Invite only**, invite the editor address; then Identity → Services →
   **Enable Git Gateway**.
4. Optional: add a Netlify redirect `/sitemap/sitemap-index.xml → /sitemap.xml`
   (301) if any external tool still polls the Gatsby sitemap path.

## Unresolved / open

- Visual fidelity is "Lumen-like", not pixel-identical: no dark-mode toggle,
  no Disqus comments (the old config had an empty Disqus shortname, so nothing
  was lost), no syntax-highlighting theme beyond Hugo/Chroma defaults via the
  `github` style setting (no generated CSS classes wired into style.css yet).
- The two static taxonomy-list redirects (`/categories`, `/tags`) are a
  stopgap; native list pages exist at `/category/` and `/tag/`.

## Addendum — connector branch (2026-11-25)

This branch (`hugo-migration`) was created from `master` (05156de) via the
GitHub connector, which can add/update text files but cannot delete files or
push binaries. Therefore the Gatsby files (src/, internal/, package.json,
gatsby-*.ts, content/config.json) are still present in this branch. This was
verified harmless: a local simulation build (master tree + exactly these Hugo
files overlaid) builds with Hugo 0.170.1 in ~54 ms, all live URLs preserved,
/photo.jpg and /photo.png publish automatically from content/, and
/pages/book.jpg publishes from content/pages/. Binary post media already
exists in the repo at the correct bundle paths and is unchanged.
A later cleanup (deleting the Gatsby files) requires a regular git push and
is NOT required for the Hugo build or the Netlify Deploy Preview.

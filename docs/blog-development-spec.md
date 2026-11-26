# Changelog

## Next

- add article counts to the sidebar(s) articles categories/months in a smaller font and darker color (same as the personal blurb)

## Versions

### 1.2

**Released:** 2026/11/26 6:13am

- Articles archive expanded by default on all views: the sidebar archive `<details>` element now carries the `open` attribute.
- Ubuntu is the blog's font: embedded from Google Fonts (preconnect plus the css2 stylesheet, weights 400/500/700 and italic 400, `display=swap`) and placed first in the body font stack, ahead of the system fallbacks, so the blog renders in Ubuntu on every platform instead of each platform's system font.
- Implemented in production as commit `0e99a4c99e5594f0e3c99316291850f69a369e89` and verified live: the `open` attribute and the Ubuntu stylesheet link are present on the home page, an article page and the About page; the production CSS has Ubuntu first in the stack; the Google Fonts stylesheet serves the Ubuntu faces.

### 1.1

**Released:** 2026/11/26 5:48am

- Sidebar always visible, including on individual article pages: the base template no longer switches posts to a separate no-sidebar layout.
- Sidebar Articles links are now filtered, paginated views in the frontpage feed format: category entries land on the category feeds, and month entries land on a new `/archive/<yyyy-mm>/` month archive. Month archive sections are generated at build time by a Hugo content adapter that reads the post files, so a new month appears automatically with its first published post. `/archive/` itself lists all posts paginated, newest first.
- Sidebar archive entries (months and categories) are indented one level further right (30px), so they no longer align with their By date / By category headings.
- Implemented in production as commit `fd200d3c811a08766dbcf3a3fd47253b476a7f38` and verified live (see §17.5).

### 1.0

**Released:** 2026/11/26 4:00am

- the former Gatsby/Lumen site rebuilt with **Hugo 0.170.1 Extended**;
- the existing published articles, pages, categories, tag, RSS feed and public URLs preserved;
- **Decap CMS** restored at `/admin/`;
- Netlify Identity and Git Gateway configured for authenticated editing;
- the Lumen appearance approximated closely, including dark mode;
- eight unpublished article drafts recovered from Joplin and added to the repository;
- a new `/articles/` archive page and an expandable Articles archive in the sidebar;
- production deployment through Netlify from the `master` branch.

# paulolc.pt — Blog Development Specification (As-Built)

**Document status:** As-built specification of the development completed so far
**Version:** 1.2
**Date:** 26 November 2026
**Site:** https://paulolc.pt
**Site title:** *The Aspiring Software Craftsman*
**Production state at last verification:** Live, Hugo-generated, editable through Decap CMS
**Version control:** Tracked in the blog repository at `docs/blog-development-spec.md`; every revision of this specification is committed there.

---

## 1. Purpose

This specification records what was built for paulolc.pt, why it was built, how the system works, what has been verified, and which items remain open or explicitly parked.

The immediate problem was that the old blog could no longer be edited or rebuilt reliably with updated articles. The objective was not a redesign for its own sake. The objective was to preserve the existing content and public URLs while replacing the obsolete publishing machinery with a maintainable static-site system and restoring browser-based editing.

The completed result is:

- the former Gatsby/Lumen site rebuilt with **Hugo 0.170.1 Extended**;
- the existing published articles, pages, categories, tag, RSS feed and public URLs preserved;
- **Decap CMS** restored at `/admin/`;
- Netlify Identity and Git Gateway configured for authenticated editing;
- the Lumen appearance approximated closely, including dark mode;
- eight unpublished article drafts recovered from Joplin and added to the repository;
- a new `/articles/` archive page and an expandable Articles archive in the sidebar;
- filtered, paginated month archives at `/archive/<yyyy-mm>/`, generated automatically at build time (version 1.1);
- production deployment through Netlify from the `master` branch.

---

## 2. Scope

### 2.1 In scope and completed

1. Diagnose why the old site was frozen.
2. Preserve all content that was already public.
3. Replace Gatsby with a currently maintainable static generator.
4. Restore an `/admin/` editing interface.
5. Preserve the old public URL inventory, including malformed legacy slugs.
6. Reproduce the Lumen visual design as closely as practical.
7. Restore dark-mode behaviour compatible with the old site's saved reader preference.
8. Verify the new site first on a Netlify Deploy Preview, then in production.
9. Recover unpublished blog-article material from Joplin as Hugo drafts.
10. Add a date/category archive to the sidebar and a full archive page.
11. Repair Netlify Identity password-reset handling.
12. Reissue the expired Git Gateway token and confirm Decap access.

### 2.2 Not completed

The following were not completed as part of the work described here:

- deletion of the legacy Gatsby files from the Git repository;
- resolution of the second Netlify project named `paulolc-blog`;
- the parked, more exhaustive article-recovery comparison against the material on lifthrasir;
- confirmation that the `plcarvalho@gmail.com` Identity login works end to end;
- migration of one image attached to the Portuguese Joplin draft.

Making the sidebar visible on individual article pages was requested on 26 November 2026, immediately stopped, and then formally queued by Paulo in this document's Changelog “Next” section; it was implemented as version 1.1 (see §12.6 and §14).

---

## 3. Pre-migration diagnosis

### 3.1 Former system

The production site before migration had these characteristics:

| Item | Former state |
| --- | --- |
| Generator | Gatsby 4.25.4 |
| Theme basis | Gatsby Starter Lumen |
| Repository | `github.com/paulolc/blog.paulolc.pt` |
| Repository ID | 189497956 |
| Production branch | `master` |
| Netlify project | `paulolc` |
| Netlify Site ID | `465af81c-02e1-4f05-acee-cc27d4860448` |
| Build command | `gatsby build` |
| Publish directory | `public` |
| Runtime | Node.js 16, already deprecated |
| Last published deploy | 19 November 2023 |
| Last published commit | `05156de` |

The site was frozen rather than visibly broken: the last successful Gatsby deployment remained online, but the old toolchain was no longer a practical basis for publishing new articles.

### 3.2 Missing administration interface

No working `/admin/` existed under the Gatsby build; the path returned 404.

The administration interface Paulo remembered was the former Netlify CMS, now Decap CMS, from an earlier configuration. It had been lost during a later Lumen/Gatsby revert. Restoring editing therefore required adding a CMS layer to the new generator rather than merely fixing a broken page.

### 3.3 Content found in the repository

The repository contained:

- 14 posts in `content/posts/<folder>/index.md` form;
- 7 published posts;
- 7 draft posts;
- About and Contact pages;
- post media in leaf-bundle `media/` folders.

The seven drafts already in the repository were:

- five typography/starter posts dated 2016–2017;
- “Hacking Lego Clone Bluetooth Power Brick” (2020-01-23);
- “The Art of Computer Programming” (dated 2020-06-24; the draft ends mid-sentence).

---

## 4. Principal design decisions

### 4.1 Generator: Hugo

**Decision:** Hugo v0.170.1 Extended.

**Reasons:**

- single-binary toolchain, avoiding the obsolete Node/Gatsby dependency chain;
- native Markdown leaf bundles;
- native taxonomies for categories and tags;
- native RSS, sitemap, pagination and draft handling;
- explicit per-post `url:` values can preserve irregular legacy URLs exactly;
- fast builds — the migration build was measured in tens of milliseconds locally, rather than minutes under the old Gatsby build.

Zola was the other candidate considered. Hugo was selected because its content, taxonomy and URL controls covered the existing site without requiring a URL redesign.

### 4.2 Content preservation over URL normalisation

**Decision:** preserve the live URLs, including two irregular legacy slugs.

This means every migrated published post carries an explicit `url:` in frontmatter. Hugo's folder or slug value alone is not authoritative for public addressing.

The two difficult cases are:

1. the HPDL1414 post, whose URL contains uppercase letters and spaces;
2. the Raspberry Pi 4 post, whose URL is a long sentence beginning “If you plan to have an informed decision…” and ending “Just buy it.”

Netlify normalises the HPDL1414 address to lowercase in some contexts, but the original uppercase form resolves by redirect and does not produce a 404.

### 4.3 CMS: Decap

**Decision:** Decap CMS at `/admin/`, using Netlify Identity and Git Gateway.

**Reasons:**

- it restores the editing model Paulo previously used;
- edits become ordinary Git commits to the same repository;
- browser editing and direct Git editing remain interchangeable;
- no separate content database is introduced.

### 4.4 Theme: reproduce Lumen, do not invent a new design

At Paulo's request, the Hugo templates were rebuilt to approximate the live Lumen site. Design values and structure were extracted from the old site's own HTML and stylesheet rather than guessed.

### 4.5 Preview before production

The migration was pushed first to a `hugo-migration` branch and reviewed through a Netlify Deploy Preview. Production remained on Gatsby until Paulo approved the preview and instructed that it be moved to production.

---

## 5. Current production architecture

```text
Editor
  │
  ├── Browser editing ──► Decap CMS at https://paulolc.pt/admin/
  │                         │
  │                         ├── Netlify Identity: authentication
  │                         └── Git Gateway: authorised Git access
  │
  └── Direct Git editing ─► Markdown files in GitHub
                              │
                              ▼
              GitHub repository: paulolc/blog.paulolc.pt
              Production branch: master
                              │
                              ▼
              Netlify project: paulolc
              Build: hugo --gc --minify
              Publish directory: public
                              │
                              ▼
              Hugo 0.170.1 Extended
              ├── home feed and pagination
              ├── individual posts and pages
              ├── category and tag pages
              ├── /articles/ archive
              ├── /archive/<yyyy-mm>/ month archives (adapter-generated)
              ├── RSS at /rss.xml
              └── sitemap at /sitemap.xml
                              │
                              ▼
              Production site: https://paulolc.pt
```

There is no application server and no content database. Git is the content store; Netlify builds and serves the static output.

---

## 6. Repository and deployment specification

### 6.1 Repository

| Item | Value |
| --- | --- |
| Owner | `paulolc` |
| Repository | `blog.paulolc.pt` |
| URL | `https://github.com/paulolc/blog.paulolc.pt` |
| Production branch | `master` |
| Migration branch | `hugo-migration` |
| Migration pull request | PR #21 |
| Production merge commit | `db14b887dd7e46c1f054b152fc823c880d89ba98` |

### 6.2 Netlify build

The repository's `netlify.toml` specifies:

```toml
[build]
  command = "hugo --gc --minify"
  publish = "public"

[build.environment]
  HUGO_VERSION = "0.170.1"
  HUGO_ENV = "production"
```

A commit to `master` triggers a Netlify production build and deployment. Observed deployment time after the later small changes was approximately one to three minutes.

### 6.3 Repository-tree caveat

The old Gatsby files remain in the repository tree.

The GitHub connector used for the migration could create and update text files but could not delete files or push binaries. Hugo ignores the legacy Gatsby files, so they do not affect the build or the published site. Removing them requires a normal Git clone and push; that cleanup has not been done.

---

## 7. Site configuration

The Hugo configuration is held in `hugo.toml`.

### 7.1 Identity and author data

| Setting | Value |
| --- | --- |
| Site title | The Aspiring Software Craftsman |
| Subtitle | From someone who loves to write software |
| Author | Paulo Carvalho |
| Author photo | `/photo.png` |
| Bio | “Dad. Forever aspiring Software Craftsman. Probable Hacker. Wannabe Maker. Possible Tinkerer. CoderDojo Champion.” |
| Email contact | `plc@sdf.org` |
| Twitter | `paulolc` |
| GitHub | `paulolc` |
| Google Analytics | `G-P5VY49XL4L` |
| Copyright | © All rights reserved. |

Several social links inherited from the old configuration are placeholders (`#`), including Facebook, LinkedIn, Telegram, Instagram and Mastodon targets. They were reproduced for fidelity with the former site rather than silently replaced.

### 7.2 Pagination

- Home feed page size: 4 posts.
- Hugo pagination is enabled.
- `/page/1` resolves as part of the preserved URL inventory.

### 7.3 Taxonomies

Hugo taxonomies are configured as:

- `categories`, published under `/category/<slug>`;
- `tags`, published under `/tag/<slug>`.

The old plural list addresses are retained by static redirects:

- `/categories` → `/category/`;
- `/tags` → `/tag/`.

### 7.4 RSS and sitemap

- RSS is deliberately emitted at `/rss.xml`, not Hugo's default `/index.xml`.
- The production RSS feed contains the 7 published posts.
- The sitemap is emitted at `/sitemap.xml`.
- The former Gatsby sitemap path was `/sitemap/sitemap-index.xml`; adding a redirect for that legacy path was identified as optional and is not recorded as implemented.

### 7.5 Markdown and highlighting

- Goldmark rendering is enabled with `unsafe = true`, preserving the HTML needed by migrated content and provenance comments.
- Syntax highlighting style is configured as `github`.

---

## 8. Content model

### 8.1 Posts

Posts are Hugo leaf bundles:

```text
content/posts/<post-folder>/index.md
```

Media belonging to a post may live in:

```text
content/posts/<post-folder>/media/
```

The principal frontmatter fields are:

```yaml
template: post
title: "..."
slug: "..."
draft: false
date: "..."
description: "..."
socialImage: "..."
url: /authoritative-public-url
categories:
  - "..."
tags:
  - "..."
```

Important rules:

1. `draft: true` excludes the post from the production build.
2. `url:` is authoritative and must be present to preserve or assign the intended public address.
3. In Decap, the editor must keep `slug` and `url` in sync for a new post.
4. Hugo generates ordering, taxonomy pages, RSS and sitemap entries from the post data at build time.

### 8.2 Published posts preserved

| Date | Title | Category | Public URL |
| --- | --- | --- | --- |
| 2021-01-31 | A Vintage “Feel” 16 Segment 4 Digit Display To Use In Your Electronic Creations | Electronics | `/HPDL1414 Bubble 4 Digit 16 Segment Display Simplest Breadboard Test Circuit` |
| 2021-01-21 | Cascade Studio: A Full Live-Scripted CAD Kernel and IDE in the Browser. | Software | `/cascade-studio` |
| 2020-12-30 | wslpath — convert windows paths to wsl equivalents | Tips | `/wslpath` |
| 2019-07-23 | GIT PUSH LIVE | Jobs | `/geeks-dream-festival` |
| 2019-07-23 | Create a QR code with your WIFI config for your guests | Tips | `/create-a-qr-code-with-your-wifi-config-for-your-guests` |
| 2019-07-19 | Use tabs for indentation instead of spaces. | Coding | `/use-tabs-for-indentation-instead-of-spaces` |
| 2019-06-25 | Raspberry Pi 4 is out. Buy it. Now. | Opinion | The long legacy sentence URL beginning `/If you plan to have an informed decision...` |

### 8.3 Pages

The migrated static pages are:

- About: `/pages/about/`;
- Contacts: `/pages/contacts/`.

They are stored under `content/pages/` and are editable through Decap's Pages collection.

### 8.4 Current content totals on `master`

After the Joplin recovery work, the repository content model contains:

| Content type | Count |
| --- | --- |
| Published posts | 7 |
| Draft posts | 15 |
| Total posts | 22 |
| Main static pages | 2 |
| Archive page | 1 (`/articles/`) |

The public site still exposes only the seven published posts. Drafts are present in Git and Decap but return no public page and do not enter RSS.

---

## 9. Recovered Joplin drafts

### 9.1 Source

Blog-article notes were found in Paulo's Joplin data, in the Evernote-import notebook `_EVERNOTE IMPORT/plcarvalho.1`. The relevant notes were named in the form:

```text
[PROJECT][BLOG][ARTICLE] ... - blog.paulolc.pt
```

Their last-updated date was 8 July 2022.

### 9.2 Conversion rule

At Paulo's approval, the notes were converted without editing his text. Each new Hugo draft has:

- `draft: true`;
- date 2022-07-08;
- an explicit `url:`;
- an HTML provenance comment identifying the source Joplin note.

They were pushed directly to `master` in commit:

```text
e3174150fcb42f2f04ab2b08c49f76a8edb8c5e0
```

### 9.3 New drafts created

| Draft | Category | State of source material |
| --- | --- | --- |
| How Do We Learn? | Opinion | Article/ideas text |
| How Thinking Fast and Slow and Stumbling on Happiness Help Understand the World Today | Opinion | Article/ideas text |
| Save Us From the Save Button | Software | Title only; empty body |
| Should I Write Clean Code or Not? | Coding | Title only; empty body |
| The Post Apocalyptic Computer | Opinion | Outline |
| Exciting Times to be a part of Nokia. | Jobs | Opening material |
| Is TDD Dead? | Coding | Research notes |
| “Sê humilde mas não sejas modesto”? | Opinion | Short Portuguese draft |

### 9.4 Knuth material

The Joplin idea “Books — Why The Art of Programming” was not made into a duplicate post. It was appended to the existing repository draft “The Art of Computer Programming” as a marked “Why Knuth” section.

### 9.5 Known omission

The Portuguese draft's Joplin image was not migrated. The draft contains a reference/provenance note for the Joplin resource; the image itself remains outside the blog repository.

### 9.6 Verification

After the drafts were pushed:

- Netlify rebuilt production;
- all new draft URLs returned 404 publicly;
- RSS still contained exactly 7 items;
- the drafts were available only through the repository and Decap.

---

## 10. Decap CMS specification

### 10.1 Location and files

| Item | Location |
| --- | --- |
| Administration page | `/admin/` |
| Decap configuration | `static/admin/config.yml` |
| Published configuration URL | `/admin/config.yml` |
| Backend | Git Gateway |
| Branch | `master` |
| Publishing mode | `simple` |
| Local backend | Enabled in configuration for local Decap-server use |

### 10.2 Posts collection

Decap's Posts collection is configured as:

- folder collection over `content/posts`;
- new entries created as leaf bundles using `path: "{{slug}}/index"`;
- Markdown extension `md`;
- frontmatter format;
- creation enabled;
- preview disabled.

Fields exposed to the editor include:

- Title;
- Slug;
- URL override;
- Publish date;
- Description;
- Category;
- Categories;
- Tags;
- Draft;
- Body.

New posts default to draft state in the form configuration.

### 10.3 Pages collection

Decap's Pages collection is a file collection for:

- `content/pages/about.md`;
- `content/pages/contacts.md`.

Each exposes Title, URL and Body.

### 10.4 Media handling and limitation

Decap is configured with one global upload destination:

```yaml
media_folder: "static/media"
public_folder: "/media"
```

Consequences:

- an image uploaded through Decap is published under `/media/<file>`;
- Decap cannot choose a different per-post leaf-bundle `media/` directory for each article;
- existing post-bundle media remains valid and unaffected;
- an editor who requires bundle-local media must add it through Git or another file process rather than the standard Decap upload control.

### 10.5 Other Decap limitations

1. Decap does not edit Hugo configuration, menus or CSS.
2. The `slug` and `url` fields must be kept consistent manually.
3. If a future Decap version ignored the leaf-bundle `path` setting, it could create flat Markdown files. Hugo can render both forms, so this would be a repository-structure difference rather than a publishing failure.

---

## 11. Netlify Identity and Git Gateway

### 11.1 Intended access model

- Netlify Identity is enabled.
- Registration is **Invite only**.
- Email confirmation is required.
- Git Gateway is enabled for repository `https://github.com/paulolc/blog.paulolc.pt`.
- Identity authenticates the editor; Git Gateway authorises Decap's Git operations.

### 11.2 Identity users

| User | Recorded state |
| --- | --- |
| `plcarvalho@gmail.com` | Already registered as an Identity user on 18 November 2023, a leftover from the former CMS setup; end-to-end login after the repairs is not confirmed. |
| `complexo@gmail.com` | Invited on 25 November 2026 and registered; Paulo confirmed Decap works for this user. |

### 11.3 Password-reset defect and repair

**Defect reported:** a password-reset link opened the site homepage rather than a reset-password form.

**Cause:** Netlify Identity recovery links land on the site root with a `#recovery_token` fragment. Only a page loading the Netlify Identity widget intercepts that token. Before the repair, the widget was effectively available through `/admin/`, but not on the homepage where the link landed.

**Repair, commit&#32;`c3bc1e4`:**

- load `https://identity.netlify.com/v1/netlify-identity-widget.js` in the base template, so it is present on ordinary site pages;
- add the standard handler that forwards a user who logs in through the homepage widget to `/admin/`.

The repair was verified live: the widget and handler are present on the production homepage and on post pages.

A token from an earlier failed reset may be expired or consumed; a fresh reset request is the correct recovery path.

### 11.4 Git Gateway token failure and reissue

**Error shown by Decap:**

```text
Git Gateway Error: Please ask your site administrator to reissue the Git Gateway token.
```

**Cause:** the stored Git Gateway GitHub token dated from the 2023 Netlify CMS setup and was no longer valid.

**Repair performed on 25 November 2026:**

1. Opened Netlify Identity → Services → Git Gateway.
2. Used “Generate access token in GitHub”.
3. Authorised Netlify Auth on GitHub.
4. Saved the newly generated token in Netlify.
5. Verified the saved Git Gateway state in the Netlify UI: correct repository and masked token present.

**Account rule:** the token must be authorised as GitHub user **`paulolc`**, because that account owns the blog repository.

Paulo also has a GitHub account named **`vekkth0r`**. The browser's saved `complexo@gmail.com` GitHub login belongs to `vekkth0r`, not `paulolc`. Authorising Git Gateway as `vekkth0r` was deliberately stopped because that account is not the repository owner.

**Anomaly recorded:** during the successful reissue, GitHub's redirect to Netlify's callback displayed “Invalid state key”. Despite that message, the fresh token reached the Netlify form and was saved. Paulo subsequently confirmed that Decap login works, at least for `complexo@gmail.com`.

---

## 12. Visual design specification

### 12.1 Design basis

The Hugo theme is hand-written; it is not an installed third-party Hugo theme. It reconstructs the former Lumen design from the old live site's own CSS and HTML.

### 12.2 Core tokens

| Token | Light value |
| --- | --- |
| Primary | `#5c92ff` |
| Secondary | `#f7a145` |
| Base text | `#242933` |
| Muted text | `#7f8ba4` |
| Border | `#eaeaeb` |
| Page background | `#ffffff` |
| Code background | `#eaeaeb` |
| Base font | Ubuntu (embedded from Google Fonts; system font stack as fallback) — changed in 1.2; a system font stack in 1.0–1.1 |
| Base size/line height | 16px / 26px |

### 12.3 General layout

- Overall content maximum width: 1070px.
- Non-article pages use the sidebar layout.
- The sidebar has a gradient vertical divider.
- The homepage does not place a large site title/subtitle above the feed; identity is carried by the sidebar.

### 12.4 Sidebar identity block

The sidebar contains, in current order:

1. author photograph — 75px, round;
2. author name — 18px, weight 600;
3. dark-mode toggle beside the author name;
4. author bio/blurb in muted text;
5. social/contact icon buttons immediately below the blurb;
6. navigation menu;
7. copyright line.

The contact icons use SVG paths taken from the former live site. The set is:

- email;
- GitHub;
- Twitter;
- Facebook;
- LinkedIn;
- Telegram;
- Instagram;
- Mastodon.

### 12.5 Home feed

Each feed item follows the Lumen pattern:

- metadata line in the form `MONTH YEAR · CATEGORY`;
- category in orange, uppercase, weight 600;
- title at 27px;
- description;
- blue “Read” link;
- no horizontal separators between posts.

Pagination uses:

- `← PREV`;
- `→ NEXT`;
- bold 26px orange styling for the pagination controls.

### 12.6 Individual article layout

In version 1.0, individual posts used Lumen's centred, no-sidebar layout, with a fixed “All Articles” pill button and the dark-mode toggle beside it.

**Current design (version 1.1):** the sidebar is always visible, including while reading an article. The base template uses the standard sidebar layout for every page, and the article's own styling is retained inside the content column:

- title, 32px, weight 600, centred within the content column;
- body column maximum width 640px, centred within the content column;
- footer containing publication data;
- tag pills;
- author bio box with Twitter link;
- dark-mode toggle in the sidebar, beside the author name, as on every other page.

The former “All Articles” pill and the separate no-sidebar post layout were removed in 1.1 (commit `fd200d3`).

---

## 13. Dark mode

### 13.1 Palette

The dark palette was copied from the former live site's `:root.dark` values:

| Token | Dark value |
| --- | --- |
| Page background | `#242933` |
| Base text | `#ffffff` |
| Border | `#3f485a` |
| Code background | `#191d24` |

Primary and secondary colours remain unchanged.

### 13.2 Preference compatibility

A pre-paint inline script in the base template determines the initial mode before rendering.

It reads the former Lumen localStorage key:

```text
diesel:theme-atom
```

The stored value is JSON of the form:

```json
{"mode":"dark"}
```

or:

```json
{"mode":"light"}
```

Using the same key and format means a reader's preference saved under the old Gatsby site carries across the migration.

If no stored preference exists, the script falls back to the operating system's `prefers-color-scheme` setting.

### 13.3 Toggle locations

The sun/moon toggle appears beside the author name in the sidebar, which since version 1.1 is present on every page, article pages included. (In version 1.0 a second toggle sat beside “All Articles” on article pages; that layout was removed in 1.1.)

Clicking it toggles the `dark` class on the document root and persists the same localStorage key.

Dark mode was pushed before production release as commit `b66fe1d` and verified on the Deploy Preview and production.

---

## 14. Sidebar navigation and archive

### 14.1 Menu order

The current menu order is determined by Hugo menu weights:

| Order | Item | Weight | Target |
| --- | --- | --- | --- |
| 1 | About me | 1 | `/pages/about` |
| 2 | Contact me | 2 | `/pages/contacts` |
| 3 | Articles | 3 | Expandable archive; Articles menu URL remains `/` |

The social/contact icons are above this menu, immediately below the author blurb.

### 14.2 Expandable Articles item

The Articles menu item expands in the sidebar. Its current structure is:

```text
Articles                              16px menu level
    By date                           13px, indented one level
        Jan 2021                      12.5px, indented another level
        Dec 2020
        Jul 2019
        Jun 2019
    By category                       13px, indented one level
        Coding                        12.5px, indented another level
        Electronics
        Jobs
        Opinion
        Software
        Tips
```

Rules implemented at Paulo's direction:

- no article titles in the sidebar archive;
- no “Latest articles” link in the expanded submenu;
- no article counts in the current sidebar presentation;
- grouping by **category**, not tag;
- month labels abbreviated, for example `Jan 2021`;
- each deeper level is indented further;
- each deeper level uses a smaller font size.

### 14.3 Sidebar link destinations

- A category entry links to the existing category page, for example `/category/coding`. Category pages render the filtered posts as a paginated feed in the frontpage format.
- A month-year entry links to that month's archive, `/archive/<yyyy-mm>/` — since version 1.1 also a filtered, paginated feed in the frontpage format (in 1.0 it linked to an anchor on `/articles/`).

Current month archives are:

- `/archive/2021-01/` — 2 posts;
- `/archive/2020-12/` — 1 post;
- `/archive/2019-07/` — 3 posts;
- `/archive/2019-06/` — 1 post.

Month archives are generated at build time by a Hugo content adapter (`content/archive/_content.gotmpl`). The adapter reads the post files under `content/posts/`, ignores drafts, collects the distinct year-months of published posts, and creates one archive section per month (`content/archive/_index.md` is the section root; `layouts/archive/list.html` renders the filtered, paginated feed). `/archive/` itself lists all published posts paginated, newest first. Publishing a post in a new month automatically creates the corresponding sidebar entry and month archive at the next build; no manual step exists.

### 14.4 `/articles/` archive page

The archive page is defined by:

- content stub: `content/articles.md`;
- layout: `layouts/_default/articles.html`;
- public URL: `/articles/`.

Unlike the sidebar, the archive page lists article titles, because it has the wide content column. It remains as a grouped title index alongside the `/archive/` month feeds described in §14.3. It groups them:

1. by year, then month, newest first;
2. by category, with each category heading linking to its category page.

The page also carries the normal sidebar layout.

### 14.5 Sidebar/archive change history

| Commit | Change |
| --- | --- |
| `23cb708` | First expandable sidebar archive: titles grouped by date and tag. |
| `6294406` | Paulo's correction: no titles in sidebar; category instead of tag; month/category links only; `/articles/` page added as the title-list destination. |
| `873dafa` | “Latest articles” and counts removed; two-level indentation and decreasing font sizes; About/Contact moved above Articles; social icons moved immediately below the blurb. |
| `fd200d3` | Version 1.1: sidebar on all pages including articles; month entries link to the new filtered, paginated `/archive/<yyyy-mm>/` month archives (adapter-generated); archive entries indented further right (30px). |

---

## 15. Publishing workflow

### 15.1 Publish through Decap

1. Go to `https://paulolc.pt/admin/`.
2. Log in with a Netlify Identity account.
3. Open **Posts**.
4. Create a new post or edit an existing draft.
5. Complete title, slug, URL, date, description, categories/tags and body.
6. Keep `draft: true` to leave it unpublished, or set it to false to publish.
7. Save. Decap commits the change to `master` through Git Gateway.
8. Netlify builds with Hugo and deploys production.

### 15.2 Publish through Git

1. Add or edit a Markdown leaf bundle under `content/posts/`.
2. Ensure the frontmatter contains the intended explicit `url:`.
3. Set `draft: false` for publication.
4. Commit to `master`.
5. Netlify builds and deploys automatically.

### 15.3 Automatic consequences of publication

When a non-draft post is added, Hugo/Netlify automatically update:

- the home feed and pagination;
- the post page;
- category and tag pages;
- the sidebar month/category archive;
- the `/archive/<yyyy-mm>/` month archive for the post's month (created automatically if it is the month's first published post);
- `/articles/`;
- RSS;
- sitemap.

No manual archive maintenance is required.

---

## 16. Development chronology

All dates below are 25 November 2026 unless stated otherwise.

| Stage | Result |
| --- | --- |
| Diagnosis | Confirmed Gatsby 4.25.4/Lumen, frozen November 2023 production deploy, deprecated Node 16, no working `/admin/`. |
| Migration branch | Created `hugo-migration` from `master` at `05156de`. |
| Initial Hugo migration | Commit `173a255`: Hugo configuration, layouts, Decap files, migrated content and `netlify.toml`. |
| Preview | Draft PR #21 and Netlify Deploy Preview created; Hugo output and URL inventory verified live. |
| Lumen theme | Commit `477d0b9`: design tokens and structure extracted from the live Gatsby site and rebuilt in Hugo templates. |
| Dark mode | Commit `b66fe1d`: Lumen dark palette, compatible localStorage key and toggles added. |
| Production approval | Paulo approved the preview and instructed that it be moved to production. |
| Production merge | PR #21 merged to `master`; merge commit `db14b88`. Production verified as Hugo 0.170.1. |
| Joplin drafts | Commit `e317415`: eight recovered drafts added; Knuth material appended to the existing TAOCP draft. |
| Password-reset repair | Commit `c3bc1e4`: Identity widget loaded site-wide. |
| Git Gateway repair | Dead 2023 token reissued through GitHub as `paulolc` and saved in Netlify; Decap later confirmed working for `complexo@gmail.com`. |
| Sidebar archive v1 | Commit `23cb708`: expandable Articles archive with titles, grouped by date and tag. |
| Sidebar archive correction | Commit `6294406`: titles removed from sidebar; grouping changed to category; `/articles/` added. |
| Sidebar layout refinement | Commit `873dafa`: current menu order, icon placement, indentation and font-size hierarchy. |
| Stopped change, 26 Nov | “Sidebar always visible, even while reading articles” was requested and immediately stopped by Paulo. Not implemented at that time. |
| Version 1.1, 26 Nov | Paulo queued the stopped sidebar change, the filtered month/category views and the archive indentation fix in this specification's Changelog “Next” section. Implemented as commit `fd200d3`, released as spec/site version 1.1 and verified in production (§17.5). |

---

## 17. Verification record

### 17.1 Migration simulation

Before the production push, the exact intended repository state was simulated locally by overlaying the Hugo files on the full existing `master` tree. The simulation:

- built successfully with Hugo;
- produced no build errors;
- generated all checked legacy URLs;
- produced an RSS feed with 7 items;
- excluded drafts from public output.

### 17.2 Deploy Preview verification

The Netlify Deploy Preview was checked directly, not inferred from build logs. Verified items included:

- Hugo 0.170.1 generator marker;
- correct title, subtitle and author sidebar;
- Google Analytics ID;
- HTTP 200 for home, About, RSS, author photo, `/admin/` and `/admin/config.yml`;
- posts, categories and tags resolving;
- the new theme CSS and structure;
- dark-mode toggle and dark CSS.

### 17.3 Production verification after merge

Production was swept after PR #21 was merged. HTTP 200 was recorded for:

- all seven published posts, including the uppercase HPDL1414 address;
- `/pages/about/` and `/pages/contacts/`;
- category and tag pages;
- `/rss.xml`, with 7 items;
- `/sitemap.xml`;
- `/admin/` and `/admin/config.yml`;
- author photo files;
- production CSS;
- `/page/1`.

### 17.4 Latest recorded live check

A further production check on 26 November 2026 returned:

| Check | Result |
| --- | --- |
| `/` | HTTP 200 |
| `/articles/` | HTTP 200 |
| `/pages/about/` | HTTP 200 |
| `/pages/contacts/` | HTTP 200 |
| `/rss.xml` | HTTP 200; 7 items |
| `/sitemap.xml` | HTTP 200 |
| `/admin/` | HTTP 200 |
| `/admin/config.yml` | HTTP 200 |
| `/category/coding` | HTTP 200 after trailing-slash resolution |
| `/category/tips` | HTTP 200 after trailing-slash resolution |
| `/tag/raspberry-pi` | HTTP 200 after trailing-slash resolution |
| Generator | Hugo 0.170.1 |
| Sidebar archive markers | Present |
| About/Contact menu entries | Present |
| Identity widget marker | Present |
| `/articles/` anchors | `january-2021`, `december-2020`, `july-2019`, `june-2019` |

### 17.5 Version 1.1 verification

The 1.1 changes were simulated locally before pushing (post page carries the sidebar; each month archive contains exactly its month's posts; category feed paginated; `/archive/` paginates to a second page) and then verified in production after commit `fd200d3` deployed:

| Check | Result |
| --- | --- |
| Article page (`/cascade-studio/`) | Sidebar present; former post-layout absent |
| `/archive/2021-01/` | HTTP 200; 2 feed items |
| `/archive/2019-07/` | HTTP 200; 3 feed items |
| `/archive/` | HTTP 200; first page of the all-posts feed |
| `/archive/page/2/` | HTTP 200 |
| Homepage sidebar month links | `/archive/2019-06/`, `/archive/2019-07/`, `/archive/2020-12/`, `/archive/2021-01/` |
| Production CSS | Archive entry indent 30px present; post-layout rules absent |

---

## 18. Operational rules and cautions

### 18.1 Git Gateway account rule

Never authorise a replacement Git Gateway token as `vekkth0r` for this repository. The token must belong to `paulolc`, the repository owner.

### 18.2 URL rule

Do not remove or casually change a post's explicit `url:`. It is the mechanism that preserves the public address, especially for legacy posts whose folder/slug does not resemble the live URL.

### 18.3 Draft rule

A post with `draft: true` is not public. Changing it to `false` is the publication action, whether done in Decap or Git.

### 18.4 Hugo version rule

Production is pinned to Hugo 0.170.1 through `netlify.toml`. Any Hugo upgrade should be treated as a code change: build locally or on a preview, check the URL inventory, RSS, taxonomies, archive and Decap configuration, and only then merge.

### 18.5 Theme-preference rule

Do not rename the `diesel:theme-atom` localStorage key without a migration reason. Its compatibility is what preserved existing readers' dark/light choices from the Gatsby site.

### 18.6 Identity-token rule

Password-reset and invite links are time-sensitive. If a link lands on the homepage without acting, first confirm the current page contains the Identity widget, then request a fresh link rather than reusing a possibly consumed token.

---

## 19. Known limitations and open items

### 19.1 Legacy Gatsby files remain in Git

**Impact:** repository clutter only; no production impact.
**Required cleanup:** use a normal Git clone, delete the obsolete Gatsby tree, test the Hugo build, and push the deletion commit.

### 19.2 Second Netlify project: `paulolc-blog`

A second Netlify project with this name is also connected to the repository and reacted to the migration pull request. Its purpose and served domain were not established during the migration.

**Open decision:** inspect it and either document its purpose or pause/disable it if abandoned.

### 19.3 `plcarvalho@gmail.com` login not confirmed

Decap is confirmed working for `complexo@gmail.com`. The `plcarvalho@gmail.com` Identity account existed before the migration, but a complete post-repair login has not been confirmed.

**Next check:** request a fresh password reset if necessary and complete one login at `/admin/`.

### 19.4 Parked lifthrasir article recovery

A read-only inventory of Paulo's older blog material on lifthrasir found no additional article titles beyond the repository set. The deeper work — extracting an old tarball and diffing same-named files for in-place edits — was parked at Paulo's instruction.

No changes were made to the lifthrasir filesystem during the inventory.

**Resume condition:** Paulo's fresh explicit instruction.

### 19.5 Joplin image not migrated

One image belonging to the Portuguese Joplin draft remains outside the repository.

**Impact:** that draft is textually present but visually incomplete.

### 19.6 Two recovered drafts are titles only

“Save Us From the Save Button” and “Should I Write Clean Code or Not?” had no body text in their source notes. They are placeholders for articles still to be written.

### 19.7 Article pages have no sidebar — resolved in 1.1

In version 1.0 article pages used the former Lumen no-sidebar layout. Paulo first requested the always-visible sidebar on 26 November 2026 and immediately stopped it, then queued it in the Changelog “Next” section; version 1.1 implemented it. The sidebar is now present on article pages, and this item is closed.

### 19.8 Decap media placement

Decap uploads cannot be directed into each post's own `media/` bundle. They use the global `/media/` path described in §10.4.

---

## 20. Source and working-file map

| Material | Location |
| --- | --- |
| Production repository | `github.com/paulolc/blog.paulolc.pt` |
| Local Hugo working tree | `~/workspace/paulolc-hugo/` |
| Migration notes in the working tree | `~/workspace/paulolc-hugo/MIGRATION_NOTES.md` |
| Original connector push source | `~/workspace/blog-push-source/` |
| Recovered-drafts push source | `~/workspace/blog-drafts-source/` |
| Later fixes push source | `~/workspace/blog-fixes-source/` |
| Local simulation tree used before pushes | `~/workspace/blog-sim/` |
| Abandoned messy local clone — do not use as a source | `~/workspace/blog-repo/` |

The local working trees are development records. The authoritative production source is the GitHub repository's `master` branch, and the authoritative rendered state is the deployed site at paulolc.pt.

---

## 21. Definition of the current state

As of 26 November 2026, paulolc.pt is considered to be in the following state:

- **Publishing:** restored and working.
- **Generator:** Hugo 0.170.1 Extended.
- **Editing:** Decap at `/admin/`, backed by Netlify Identity and Git Gateway.
- **Content:** 7 published posts, 15 drafts, About and Contact pages.
- **Design:** Lumen approximation with light and dark modes.
- **Navigation:** sidebar on every page, articles included (version 1.1).
- **Archive:** expandable sidebar Articles archive (expanded by default since 1.2), `/articles/` title index, and filtered, paginated month archives under `/archive/<yyyy-mm>/`.
- **Font:** Ubuntu, embedded from Google Fonts (since 1.2).
- **Deployment:** automatic Netlify production build from `master`.
- **Blocking defects:** none known.
- **Non-blocking open items:** legacy-file cleanup, second Netlify project, one unconfirmed Identity login, one unmigrated Joplin image, and the parked lifthrasir comparison.
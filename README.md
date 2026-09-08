# Cathedral Baptist — SEO cutover plan for Grok agent

**Paste this to Will's Grok agent:**

> Read https://github.com/lquessenberry/cathedralbaptist-seo-cutover-plan and implement it. Keep `www.cathedralbaptist.com` as the canonical host. Preserve the identity URL map. Add `robots.txt` + `sitemap.xml` from this plan, 301 WordPress leftovers, noindex `/leader/`, and 301 `.net` (and apex `.com`) to `https://www.cathedralbaptist.com` with the path preserved. Do **not** 301 `.com` to `.net`. This is a same-domain redesign, not a site move.

---

**Church:** Cathedral Baptist Church, Jonesboro, AR  
**New site (live now):** https://cathedralbaptist.net/  
**Old site (WordPress, still live):** https://www.cathedralbaptist.com/  
**Goal:** Point `cathedralbaptist.com` DNS at the new static Apache site without SEO interruption.  
**Scan date:** 2026-09-08

---

## 0. Agent instructions

You are implementing a **same-domain redesign**, not a site move.

1. Do **not** 301 `cathedralbaptist.com` → `cathedralbaptist.net`. That would split ranking equity.
2. After DNS, `www.cathedralbaptist.com` is the canonical host (that is what Google already has).
3. New-site pretty paths already match old WordPress permalinks. Preserve them.
4. Add `robots.txt`, `sitemap.xml`, leftover 301s, host canonicalization, and `noindex` on `/leader/`.
5. Verify live after files are on the host. Do not declare done from code alone.

If the site source lives in a git repo, commit the files there. If it is a static tree on Apache, write them into the document root.

---

## 1. Situation (verified live)

| Fact | Detail |
|---|---|
| New stack | Static HTML on Apache. `/path/` serves `/path/index.html`. Bare `/path` 301s to `/path/`. |
| Old stack | WordPress + Yoast. Apex `cathedralbaptist.com` 301s to `www.cathedralbaptist.com`. |
| New sitemap | `https://cathedralbaptist.net/sitemap.xml` → **404** |
| New robots | `https://cathedralbaptist.net/robots.txt` → **404** |
| New canonical tags | **None** on sampled pages (home, doctrine, expect, about, media) |
| New www vs apex | Both `cathedralbaptist.net` and `www.cathedralbaptist.net` return **200** (duplicate host) |
| Old Yoast sitemap | `https://www.cathedralbaptist.com/sitemap_index.xml` → `page-sitemap.xml` only. No real posts. |
| Favicon | `https://cathedralbaptist.net/favicon.ico` → **404** |

**Implication:** When `.com` DNS lands on the new Apache box, indexed URLs like `/doctrine/` will 200 with equivalent content. Rankings survive if hostname stays `www.cathedralbaptist.com` and those paths stay live.

---

## 2. Old WordPress URLs (Yoast page-sitemap.xml)

All of these currently 200 on www.cathedralbaptist.com:

| Old path | Title (old) |
|---|---|
| `/` | Cathedral Baptist Jonesboro is Connecting Jesus to our Community |
| `/expect/` | Expect |
| `/about/` | About |
| `/doctrine/` | Doctrine and core beliefs |
| `/typical-week/` | A typical Week |
| `/media/` | Media |
| `/sermon-archive/` | Sermon Archive |
| `/calendar/` | Calendar |
| `/cathedral/` | Weekly schedule / welcome |
| `/contact/` | Contact |
| `/sample-page/` | WP leftover Sample Page |

Also live but not worth ranking:

| Old path | Notes |
|---|---|
| `/news/` | Empty posts archive. Yoast post-sitemap only lists this URL. |
| `/feed/` | RSS |
| `/index.php` | Already 301s to `/` |
| `/wp-login.php`, `/wp-admin/`, `/wp-json/` | Drop |
| `/wp-content/uploads/...` | Old images. Will 404 unless copied or redirected. |

Probed and **404 on old site** (do not invent redirects from these): `/prayer/`, `/safety/`, `/photos/`, `/leader/`, `/give/`, `/events/`, `/youth/`, `/awana/`, `/beliefs/`, `/watch/`, `/live/`.

---

## 3. New site public inventory (crawled 2026-09-08)

Pretty URLs all 200 on cathedralbaptist.net (no `index.html` required):

| Path | Title |
|---|---|
| `/` | Cathedral Baptist Church \| Jonesboro, AR |
| `/expect/` | What to Expect |
| `/about/` | About Us |
| `/doctrine/` | Doctrine |
| `/typical-week/` | A Typical Week |
| `/typical-week/sunday-school/` | Sunday School |
| `/typical-week/awana/` | AWANA |
| `/typical-week/youth/` | Youth |
| `/typical-week/womens-bible-study/` | Women's Fellowship |
| `/typical-week/mens-fellowship/` | Men's Fellowship |
| `/typical-week/adult-bible-study/` | Wednesday Night Meal and a Message |
| `/typical-week/faith-bible-institute/` | Faith Bible Institute |
| `/typical-week/griefshare/` | GriefShare |
| `/typical-week/food-clothing-closet/` | Food and Clothing Closet |
| `/media/` | Watch |
| `/sermon-archive/` | Sermon Archive |
| `/calendar/` | Calendar |
| `/cathedral/` | Weekly Schedule |
| `/contact/` | Contact & Visit |
| `/prayer/` | Prayer Wall |
| `/safety/` | Protecting our Family |
| `/safety/weather/` | Inclement Weather Policy |
| `/safety/equipment/` | Safety Equipment Locations |
| `/photos/` | Pictures (gallery; optional in sitemap) |
| `/leader/` | Leader Login — **noindex, keep out of sitemap** |

---

## 4. URL map to implement

After DNS, these resolve on `https://www.cathedralbaptist.com`.

### Identity (no rewrite — path must 200)

```
/                              → /
/expect/                       → /expect/
/about/                        → /about/
/doctrine/                     → /doctrine/
/typical-week/                 → /typical-week/
/media/                        → /media/
/sermon-archive/               → /sermon-archive/
/calendar/                     → /calendar/
/cathedral/                    → /cathedral/
/contact/                      → /contact/
```

### 301 leftovers (old WP only)

```
/sample-page/                  → /
/news/                         → /
/feed/                         → /
/comments/feed/                → /
/index.php                     → /
```

### New-only (no old URL; include in sitemap)

```
/prayer/
/safety/
/safety/weather/
/safety/equipment/
/typical-week/sunday-school/
/typical-week/awana/
/typical-week/youth/
/typical-week/womens-bible-study/
/typical-week/mens-fellowship/
/typical-week/adult-bible-study/
/typical-week/faith-bible-institute/
/typical-week/griefshare/
/typical-week/food-clothing-closet/
```

### Host aliases (after DNS cutover)

```
http://cathedralbaptist.com/*              → 301 https://www.cathedralbaptist.com/$1
https://cathedralbaptist.com/*             → 301 https://www.cathedralbaptist.com/$1
http://www.cathedralbaptist.com/*          → 301 https://www.cathedralbaptist.com/$1
http://cathedralbaptist.net/*              → 301 https://www.cathedralbaptist.com/$1
https://cathedralbaptist.net/*             → 301 https://www.cathedralbaptist.com/$1
http://www.cathedralbaptist.net/*          → 301 https://www.cathedralbaptist.com/$1
https://www.cathedralbaptist.net/*         → 301 https://www.cathedralbaptist.com/$1
```

Preserve path and query string. Do not flatten everything to homepage.

### Do not 301

- `/leader/` — leave live, `noindex,nofollow`
- `/photos/` — live gallery; sitemap optional
- Old `/wp-content/uploads/*` — copy assets if inbound links matter; otherwise let 404. Do not blanket-redirect all images to homepage.

---

## 5. Files to add on the new host document root

### 5.1 `robots.txt`

```
User-agent: *
Allow: /
Disallow: /leader/

Sitemap: https://www.cathedralbaptist.com/sitemap.xml
```

Until DNS actually serves `.com` from this host, a temporary `Sitemap: https://cathedralbaptist.net/sitemap.xml` is acceptable, then switch the loc host to www.cathedralbaptist.com at cutover. Prefer writing the `.com` URLs now if cutover is imminent.

### 5.2 `sitemap.xml`

Do **not** include `/leader/`, `/photos/` (unless you want the gallery indexed), `/sample-page/`, or `/news/`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url><loc>https://www.cathedralbaptist.com/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/expect/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/about/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/doctrine/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/sunday-school/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/awana/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/youth/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/womens-bible-study/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/mens-fellowship/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/adult-bible-study/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/faith-bible-institute/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/griefshare/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/typical-week/food-clothing-closet/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/media/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/sermon-archive/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/calendar/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/cathedral/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/contact/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/prayer/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/safety/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/safety/weather/</loc></url>
  <url><loc>https://www.cathedralbaptist.com/safety/equipment/</loc></url>
</urlset>
```

### 5.3 Apache leftovers + host canonicalization

If the vhost already has HTTPS, add to `.htaccess` in docroot (or the vhost config):

```apache
RewriteEngine On

# Force https + www on the canonical host
RewriteCond %{HTTP_HOST} !^www\.cathedralbaptist\.com$ [NC]
RewriteCond %{HTTP_HOST} ^(www\.)?(cathedralbaptist\.com|cathedralbaptist\.net)$ [NC]
RewriteRule ^ https://www.cathedralbaptist.com%{REQUEST_URI} [L,R=301]

RewriteCond %{HTTPS} !=on
RewriteCond %{HTTP_HOST} ^www\.cathedralbaptist\.com$ [NC]
RewriteRule ^ https://www.cathedralbaptist.com%{REQUEST_URI} [L,R=301]

# Old WordPress leftovers
Redirect 301 /sample-page/ /
Redirect 301 /news/ /
Redirect 301 /feed/ /
Redirect 301 /comments/feed/ /
```

**Do not enable the host-alias 301s until `.com` DNS actually points at this host.** Enabling `cathedralbaptist.net` → `.com` too early is fine (net is the staging hostname). Enabling `.com` → www on the *new* host before DNS flips will not affect the old WordPress box.

If `.htaccess` is not allowed, use the equivalent `Redirect` / `Rewrite` directives in the Apache vhost.

### 5.4 Canonical tags

On every public HTML page, inside `<head>`:

```html
<link rel="canonical" href="https://www.cathedralbaptist.com{PATH}">
<meta property="og:url" content="https://www.cathedralbaptist.com{PATH}">
```

`{PATH}` is the pretty path with trailing slash, e.g. `/doctrine/`.

On `/leader/index.html` only:

```html
<meta name="robots" content="noindex, nofollow">
```

Until DNS flips, canonicals pointing at `.com` while `.com` still serves WordPress is slightly early but correct for the destination. If cutover is more than a few days out, use `https://cathedralbaptist.net{PATH}` now and switch at flip. Prefer switching at flip if you can do one deploy.

### 5.5 Favicon

Add `/favicon.ico` (and a `<link rel="icon">` if the HTML does not already reference one). Current 404 is noisy in crawls, not a ranking killer.

---

## 6. DNS / Search Console (human + agent)

Agent: document these; do not change DNS unless asked.

1. Point `cathedralbaptist.com` and `www.cathedralbaptist.com` A/CNAME/AAAA at the **new Apache host** (the one currently serving `.net`).
2. Keep `.net` as an alias that 301s to `https://www.cathedralbaptist.com` + same path.
3. In Google Search Console, keep the existing **`www.cathedralbaptist.com`** property. Do **not** use Change of Address (same domain).
4. After DNS + sitemap are live, submit `https://www.cathedralbaptist.com/sitemap.xml`.
5. Request indexing on the homepage and the 10 old identity URLs if they lag.
6. Leave Facebook / YouTube / GBP NAP links on `cathedralbaptist.com` as they are; they become correct once DNS flips.

---

## 7. Implementation order

1. Add `robots.txt` and `sitemap.xml` on the new host (can go live on `.net` immediately).
2. Add leftover 301s (`/sample-page/`, `/news/`, `/feed/`).
3. `noindex` `/leader/`.
4. Canonical + og:url on public pages (at or just before DNS).
5. Host-alias 301s: `.net` → `www.cathedralbaptist.com` can go live anytime; `.com` www/https rules live on the new vhost for when DNS arrives.
6. Favicon.
7. Human flips DNS.
8. Verify checklist below against `https://www.cathedralbaptist.com`.
9. Submit sitemap in Search Console.

---

## 8. Verification checklist (run after files land, again after DNS)

Use curl. Expect:

```
# Pretty paths still 200 (identity map)
curl -sI https://www.cathedralbaptist.com/doctrine/          # 200
curl -sI https://www.cathedralbaptist.com/expect/            # 200
curl -sI https://www.cathedralbaptist.com/typical-week/      # 200
curl -sI https://www.cathedralbaptist.com/media/             # 200
curl -sI https://www.cathedralbaptist.com/contact/           # 200
curl -sI https://www.cathedralbaptist.com/cathedral/         # 200

# Leftovers
curl -sI https://www.cathedralbaptist.com/sample-page/       # 301 → /
curl -sI https://www.cathedralbaptist.com/news/              # 301 → /

# SEO files
curl -sI https://www.cathedralbaptist.com/robots.txt         # 200
curl -sI https://www.cathedralbaptist.com/sitemap.xml        # 200

# Leader hidden
curl -s https://www.cathedralbaptist.com/leader/ | grep -i 'noindex'

# Host aliases (after DNS)
curl -sI https://cathedralbaptist.net/doctrine/              # 301 → https://www.cathedralbaptist.com/doctrine/
curl -sI https://cathedralbaptist.com/doctrine/              # 301 → https://www.cathedralbaptist.com/doctrine/
```

Also open the 10 identity URLs in a browser and confirm content, not just status codes.

---

## 9. Out of scope unless asked

- Redesigning pages or changing slugs
- Migrating WordPress `/wp-content/uploads` unless a specific inbound image is important
- Change of Address in Search Console
- 301 of the whole `.com` domain to `.net`
- Indexing `/leader/` or `/photos/`

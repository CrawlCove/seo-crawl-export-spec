# CSV column reference

The Crawl Cove desktop app's Reports page can also export the full crawl as
CSV (RFC 4180: comma-separated, `\r\n` line endings, UTF-8 with a BOM so Excel
opens it correctly). It contains the same page set as the
[JSON export](../schema/crawl-export.schema.json), every JSON field, and nine
CSV-only columns (marked below). This is the order the app writes from
v1.3.18 (38 columns):

**Read columns by header name, not by position.** Column 27 (`Schema types`)
was inserted mid-row in app v1.3.14, which moved `hreflang count`,
`Content fingerprint` and `Findings` one place to the right. From v1.3.16 on,
new columns are only added at the end; see [versioning.md](versioning.md).

| # | Column | Type | Notes |
|---|---|---|---|
| 1 | `URL` | string | The URL as requested. |
| 2 | `Final URL` | string | The URL after following redirects. Empty if not recorded. |
| 3 | `Status` | integer | HTTP status code of the final response. Empty if the request never got one. |
| 4 | `Content-Type` | string | Response `Content-Type`. Empty if absent. |
| 5 | `Depth` | integer | Crawl depth from the start URL (0 = start URL). |
| 6 | `Indexable` | `yes` / `no` | HTTP 200 and not noindex, by robots meta OR `X-Robots-Tag`. |
| 7 | `Title` | string | `<title>` contents. Empty if absent. |
| 8 | `Title length` | integer | Character count of `Title`. Empty if `Title` is empty. |
| 9 | `Meta description` | string | Empty if absent. |
| 10 | `Meta length` | integer | Character count of `Meta description`. Empty if empty. |
| 11 | `Canonical` | string | `<link rel="canonical">` href. Empty if absent. |
| 12 | `HTML lang` | string | `<html lang>` attribute. Empty if absent. |
| 13 | `Robots meta` | string | Raw `<meta name="robots">` content. Empty if absent. |
| 14 | `X-Robots-Tag` | string | Raw response header. Empty if absent — the other source of a noindex. |
| 15 | `H1 count` | integer | Number of `<h1>` elements. |
| 16 | `Word count` | integer | Visible body word count. |
| 17 | `Internal links` | integer | Count of links to the same site. |
| 18 | `External links` | integer | Count of links to other sites. |
| 19 | `Images` | integer | Total `<img>` count. |
| 20 | `Images missing alt` | integer | Count of images with an empty or absent `alt`. |
| 21 | `Response time (ms)` | integer | Time to first byte / fetch completion. Empty if not recorded. |
| 22 | `Byte size` | integer | Response body size in bytes. Empty if not recorded. |
| 23 | `Rendered` | `yes` / `no` | Whether the page was fetched with a JS-rendering pass rather than the plain HTTP response. |
| 24 | `Redirect hops` | integer | Number of redirect hops the URL went through (0 = none). |
| 25 | `Fetch error` | string | Network/fetch-level error (e.g. a timeout code). Empty if the request completed. |
| 26 | `Schema blocks` | integer | Count of JSON-LD schema.org blocks on the page. |
| 27 | `Schema types` | string | CSV only, since v1.3.14. Distinct schema.org types on the page, `; `-separated: JSON-LD `@type`s first, then microdata `itemtype`s not already listed. Empty if none. |
| 28 | `hreflang count` | integer | Count of `<link rel="alternate" hreflang>` entries. Column 27 before v1.3.14. |
| 29 | `Content fingerprint` | string | 64-bit content fingerprint as hex, or empty when the page had too little content to fingerprint. Column 28 before v1.3.14. |
| 30 | `Findings` | integer | Number of SEO findings attributed to this page in the run. Column 29 before v1.3.14. |
| 31 | `Inlinks` | integer | CSV only, since v1.3.16. Crawled pages that link to this URL. |
| 32 | `OG title` | string | CSV only, since v1.3.16. `<meta property="og:title">` content. Empty if absent. |
| 33 | `OG description` | string | CSV only, since v1.3.16. `og:description` content. Empty if absent. |
| 34 | `OG image` | string | CSV only, since v1.3.16. `og:image` content. Empty if absent. |
| 35 | `Twitter card` | string | CSV only, since v1.3.16. `<meta name="twitter:card">` content. Empty if absent. |
| 36 | `Googlebot meta` | string | CSV only, since v1.3.16. Raw `<meta name="googlebot">` content. Empty if absent. |
| 37 | `Fetched at` | string | CSV only, since v1.3.16. When the crawler fetched the page, ISO 8601 in UTC (`2026-11-13T02:27:26.595Z`). Empty if not recorded. |
| 38 | `Redirect statuses` | string | CSV only, since v1.3.18. The status code of each redirect hop, in order, joined with `; ` (`301`, `301; 302`). Empty when the URL did not redirect. With `Final URL`, `Status` and `Redirect hops` it is a redirect check for a site move: crawl the old URLs as a list and read one row per URL. |
## Optional columns after column 38

Some crawls add columns after `Redirect statuses`, always in this order, so
column 38 and everything before it never move. Read these by header name.

1. **Find text on pages**, one per phrase set on the crawl: `Contains "<phrase>"`,
   the number of times the phrase appears in the page's visible text. Empty when
   the page was not searched (not HTML).
2. **Extract from pages**, one per selector: `Extract "<selector>"`, the values
   found joined with ` | `. Empty when nothing matched or the row is not HTML.
3. **Search Console**, since v1.3.20, only when the site has Search Console data.
   Four columns over the 28 days ending on the latest synced day:

| Column | Type | Notes |
| --- | --- | --- |
| `Search clicks (28 days)` | integer | Clicks Search Console reports for the URL. `0` when Google did not show it in the window. |
| `Search impressions (28 days)` | integer | Impressions, as above. |
| `Search CTR % (28 days)` | number | Clicks / impressions as a percent, 2 decimal places. Empty with no impressions. |
| `Search position (28 days)` | number | Average position weighted by impressions, 1 decimal place. Empty with no impressions. |

URLs are matched the way Search Console groups a property: http and https,
`www.` and a trailing slash count as one URL. A row that redirects is left
empty, so the destination's clicks are not counted twice. The JSON export does
not carry these columns.

## Differences from the JSON export

CSV has no native boolean or null, so two fields are re-encoded on the way
out — the JSON export is the source of truth for these:

- `Indexable` and `Rendered` (JSON: real booleans) become the strings `yes`
  / `no`.
- Every `null` field (JSON: `null`) becomes an **empty cell** in CSV — there
  is no way to tell "empty string" apart from "absent" in a CSV cell, unlike
  JSON where `""` and `null` are distinct (see `contentFingerprint`, which is
  `""` — not absent — when a page had too little content to fingerprint).

A cell whose content would otherwise parse as a spreadsheet formula (leading
`=`, `+`, `-`, `@`, tab, or CR) is prefixed with `'` so Excel/Sheets/LibreOffice
treats it as text rather than executing it. Number columns are never
prefixed, so this can only affect the text columns, which carry content from
a third-party page (`URL`, `Title`, `Meta description`, the OG and robots
fields, and so on).

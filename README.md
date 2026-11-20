<a href="https://crawlcove.com/download?utm_source=github&utm_medium=readme&utm_campaign=seo-crawl-export-spec"><img src="https://raw.githubusercontent.com/CrawlCove/.github/main/banners/seo-crawl-export-spec.svg" width="1200" alt="seo-crawl-export-spec: An open format for site crawl data: JSON Schema and CSV column reference, with a real sample export and a validator script." /></a>

# seo-crawl-export-spec

An **open SEO crawl export format**: the JSON Schema and CSV column reference for [Crawl Cove](https://crawlcove.com/?utm_source=github&utm_medium=seo-crawl-export-spec)'s site crawl export. Validate an export, script against it, or build an integration on top of it without reverse-engineering the file.

## What's here

- **[`schema/crawl-export.schema.json`](schema/crawl-export.schema.json)**: a JSON Schema (2020-12) for the full-crawl JSON export the desktop app produces from its Reports page.
- **[`docs/csv-columns.md`](docs/csv-columns.md)**: the column reference for the equivalent CSV export, including exactly how it differs from JSON (booleans, nulls, formula-injection escaping).
- **[`docs/versioning.md`](docs/versioning.md)**: how the `schemaVersion` field changes, and what counts as a breaking change.
- **[`examples/sample-crawl-export.json`](examples/sample-crawl-export.json)**: a real export, produced by the app's own export code against a small seeded crawl, not hand-written JSON.
- **[`scripts/validate.js`](scripts/validate.js)**: a small Ajv-based validator you can point at your own export.

## Install & validate

```sh
git clone https://github.com/CrawlCove/seo-crawl-export-spec.git
cd seo-crawl-export-spec
npm install
npm run validate -- path/to/your-export.json   # or omit the path to check the bundled sample
```

```
OK: examples/sample-crawl-export.json matches schemaVersion 1.0.0 (7 pages).
```

## Using the schema in your own tools

```js
const Ajv = require('ajv')
const addFormats = require('ajv-formats')
const schema = require('crawlcove-export-spec/schema/crawl-export.schema.json')

const ajv = new Ajv()
addFormats(ajv)
const validate = ajv.compile(schema)
validate(myExport) // false + validate.errors on mismatch
```

## Works with CrawlCove

This is the export format produced by [Crawl Cove](https://crawlcove.com/?utm_source=github&utm_medium=seo-crawl-export-spec), the desktop SEO crawler. Run a crawl, export the Reports page to JSON or CSV, and validate it here, or build against the schema directly if you're consuming exports programmatically.

This repo has its own page on crawlcove.com: [Crawl Cove export spec](https://crawlcove.com/open-source/crawlcove-export-spec?utm_source=github&utm_medium=seo-crawl-export-spec), with the post on the crawl export format at [https://crawlcove.com/blog/seo-crawl-export-format](https://crawlcove.com/blog/seo-crawl-export-format?utm_source=github&utm_medium=seo-crawl-export-spec).

## Related tools

<!-- related:start, kept in step by CrawlCove/.github scripts/related.mjs -->
| Repo | What it does |
|---|---|
| [seo-crawler-cli](https://github.com/CrawlCove/seo-crawler-cli) | Crawl a site from a script or CI. Checks status codes, redirect chains, titles, meta descriptions, H1s, canonicals, noindex and broken links, and fails the build on regressions. |
| [seo-audit-action](https://github.com/CrawlCove/seo-audit-action) | Crawl a staging or preview URL on every pull request. Fails the check on broken links, missing titles, noindex and redirect chains, with the details as a PR comment. |
| [seo-mcp-server](https://github.com/CrawlCove/seo-mcp-server) | Crawl a site, list SEO issues, inspect pages and find broken links from inside Claude, Cursor and other AI assistants. |
| [claude-seo-plugin](https://github.com/CrawlCove/claude-seo-plugin) | Seven SEO skills for Claude Code backed by a real site crawl: audits, broken links, redirects, titles and fixes, from a chat. |
| [seo-chrome-extension](https://github.com/CrawlCove/seo-chrome-extension) | On-page SEO checker with an AI visibility report: which AI crawlers can read the page, snippet blocks, llms.txt and extractability. Nothing leaves the browser. |
| [xml-sitemap-validator](https://github.com/CrawlCove/xml-sitemap-validator) | Check a sitemap or sitemap index against the sitemaps.org protocol and search engine limits. Flags bad URLs, lastmod dates and cross-host entries. |
| [robots-txt-tester](https://github.com/CrawlCove/robots-txt-tester) | Fetch and lint a robots.txt, test which URLs each crawler may fetch and see the deciding line. Fails deploys that block the site. |
| [redirect-chain-checker](https://github.com/CrawlCove/redirect-chain-checker) | Follow every hop of a URL's redirects and flag chains, loops, HTTPS downgrades, meta refreshes and redirects to error pages. |
| [hreflang-checker](https://github.com/CrawlCove/hreflang-checker) | Validate the hreflang tags on a page or across a whole XML sitemap, including self-reference, x-default and return tags. |
| [schema-markup-validator](https://github.com/CrawlCove/schema-markup-validator) | Check every schema.org node on a page against the required and recommended properties for rich results. |
| [seo-test-site](https://github.com/CrawlCove/seo-test-site) | A deliberately broken SEO test site with every planted problem and its expected finding listed, for testing any SEO crawler. |
| [seo-crawl-export-js](https://github.com/CrawlCove/seo-crawl-export-js) | Load, query and convert site crawl data in JavaScript and TypeScript, with typed pages, issues by type and status filters. |
| [screaming-frog-export-converter](https://github.com/CrawlCove/screaming-frog-export-converter) | Turn an SEO Spider internal_all.csv into the open JSON crawl export format, with a report of which columns carried over. |
| [seo-audit-google-sheets](https://github.com/CrawlCove/seo-audit-google-sheets) | An Apps Script add-on that turns a site crawl export into an audit workbook: pages, issues by type, pages by status and title and meta flags. |
| [wordpress-seo-connector](https://github.com/CrawlCove/wordpress-seo-connector) | Bulk-apply fixes from the Crawl Cove desktop crawler to Yoast SEO, Rank Math, SEOPress or AIOSEO, with a change log and one-click revert. |
<!-- related:end -->

## License

MIT, see [LICENSE](LICENSE).

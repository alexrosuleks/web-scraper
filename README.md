# Web Scraper: Web Scraper API for Python & Node.js (2026)

Crawl websites over HTTP and run your own page function on each page to extract exactly the data you need. Follow links with globs and pseudo-URLs, set limits, and export structured results.

**[▶ Try the Web Scraper free on Scrapely](https://scrape.ly/store/querystone/web-scraper)** · [Compare Scrapely vs other scraping tools](https://scrape.ly/compare)

This repository shows how to scrape Web data with the [Web Scraper](https://scrape.ly/store/querystone/web-scraper) using the official
**Scrapely SDKs for JavaScript and Python**. You pass the input, Scrapely runs the web scraper in the cloud
(proxies, retries, browser and scaling included) and returns clean, structured data as JSON, CSV or Excel.
No scraper code to maintain and no blocked requests to debug.

## Why use this Web scraper

- **No-code or API:** run it from the [Scrapely Store](https://scrape.ly/store/querystone/web-scraper) or from your code with the SDK.
- **Structured web data:** ready-to-use fields instead of raw HTML.
- **Pay per result:** $0.84 per 1,000 result results, no monthly subscription required.
- **Maintained and monitored:** scrapers are tested around the clock and fixed fast when Web changes.
- **Integrations:** n8n, Make, Zapier, LangChain, LlamaIndex, MCP (Claude, Cursor) and webhooks.

## What Web data can you extract?

Crawl websites over HTTP and run your own page function on each page to extract exactly the data you need. Follow links with globs and pseudo-URLs, set limits, and export structured results.

## Quick start

1. [Sign up for Scrapely](https://scrape.ly) (free plan available) and copy your API token from
   **Settings → API & Integrations**.
2. Clone this repo and set your token:

```bash
export SCRAPELY_TOKEN=your_token
```

### Node.js (JavaScript SDK)

```bash
npm install
node example.js
```

```js
const { ScrapelyClient } = require('scrapely-client');
const client = new ScrapelyClient({ token: process.env.SCRAPELY_TOKEN });

const run = await client.actor('querystone/web-scraper').call({"startUrls": [{"url": "https://docs.python.org/3/tutorial/"}], "pageFunction": "// Runs on every page and returns its title and main text.\n// To scrape another site, change the Start URL. To extract other data, edit this\n// function; the README lists everything on the \"context\" object.\nasync function pageFunction(context) {\n    // jQuery reads the page (needs \"Inject jQuery\", which is on by default).\n    const $ = context.jQuery;\n    const pageTitle = $('title').first().text().replace(/\\s+/g, ' ').trim();\n\n    // The main content: the page's <main> or role=\"main\" area, a single <article>,\n    // a common content container, or else the whole body.\n    let main = $('main, [role=\"main\"]').first();\n    if (!main.length && $('article').length === 1) main = $('article');\n    if (!main.length) main = $('#content, #main, #main-content, .main-content, .post-content, .entry-content').first();\n    if (!main.length) main = $('body');\n\n    // Work on a copy without menus, headers, footers, sidebars, forms and scripts.\n    main = main.clone();\n    main.find('nav, header, footer, aside, form, script, style, noscript, template, svg, iframe, button, '\n        + '[role=\"navigation\"], [role=\"banner\"], [role=\"contentinfo\"], [role=\"search\"], [aria-hidden=\"true\"], .headerlink').remove();\n    // Mark the end of each block element, so paragraphs become separate lines.\n    const BREAK = '\\uE000';\n    main.find('p, div, section, li, h1, h2, h3, h4, h5, h6, pre, blockquote, tr, dt, dd, br').after(BREAK);\n    const text = main.text()\n        .split(BREAK)\n        .map((line) => line.replace(/\\s+/g, ' ').trim())\n        .filter(Boolean)\n        .join('\\n');\n\n    context.log.info(`URL: ${context.request.url}, TITLE: ${pageTitle}`);\n\n    // Each returned object becomes one row of the dataset.\n    return {\n        url: context.request.url,\n        pageTitle,\n        text,\n    };\n}", "excludes": [{"glob": "/**/*.{png,jpg,jpeg,pdf}"}], "respectRobotsTxtFile": true, "waitUntil": ["domcontentloaded"], "preNavigationHooks": "// We need to return array of (possibly async) functions here.\n// The functions accept two arguments: the \"crawlingContext\" object\n// and \"gotoOptions\".\n[\n    async (crawlingContext, gotoOptions) => {\n        // ...\n    },\n]\n", "postNavigationHooks": "// We need to return array of (possibly async) functions here.\n// The functions accept a single argument: the \"crawlingContext\" object.\n[\n    async (crawlingContext) => {\n        // ...\n    },\n]", "runMode": "PRODUCTION", "proxyConfiguration": {"useScrapelyProxy": true}});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
```

### Python (Python SDK)

```bash
pip install -r requirements.txt
python example.py
```

```python
from scrapely_client import ScrapelyClient
client = ScrapelyClient(os.environ['SCRAPELY_TOKEN'])

run = client.actor('querystone/web-scraper').call(run_input={ "startUrls": [ { "url": "https://docs.python.org/3/tutorial/" } ], "pageFunction": "// Runs on every page and returns its title and main text.\n// To scrape another site, change the Start URL. To extract other data, edit this\n// function; the README lists everything on the \"context\" object.\nasync function pageFunction(context) {\n// jQuery reads the page (needs \"Inject jQuery\", which is on by default).\nconst $ = context.jQuery;\nconst pageTitle = $('title').first().text().replace(/\\s+/g, ' ').trim();\n\n// The main content: the page's <main> or role=\"main\" area, a single <article>,\n// a common content container, or else the whole body.\nlet main = $('main, [role=\"main\"]').first();\nif (!main.length && $('article').length === 1) main = $('article');\nif (!main.length) main = $('#content, #main, #main-content, .main-content, .post-content, .entry-content').first();\nif (!main.length) main = $('body');\n\n// Work on a copy without menus, headers, footers, sidebars, forms and scripts.\nmain = main.clone();\nmain.find('nav, header, footer, aside, form, script, style, noscript, template, svg, iframe, button, '\n+ '[role=\"navigation\"], [role=\"banner\"], [role=\"contentinfo\"], [role=\"search\"], [aria-hidden=\"true\"], .headerlink').remove();\n// Mark the end of each block element, so paragraphs become separate lines.\nconst BREAK = '\\uE000';\nmain.find('p, div, section, li, h1, h2, h3, h4, h5, h6, pre, blockquote, tr, dt, dd, br').after(BREAK);\nconst text = main.text()\n.split(BREAK)\n.map((line) => line.replace(/\\s+/g, ' ').trim())\n.filter(Boolean)\n.join('\\n');\n\ncontext.log.info(`URL: ${context.request.url}, TITLE: ${pageTitle}`);\n\n// Each returned object becomes one row of the dataset.\nreturn {\nurl: context.request.url,\npageTitle,\ntext,\n};\n}", "excludes": [ { "glob": "/**/*.{png,jpg,jpeg,pdf}" } ], "respectRobotsTxtFile": True, "waitUntil": [ "domcontentloaded" ], "preNavigationHooks": "// We need to return array of (possibly async) functions here.\n// The functions accept two arguments: the \"crawlingContext\" object\n// and \"gotoOptions\".\n[\nasync (crawlingContext, gotoOptions) => {\n// ...\n},\n]\n", "postNavigationHooks": "// We need to return array of (possibly async) functions here.\n// The functions accept a single argument: the \"crawlingContext\" object.\n[\nasync (crawlingContext) => {\n// ...\n},\n]", "runMode": "PRODUCTION", "proxyConfiguration": { "useScrapelyProxy": True } })
items = list(client.dataset(run.default_dataset_id).iterate_items())
```

### REST API / cURL

```bash
curl -X POST "https://api.scrape.ly/v2/acts/querystone~web-scraper/run-sync-get-dataset-items?token=$SCRAPELY_TOKEN" \
  -H "Content-Type: application/json" \
  -d @input.json
```

## Example input

```json
{
  "startUrls": [
    {
      "url": "https://docs.python.org/3/tutorial/"
    }
  ],
  "pageFunction": "// Runs on every page and returns its title and main text.\n// To scrape another site, change the Start URL. To extract other data, edit this\n// function; the README lists everything on the \"context\" object.\nasync function pageFunction(context) {\n    // jQuery reads the page (needs \"Inject jQuery\", which is on by default).\n    const $ = context.jQuery;\n    const pageTitle = $('title').first().text().replace(/\\s+/g, ' ').trim();\n\n    // The main content: the page's <main> or role=\"main\" area, a single <article>,\n    // a common content container, or else the whole body.\n    let main = $('main, [role=\"main\"]').first();\n    if (!main.length && $('article').length === 1) main = $('article');\n    if (!main.length) main = $('#content, #main, #main-content, .main-content, .post-content, .entry-content').first();\n    if (!main.length) main = $('body');\n\n    // Work on a copy without menus, headers, footers, sidebars, forms and scripts.\n    main = main.clone();\n    main.find('nav, header, footer, aside, form, script, style, noscript, template, svg, iframe, button, '\n        + '[role=\"navigation\"], [role=\"banner\"], [role=\"contentinfo\"], [role=\"search\"], [aria-hidden=\"true\"], .headerlink').remove();\n    // Mark the end of each block element, so paragraphs become separate lines.\n    const BREAK = '\\uE000';\n    main.find('p, div, section, li, h1, h2, h3, h4, h5, h6, pre, blockquote, tr, dt, dd, br').after(BREAK);\n    const text = main.text()\n        .split(BREAK)\n        .map((line) => line.replace(/\\s+/g, ' ').trim())\n        .filter(Boolean)\n        .join('\\n');\n\n    context.log.info(`URL: ${context.request.url}, TITLE: ${pageTitle}`);\n\n    // Each returned object becomes one row of the dataset.\n    return {\n        url: context.request.url,\n        pageTitle,\n        text,\n    };\n}",
  "excludes": [
    {
      "glob": "/**/*.{png,jpg,jpeg,pdf}"
    }
  ],
  "respectRobotsTxtFile": true,
  "waitUntil": [
    "domcontentloaded"
  ],
  "preNavigationHooks": "// We need to return array of (possibly async) functions here.\n// The functions accept two arguments: the \"crawlingContext\" object\n// and \"gotoOptions\".\n[\n    async (crawlingContext, gotoOptions) => {\n        // ...\n    },\n]\n",
  "postNavigationHooks": "// We need to return array of (possibly async) functions here.\n// The functions accept a single argument: the \"crawlingContext\" object.\n[\n    async (crawlingContext) => {\n        // ...\n    },\n]",
  "runMode": "PRODUCTION",
  "proxyConfiguration": {
    "useScrapelyProxy": true
  }
}
```

See every input option on the [Web Scraper page](https://scrape.ly/store/querystone/web-scraper).

## Pricing

**$0.84 per 1,000 result results.** Free plan available, no credit card needed to start. Check current pricing on the
[Store page](https://scrape.ly/store/querystone/web-scraper) and see how Scrapely compares with other tools on [scrape.ly/compare](https://scrape.ly/compare).

## FAQ

**How do I scrape Web with Python?** Install `scrapely-client-python`, set `SCRAPELY_TOKEN` and run
`example.py`. The scraper runs on Scrapely and you read the results from the dataset.

**How do I scrape Web with Node.js?** Install `scrapely-client`, set `SCRAPELY_TOKEN` and run `example.js`.

**Is there a web scraper API?** Yes. Call `POST https://api.scrape.ly/v2/acts/querystone~web-scraper/runs`
or use `run-sync-get-dataset-items` to get results in one request.

**Do I need proxies?** No. Proxies, retries and browsers are handled by Scrapely.

**Is scraping Web legal?** This scraper collects publicly available data. Check Web's terms and the
laws that apply to you, especially for personal data (GDPR, CCPA).

**Can I schedule it or get results by webhook?** Yes. Use Scrapely schedules and webhooks, or n8n, Make and Zapier.

## Looking for an alternative?

Scrapely is an alternative to Apify, Bright Data, ScraperAPI, Firecrawl, Octoparse, PhantomBuster, SerpApi,
Zyte and others. See the side-by-side pricing on [scrape.ly/compare](https://scrape.ly/compare).

---

**Keywords:** web scraper, web scraper, web scraper api, web scraper python, web scraper node.js,
scrape web, web data extraction, web crawler, web api, export web data to csv, web-scraper

License: MIT. Example code only; the scraper runs on [Scrapely](https://scrape.ly/store/querystone/web-scraper).

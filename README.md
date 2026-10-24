# product data scraping: how to pull prices, stock and catalogs without getting blocked — real costs, credit math and plan picks

Product data scraping comes down to three things: getting the fields you need (price, variants, stock, images), keeping the scraper alive long enough to collect them, and doing it at a cost per request you can live with. The first part is mostly technical. The second and third are where most projects either stall or burn through a budget, because anti-bot systems like Cloudflare and DataDome now sit in front of the majority of ecommerce sites.

This guide walks through the working methods in 2026, then uses ScraperAPI — a scraping API that handles proxies, rendering and CAPTCHAs through one endpoint — as the reference for what scraping at scale actually costs, including a few billing rules that rarely make it into comparison articles.

> **Quick take:** if you already have scraper code and keep getting blocked, ScraperAPI's Hobby plan at $49/month (100,000 credits) is the realistic entry point — a plain HTML product page costs 1 credit, so that's roughly 100,000 standard pages if you avoid the expensive domains. All paid plans include JS rendering, residential proxies and CAPTCHA handling, and paid plans come with a 7-day refund. 👉 [Check current ScraperAPI plans and prices](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## What people actually scrape, and how often

Four data types dominate product data scraping, and they have very different demands:

- **Product catalogs** — titles, descriptions, images, categories. Crawled daily to weekly; anti-bot pressure is usually moderate.
- **Prices** — `price`, `currency`, `sale_price`, timestamp. Competitive price monitoring means hourly or near-hourly crawls, and this is where anti-bot requirements get serious, because prices load dynamically.
- **Reviews and ratings** — rating, review count, review text. Daily to weekly.
- **Inventory** — `in_stock`, quantity. Hourly to daily for high-priority SKUs.

One practical pattern worth stealing: don't store raw results and panic about every change. Save each run, compare it against the previous one, and only alert when a change crosses a threshold — say, a price drop larger than 5%. For inventory, track `(product_id, in_stock, timestamp)` and trigger an alert when a high-priority SKU flips between in-stock and out-of-stock.

## Three ways to get the data, ranked by effort

### 1. Free structured endpoints — try this first

Some platforms expose data directly. Shopify stores, for example, serve a paginated JSON catalog at `/products.json` (try `?limit=250&page=1`), which includes titles, prices, images, vendors, tags, variants and SKU-level stock status. WooCommerce sites expose a REST API at `/wp-json/wc/v3/products` when you have API keys — if keys are available, always prefer the API over scraping.

The catch: this window is closing. Since July 2025, Shopify has added an "Agent policy" to every store's robots.txt declaring that automated scraping isn't welcome. That file isn't legally binding, but it signals where things are heading — expect more stores to disable `/products.json` and put bot protection in front of their storefronts. It's also worth noting Shopify's own API caps at 40 requests per app per store per minute, so even the "free" path has rate limits.

And even where the endpoint works, it has gaps: the `available` field is a boolean only — you get in-stock yes/no, no unit counts.

### 2. Parse what's already on the page

A lot of ecommerce sites embed structured data in `<script type="application/ld+json">` tags using the Product schema. Parsing that JSON-LD gets you name, price and availability without writing fragile CSS selectors. The same trick works for reviews: `AggregateRating` and `Review` objects in the schema are far more reliable than scraping star widgets from HTML, which vary wildly across sites.

For inventory, the signals are the "Add to Cart" button versus a disabled state, plus the JSON-LD `availability` field. True quantities are rarely exposed — and when they are, sites sometimes fake them to confuse scrapers.

### 3. A scraping API for anything protected

When a target renders prices client-side, sits behind Cloudflare or DataDome, or simply rate-limits your IP after fifty requests, you need rotating residential proxies, headless browser rendering and CAPTCHA solving. Building that stack yourself is a project in itself. ScraperAPI's model is simpler: you send a URL, it returns rendered HTML or parsed JSON, and the infrastructure problems disappear behind one API endpoint. It also ships structured endpoints that return ready-made JSON for Amazon, Walmart, eBay, Home Depot, Google properties, and — since its 2026 acquisition of Traject Data — SERP and ecommerce data APIs from Rainforest and SerpWow, all running on the same credit system.

That's the tier this article prices in detail. 👉 [Try ScraperAPI free for 7 days with 5,000 credits](https://www.scraperapi.com/signup?fp_ref=coupons)

## ScraperAPI plans: the full table

All plans include the same core features — JS rendering, premium residential and mobile IPs, CAPTCHA and anti-bot bypass, automatic retries, sticky sessions, JSON auto-parsing, unlimited bandwidth. What changes is volume, concurrency and geotargeting. Annual billing takes 10% off every paid plan.

| Plan | Monthly price | Annual (per month) | API credits | Concurrency | Geotargeting | Get started |
| --- | --- | --- | --- | --- | --- | --- |
| Free | $0 | — | 1,000 | 5 | — | [Get 1,000 free credits](https://dashboard.scraperapi.com/signup?fp_ref=coupons) |
| Hobby | $49 | $44.10 | 100,000 | 20 | US & EU only | [Start Hobby](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Startup | $149 | $134.10 | 1,000,000 | 50 | US & EU only | [Start Startup](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Business | $299 | $269.10 | 3,000,000 | 100 | Global (country-level) | [Start Business](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Scaling | $475 | $427.50 | 5,000,000 | 200 | Global (country-level) | [Start Scaling](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Professional | $975 | $877.50 | 10,500,000 | 300 | Global (country-level) | [Start Professional](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Advanced | $1,975 | $1,777.50 | 21,500,000 | 500 | Global (country-level) | [Start Advanced](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Enterprise | Custom | Custom | 22M+ | 500+ | Global | [Talk to sales](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |

Two details the table hides: Hobby and Startup keep only 30 days of analytics history (unlimited from Business up), and Hobby/Startup can't use pay-as-you-go overage — more on that below.

## The credit math nobody shows you

Here's the part most comparison posts skip: your credits don't buy a fixed number of requests. One credit buys one standard HTML request. Everything else costs more:

| Request type | Credits |
| --- | --- |
| Standard HTML page | 1 |
| Ecommerce domains (Amazon, Walmart, eBay) | 5 |
| JS rendering (`render=true`) | 10 |
| Premium residential proxies (`premium=true`) | 10 |
| Premium proxy + rendering | 25 |
| Ultra-premium proxy | 30 |
| Ultra-premium + rendering | 75 |
| Google / Bing SERP | 25 |
| LinkedIn | 30 |
| Cloudflare / DataDome / PerimeterX / Akamai bypass | +10 each |

Run the numbers on a real project and the multipliers matter more than the plan price. Say you're monitoring 2,000 competitor product pages daily, and the target renders prices with JavaScript — that's 10 credits per request, 20,000 credits per day, 600,000 per month. The Hobby plan's 100,000 credits cover barely a week of that. At $0.49 per 1,000 credits on Hobby versus $0.15 on Startup and $0.10 on Business, the Startup plan is roughly the break-even point for that workload, and Business buys comfortable headroom.

The stacking is also non-linear: rendering plus premium proxies costs 25 credits, not 20 — the combination costs more than the sum of its parts. Ultra-premium plus rendering hits 75 credits for a single page.

Before committing to a plan, do what the docs themselves recommend: test your actual target URLs first. The dashboard has a Domain Multiplier that shows the credit cost of any URL, an API Playground for test runs, and a `max_cost` parameter that caps what any single request can burn. There's even a cost-check endpoint (`api.scraperapi.com/account/urlcost`) if you'd rather query it programmatically. Every response also includes a `sa-credit-cost` header showing what that request actually consumed.

Four billing rules are easy to miss and worth knowing before you buy:

1. **Credits reset monthly and don't roll over.** Unused credits vanish at renewal. Annual plans can renew early to grab a fresh batch.
2. **Only successful requests are charged** — HTTP 200 and 404 responses. But if you cancel a request yourself within 70 seconds, it still counts.
3. **Exceeding concurrency returns HTTP 429 with no charge** — the request is turned away rather than queued. If your jobs get throttled, that's a concurrency ceiling problem, not a credits problem.
4. **Pay-as-you-go overage only exists on Scaling and above.** On Hobby, Startup or Business, running dry means upgrading or pausing — by default scraping simply stops until credits renew. Auto-upgrade (opt-in, available from Hobby through Professional) bumps you to the next plan automatically when credits run out.

## Which plan fits which project

The pricing per 1,000 credits tells most of the story: $0.49 on Hobby, $0.15 on Startup, $0.10 on Business, then a flat ~$0.092–0.095 from Scaling upward. Beyond Business, you're mostly buying concurrency and credits at nearly the same unit rate.

- **Testing the waters or scraping a few hundred pages occasionally** → the Free plan's 1,000 credits (5 concurrent) costs nothing and needs no credit card. Plain-HTML targets only — a handful of Amazon requests at 5 credits each will drain it fast.
- **Small, mostly-static targets** → Hobby at $49/month. If your target pages don't need JS rendering, 100,000 standard requests go a long way.
- **Serious price monitoring with rendering** → Startup ($149) or Business ($299), depending on volume. Business is also the first tier with country-level geotargeting outside US/EU and unlimited analytics history.
- **Always hitting your ceiling and don't want workflow interruptions** → Scaling ($475) is the first plan with pay-as-you-go overage at a fixed per-credit rate, capped at a monthly limit you set.
- **Building data pipelines without much code** → DataPipeline runs up to 100,000 URLs, keywords, ASINs or Walmart IDs per project, accepts CSV and webhook input, schedules jobs, and outputs HTML, JSON or CSV. It's on all paid plans.
- **22M+ credits a month** → that's Enterprise territory, with custom pricing and dedicated support.

One honest caveat: if you need to geotarget outside the US and EU on a small budget, the Hobby and Startup plans won't do it — country-level targeting starts at Business. And if you want a scraper marketplace with pre-built bots you can run without writing code, that's a different product category entirely; ScraperAPI assumes you have (or can write) the code that calls it.

## How it compares when you're choosing

A couple of alternatives come up constantly in product data scraping discussions, and it's fair to name them without pretending one size fits all. Bright Data has a much larger IP pool and finer-grained targeting — down to specific ASNs and mobile carriers in some cases — but it costs more and takes longer to configure. Oxylabs occupies similar enterprise territory, with dedicated account management. Both lean enterprise; if you're a solo developer or a small team with a Python script and a blocked IP, ScraperAPI's flat monthly plans and 7-day trial are the lower-friction way to find out whether an API approach works for your targets.

On the criticism side, third-party reviews of ScraperAPI flag a few real issues: a 10-minute result cache on difficult targets can mean slightly stale data for fast-moving prices, the $49 entry point is steep compared to credit-card-free free tiers elsewhere, and the low-tier geotargeting limitation mentioned above. None of these are dealbreakers for catalog and inventory work, but hourly price monitoring on aggressively protected sites deserves a trial run before you commit to an annual plan.

## FAQ

**How much does product data scraping cost per month?**
For a scraping API, expect $49–149/month for small-to-medium projects (ScraperAPI Hobby and Startup), $299–475 for serious monitoring volumes, and custom pricing above 5M credits/month. The real variable is the per-request credit cost: 1 credit for plain HTML pages, 10 with JavaScript rendering, up to 75 for ultra-premium proxies plus rendering.

**Is scraping product data legal?**
Publicly available product data — prices, titles, availability — is generally scrapeable for research, comparison and analysis. Two lines not to cross: don't republish copyrighted assets like product photography on your own store, and don't hit authenticated or personal data you have no right to access. Robots.txt isn't legally binding, but Shopify's 2025 "Agent policy" shows platforms are actively tightening their posture, so build for resilience rather than assuming today's free endpoint lasts forever.

**Do I get my money back if it doesn't work for my targets?**
Yes — ScraperAPI's paid plans carry a 7-day unconditional refund, cancellable from the dashboard. Combined with the free 1,000-credit plan and the 7-day/5,000-credit trial, you can test your actual targets three different ways before spending anything.

**Why did my credits disappear so fast?**
Almost always the multipliers. Check the `sa-credit-cost` response header, look up your target domains in the Domain Multiplier, and set `max_cost` on individual requests. Amazon pages at 5 credits and any Cloudflare-protected page at 10+ add up much faster than "100,000 requests" suggests.

## Picking a plan and placing the order

If you've read this far, the decision usually collapses to one question: does your target list need rendering and bypassing, or not?

- Plain-HTML targets, low volume → start free, upgrade to Hobby only when 1,000 credits becomes a weekly ritual.
- Rendered prices or protected stores → Hobby to test, Startup or Business to run. Turn on auto-upgrade so a busy month doesn't stall your jobs.
- Hourly price monitoring on hundreds of SKUs → Business is the realistic floor; Scaling if you also want pay-as-you-go headroom.

Before checking out: confirm your target URLs' credit costs in the dashboard (the API Playground takes two minutes), and remember credits don't roll over, so size the plan to your steady monthly volume rather than your peak. If you'd rather not commit at all, the trial route costs nothing — 👉 [sign up for the 7-day trial with 5,000 free credits](https://www.scraperapi.com/signup?fp_ref=coupons) and point it at your real targets before deciding.

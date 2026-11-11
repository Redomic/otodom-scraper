# Otodom Scraper: Poland Property Listings & Price Monitor

Extract property listings from any Otodom.pl search: price, area, rooms, location, advertiser, and listing URLs, plus full ad details such as GPS coordinates, build year, monthly charges, building details, photos and description. Monitor mode tracks new and price-changed listings.

**Run it in the cloud, no setup:** [https://apify.com/redomic/otodom-scraper](https://apify.com/redomic/otodom-scraper). Works on Apify's free plan; about $3.00 per 1,000 results
(less on paid plans). Schedules, API, webhooks and CSV/Excel/JSON export are built in.

## What you get
- Listings from Otodom.pl search results (sale or rent) for flats, houses, rooms, plots, commercial and more.
- Normalized, flat rows: IDs, URLs, title, price in PLN, price per m², area, rooms, floor, property/deal type, advertiser type and agency, city/district/street/region, image count, created/modified dates.
- **Priced rows only by default**: listings without a published price are dropped from the output. Set `includeNoPrice: true` to also return them with `priceAmount`/`priceCurrency` null.
- **Optional full ad details** (`fetchDetails`): set `fetchDetails: true` to open each listing's offer page and merge GPS (`lat`/`lng`), `buildYear`, `monthlyCharges` (czynsz), `heating`, `buildingType`, `buildingMaterial`, `windowsType`, `ownership`, `constructionStatus`, `market`, `features`, the full `photos` gallery and the `description` into the row. Default `false` for fast search-card-only runs with lower proxy cost.
- **Radius search**: set centre `lat`/`lng` plus `radiusKm` (e.g. Warsaw centre with `radiusKm: 2`) to keep only rows within that distance. Every returned row includes `distanceKm`; rows without GPS are excluded.
- **Monitor mode**: run on a schedule to get only `new`, `changed` (e.g. price change) and `removed` listings since the previous run. `changed` rows include current price fields plus `previousPriceAmount`, `previousPriceCurrency`, and `previousPricePerM2` (when present in state). Each monitor row also carries `priceHistory` (append-only observations stored from this monitor, last 20), `priceChangePct` and `priceDirection` (`up`/`down`/`flat`/`new`) computed from stored snapshots without extra requests. State is kept per source URL in a named key-value store. Use `droppedOnly` to emit only rows whose price dropped while still tracking all changes in state and summary counts.
- Per-source status rows and a run summary. Empty or failed sources are reported, never silently skipped.
- **Blocked sources are explicit**: a blocked or unparsable page is reported as a `failed` status row (or `partial` when earlier pages did return rows) and never disguised as an empty result. In monitor mode a blocked run does not touch the stored state, so it cannot mark your tracked listings as removed.
- Seller contact data is never returned: no contact names, phone numbers, agency phones or e-mail addresses. Free-text descriptions are scrubbed of e-mails and phone numbers too.

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68615321,
  "url": "https://www.otodom.pl/pl/ad/super-uklad-swietna-lokalizacja-blisko-tramwaju-bielany-zoliborz-ID4DTYJ",
  "title": "Super układ, świetna lokalizacja blisko tramwaju, Bielany/Żoliborz!",
  "priceAmount": 765719,
  "priceCurrency": "PLN",
  "pricePerM2": 19100,
  "areaM2": 40.09,
  "rooms": 2,
  "floor": 1,
  "buildYear": null,
  "market": null,
  "advertiserType": "agency",
  "agencyName": "Biuro Sprzedaży Deweloperskiej",
  "city": "Warszawa",
  "district": "Żoliborz",
  "street": "Literacka",
  "region": "mazowieckie",
  "lat": null,
  "lng": null,
  "imagesCount": 4,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-11-09T11:43:04Z",
  "modifiedAt": "2026-11-11T19:06:55Z",
  "scrapedAt": "2026-11-11T19:09:05Z"
}
```

## Run it from code
Python ([apify-client](https://pypi.org/project/apify-client/)):

```python
from apify_client import ApifyClient

client = ApifyClient("<YOUR_APIFY_TOKEN>")
run = client.actor("redomic/otodom-scraper").call(run_input={"startUrls": [{"url": "https://www.otodom.pl/pl/wyniki/sprzedaz/mieszkanie/cala-polska"}], "location": "cala-polska", "maxItems": 24})
for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

curl (waits for the run and returns the rows):

```bash
curl -X POST "https://api.apify.com/v2/acts/redomic~otodom-scraper/run-sync-get-dataset-items?token=$APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"startUrls": [{"url": "https://www.otodom.pl/pl/wyniki/sprzedaz/mieszkanie/cala-polska"}], "location": "cala-polska", "maxItems": 24}'
```

## All inputs, modes and pricing
See the full documentation on the Apify page: [https://apify.com/redomic/otodom-scraper](https://apify.com/redomic/otodom-scraper)

# Otodom Scraper: Poland Property Listings & Price Monitor

Extract property listings from any Otodom.pl search: price, area, rooms, location, advertiser, and listing URLs, plus full ad details such as GPS coordinates, build year, monthly charges, building details, photos and description. Monitor mode tracks new and price-changed listings.

**Run it in the cloud, no setup:** [https://apify.com/redomic/otodom-scraper](https://apify.com/redomic/otodom-scraper). Works on Apify's free plan; about $3.00 per 1,000 results
(less on paid plans). Schedules, API, webhooks and CSV/Excel/JSON export are built in.

## What you get
- Listings from Otodom.pl search results (sale or rent) for flats, houses, rooms, plots, commercial and more.
- Normalized, flat rows: IDs, URLs, title, price in PLN, price per m², area, rooms, floor, total floors in the building, property/deal type, advertiser type and agency, city/district/street/region, image count, created/modified dates.
- **Sort results**: control the order with `sortBy` (`default`, `newest`, `oldest`, `price_asc`, `price_desc`, `area_asc`, `area_desc`). With a low `maxItems`, sorting decides which listings you get.
- **Priced rows only by default**: listings without a published price are dropped from the output. Set `includeNoPrice: true` to also return them with `priceAmount`/`priceCurrency` null.
- **Price per m² filters**: set `pricePerM2Min`/`pricePerM2Max` to target deals by unit price; rows outside the range are excluded (falls back to `priceAmount/areaM2` when `pricePerM2` is missing).
- **Full ad details by default** (`fetchDetails`): opens each listing's offer page to add GPS (`lat`/`lng`), `buildYear`, `totalFloors`, `monthlyCharges` (czynsz), `heating`, `buildingType`, `buildingMaterial`, `windowsType`, `ownership`, `constructionStatus`, `market`, `features`, the full `photos` gallery and the `description`. Uses datacenter requests only (never escalates detail fetches to residential); set `fetchDetails: false` for faster search-card-only runs with lower proxy traffic.
- **Radius search**: set centre `lat`/`lng` plus `radiusKm` (e.g. Warsaw centre with `radiusKm: 2`) to keep only rows within that distance. Every returned row includes `distanceKm`; rows without GPS are excluded.
- **Monitor mode**: run on a schedule to get only `new`, `changed` (e.g. price change) and `removed` listings since the previous run. `changed` rows include current price fields plus `previousPriceAmount`, `previousPriceCurrency`, and `previousPricePerM2` (when present in state). Each monitor row also carries `priceHistory` (append-only observations stored from this monitor, last 20), `priceChangePct` and `priceDirection` (`up`/`down`/`flat`/`new`) computed from stored snapshots without extra requests. State is kept per source URL in a named key-value store. Use `droppedOnly` to emit only rows whose price dropped while still tracking all changes in state and summary counts.
- Per-source status rows and a run summary. Empty or failed sources are reported, never silently skipped.
- **Blocked sources are explicit**: a blocked or unparsable page is reported as a `failed` status row (or `partial` when earlier pages did return rows) and never disguised as an empty result. In monitor mode a blocked run does not touch the stored state, so it cannot mark your tracked listings as removed.
- Seller contact data is never returned: no contact names, phone numbers, agency phones or e-mail addresses. Free-text descriptions are scrubbed of e-mails and phone numbers too.

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68566433,
  "url": "https://www.otodom.pl/pl/oferta/3-pokoje-garaz-bialoleka-ID4DHgd",
  "title": "3 pokoje | Garaż | Białołęka",
  "priceAmount": 799000,
  "priceCurrency": "PLN",
  "pricePerM2": 11979.01,
  "areaM2": 66.7,
  "rooms": 3,
  "floor": 2,
  "totalFloors": 3,
  "buildYear": 2009,
  "market": "secondary",
  "advertiserType": "agency",
  "agencyName": "DYBOWSCY NIERUCHOMOŚCI",
  "city": "Warszawa",
  "district": "Białołęka",
  "street": "Żeglugi Wiślanej",
  "region": "mazowieckie",
  "lat": 52.36099,
  "lng": 21.03732,
  "imagesCount": 11,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-10-26T12:21:20Z",
  "modifiedAt": "2026-11-14T13:52:20Z",
  "monthlyCharges": 0,
  "features": [
    "balcony",
    "garage"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImtxZThydDVleWMzcDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Y2aWzMg-_tuaCTJkd8GlzJOb83IcEDxOSz5h50hOHKs/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ijg1emh2N3d4MGoycTEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.yYAQgpK8Ar7bAkP_5V0hVwiV8yqL-sCCW-R0QmqGjIM/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImZyOHB6a3hrZmZtNDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.1f_V20-_alcNGmjr61Xr0Y-y9PscHZw2JXX_xBKS_bM/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im5wcnEwa21kM2NqMTMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.b-avJ5nKwZTxHEbl7pxtmzUG-_W4Hy7Jp5ncLra34ug/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImdsOThleGExejQxczMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.0ivFfaZo5lNPZhA-_Hg9m9UjyLkMjUR5rVJ5YBEHREk/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InB1Y2pveG9zYjlqaS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.oRj7bxsZaOOzDjKKW8jZg-tBD9rD9w-5bP-DbQtouOQ/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Imx0YzAwMjFjbGJvYjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.SpgPM4DaRGHOSTjG-7MkVDzWSX3mKKahSwVlQZnwJsY/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ink2OGNlODI1MGRzNi1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.bYomvcoQgD8D_hSd-obbH2PY8QXDSUwHB8O7Rn3yRzQ/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjVrbnZtMmYzMmI0cTItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.9cAl9jKrZ9dtfQAg99QknO54FkVGVc37h5lhEjFJ3jc/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjRzcWRyY21sNXFwbjItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.pP692niEoRIBgbpz2FRcOAUA88HpW19SVRfYCso2oL4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjVlM25qYXR0dmczcDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.NJjgQ3LWP6sR3prZlf2fhWmuL1Boo0L_kgf4K7b3oWU/image;s=2048x1536;q=80"
  ],
  "description": "Balans między miejską wygodą a odpoczynkiem na łonie natury.\nFunkcjonalne mieszkanie, które pozwala na szybkie wprowadzenie się bez konieczności przeprowadzania remontów. Ta oferta na warszawskim osiedlu Regaty to rozsądny wybór dla rodzin i osób aktywnych, ceniących uporządkowaną przestrzeń oraz bliskość terenów spacerowych nad Kanałem Żerańskim.\nPARAMETRY MIESZKANIA:\n• Metraż: 66,70 m²\n• Liczba pokoi: 3\n• Piętro: 2\n• Rok budowy: 2009\n• Balkon: 6,4 m²\n• Klimatyzacja: Tak\n• Stan: Do zamieszkania\n• Garaż: Tak\nOPIS WNĘTRZA I ROZKŁAD:\nMieszkanie o powierzchni niemal 67 m² zostało zaprojektowane z naciskiem na wygodę codziennego użytkowania. Część dzienną tworzy jasny salon połączony z przestronną kuchnią, w której bez problemu mieści się pełnowymiarowy stół. Jest to idealne rozwiązanie do celebrowania wspólnych posiłków i rodzinnych rozmów. Część prywatna to dwie niezależne sypialnie, które mogą pełnić funkcję pokoi dziecięcych, sypialni głównej czy gabinetu do pracy. Dużym atutem przy trzypokojowym układzie jest rozdzielenie węzła sanitarnego na główną łazienkę oraz osobne WC, co rozwiązuje problem porannych kolejek. Dopełnieniem przestrzeni jest balkon o powierzchni 6,4 m². Nieruchomość sprzedawana jest w stanie gotowym do wejścia, wymaga jedynie odświeżenia.\nLOKALIZACJA I KOMUNIKACJA:\nOsiedle Regaty to świetnie zorganizowana przestrzeń na mapie warszawskiej Białołęki. Miejsce to docenią zwłaszcza miłośnicy aktywnego wypoczynku oraz rodziny z dziećmi. Zaledwie 500 m (kilka minut spacerem) dzieli budynek od Kanału Żerańskiego, oferującego kilometry ścieżek pieszych i rowerowych. Trasa prowadzi, aż nad sam Zalew Zegrzyński, gdzie można odpocząć na plaży lub w restauracji. Tuż pod domem znajdują się Żabka, piekarnia, warzywniak oraz pyszne Zenko Sushi. W najbliższej okolicy znajdują się sklepy Biedronka, Netto, Rossmann oraz apteka. W sąsiedztwie dostępna jest pełna infrastruktura edukacyjna oraz place i sale zabaw. W pobliżu osiedla znajdują się przystanki autobusowe, które zapewniają bezpośrednie połączenie ze stacją Metro Kondratowicza. Szybki dojazd do trasy S8 gwarantuje sprawne poruszanie się samochodem po Warszawie oraz drogach wylotowych.\nSTAN PRAWNY I BEZPIECZEŃSTWO:\nNieruchomość stanowi pełną własność z założoną Księgą Wieczystą. Czysty stan prawny sprawia, że transakcja jest bezpieczna, a zakup mogą Państwo z powodzeniem sfinansować przy pomocy kredytu hipotecznego.\nGaraż dodatkowo płatny 50 000 zł.\nZakup tej nieruchomości wiąże się ze sprzedażą Twojego obecnego mieszkania lub domu?\nZapraszam do kontaktu. Zapewniam pełną koordynację obu transakcji, dzięki czemu przejdziesz przez cały proces płynnie, bezpiecznie i bez niepotrzebnego stresu.\nMalwina Arusztowicz\nDybowscy Nieruchomości",
  "ownership": "full_ownership",
  "scrapedAt": "2026-11-14T13:59:22Z"
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

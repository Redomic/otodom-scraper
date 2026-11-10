# Otodom Scraper: Poland Property Listings & Price Monitor

Extract property listings from any Otodom.pl search: price, area, rooms, location, advertiser, and listing URLs, plus full ad details such as GPS coordinates, build year, monthly charges, building details, photos and description. Monitor mode tracks new and price-changed listings.

**Run it in the cloud, no setup:** [https://apify.com/redomic/otodom-scraper](https://apify.com/redomic/otodom-scraper). Works on Apify's free plan; about $3.00 per 1,000 results
(less on paid plans). Schedules, API, webhooks and CSV/Excel/JSON export are built in.

## What you get
- Listings from Otodom.pl search results (sale or rent) for flats, houses, rooms, plots, commercial and more.
- Normalized, flat rows: IDs, URLs, title, price in PLN, price per m², area, rooms, floor, property/deal type, advertiser type and agency, city/district/street/region, image count, created/modified dates.
- **Priced rows only by default**: listings without a published price are dropped from the output. Set `includeNoPrice: true` to also return them with `priceAmount`/`priceCurrency` null.
- **Full ad details by default** (`fetchDetails: true`): each listing's offer page is opened and merged into the row, adding GPS (`lat`/`lng`), `buildYear`, `monthlyCharges` (czynsz), `heating`, `buildingType`, `buildingMaterial`, `windowsType`, `ownership`, `constructionStatus`, `market`, `features`, the full `photos` gallery and the `description`. Set `fetchDetails: false` for the fastest search-card-only run.
- **Radius search**: set centre `lat`/`lng` plus `radiusKm` (e.g. Warsaw centre with `radiusKm: 2`) to keep only rows within that distance. Every returned row includes `distanceKm`; rows without GPS are excluded.
- **Monitor mode**: run on a schedule to get only `new`, `changed` (e.g. price change) and `removed` listings since the previous run. `changed` rows include current price fields plus `previousPriceAmount`, `previousPriceCurrency`, and `previousPricePerM2` (when present in state). State is kept per source URL in a named key-value store.
- Per-source status rows and a run summary. Empty or failed sources are reported, never silently skipped.
- **Blocked sources are explicit**: a blocked or unparsable page is reported as a `failed` status row (or `partial` when earlier pages did return rows) and never disguised as an empty result. In monitor mode a blocked run does not touch the stored state, so it cannot mark your tracked listings as removed.
- Seller contact data is never returned: no contact names, phone numbers, agency phones or e-mail addresses. Free-text descriptions are scrubbed of e-mails and phone numbers too.

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 66342188,
  "url": "https://www.otodom.pl/pl/oferta/2-pokojowe-mieszkanie-47m2-balkon-bezposrednio-ID4umDi",
  "title": "2-pokojowe mieszkanie 47m2 + balkon Bezpośrednio",
  "priceAmount": 846775,
  "priceCurrency": "PLN",
  "pricePerM2": 17830.6,
  "areaM2": 47.49,
  "rooms": 2,
  "floor": 1,
  "buildYear": 2026,
  "market": "primary",
  "advertiserType": "agency",
  "agencyName": "SKANSKA RESIDENTIAL DEVELOPMENT POLAND SPÓŁKA Z OGRANICZONĄ ODPOWIEDZIALNOŚCIĄ",
  "city": "Warszawa",
  "district": "Włochy",
  "street": "Fajansowa",
  "region": "mazowieckie",
  "lat": 52.1964,
  "lng": 20.95204,
  "imagesCount": 11,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2025-01-14T09:36:07Z",
  "modifiedAt": "2026-11-10T00:42:36Z",
  "features": [
    "garage",
    "balcony",
    "lift",
    "closed_area"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InJ6eXN6OGR6cGU0NDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.nKQjEdzfw2JYo77VN_RmfeS_nf6d3onVHw1BZB-f1T4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InI2ZWNnNHVoMmhxZjMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.YhFKKKw4-8QudxjuZ4yma-3AO6dDE_CtVcJdS-kdLZw/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im9maDhvdHJ2ODk5YzEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.IIES_CwPFcBRgekAtBMFZ29oE3qquS5GgXH7sZPeFy0/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImNnbnNidjk2MjZsbjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.v_bU4mo894LF2jzmB0FaX6wh_6hNeOjK7ZYe4x3D1wI/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjA2NTBxcWVvZXl6dzEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Gd5Fx2xDLLcPzfyE_KEwsmDZ-H04UZHSa5Xk7fPP410/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InV6dnNxeWwwbmlpYzMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.tX1tDWmsgh5QbQQ1rtCPrhJ31RUuPPZcFselfNvXa2k/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InZld2g4NjhxeDU1cy1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.UrH3w38K6aWgW49W4OjGpL-t3QBGZ-78cZE5-4pdN9M/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImI2Nm42Mml5OWFpZTEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Unr9OGs-AuXUEuFyCi5CEASCSwHb6ZkqAsqnVIT0HwQ/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjI3amcyZmhmYWpmcDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.06QFNY2QdMhO6pcM3SPN78zYEA3SubBR6d8w4ZsdXbY/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImdpdGE1MzF1bjA4czMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.aaVSfN-Mjues3DcJ1pcW7w5o6i5rl51KkvixmlgHEt0/image;s=2048x1536;q=80"
  ],
  "description": "2-pokojowe mieszkanie numer BA0122 na 1. piętrze w Inwestycji Stilla Dewelopera Skanska\nPonadstandardowa wysokość pomieszczeń\nZielone patio z terenem rekreacyjnym\nZadaszona wiata do rowerów, stojaki rowerowe w garażu\nPark Szczęśliwicki (22 minuty spacerem)\nPrzystanek autobusowy (6 minut spacerem)\nStacja SKM \"Warszawa Rakowiec\" (16 minut spacerem)\nBiedronka (7 minut spacerem), Lidl (11 minut spacerem)\nCentrum Łopuszańska 22 (10 minut spacerem)\nStilla - poznaj inwestycję\nInwestycja Stilla obejmuje dwa starannie zaprojektowane budynki – cztero- i ośmiopiętrowy. Wydzielono w nich łącznie 179 mieszkań o układach 1-, 2-, 3- i 4-pokojowych oraz powierzchniach od 26 do 76 mkw. Mieszkania wyróżniają się świetnym dostępem do światła dziennego oraz dodatkową przestrzenią w postaci parterowych ogródków oraz dużych balkonów i tarasów . Osiedle przystosowane jest do potrzeb osób z niepełnosprawnościami, seniorów i dzieci.\nInwestycja Stilla sprzyja sąsiedzkiej integracji – zaplanowano tu wewnętrze patio . Dziedziniec otoczony jest z jednej strony starodrzewiem oraz został przemyślany w taki sposób, by jego wyposażenie było atrakcyjne zarówno dla dużych, jak i małych mieszkańców. Unikatowym rozwiązaniem jest szklarnia, w której każdy może uprawiać własne rośliny .\nBudynek został wyróżniony Certyfikatem Breeam , który zaświadcza o tym, że powstanie on w oparciu o zasady zróżnicowanego rozwoju. W inwestycji przewidziano liczne, ekologiczne rozwiązania , w tym:\nzielone dachy – dodatkowa powierzchnia retencjonująca wodę,\nwindy z odzyskiem energii,\nczujnik jakości powietrza Airly,\nbudowa zasilana energią z odnawialnych źródeł,\ndomki dla ptaków i owadów,\nzdrowe materiały budowlane bez lotnych związków organicznych,\nelementy drewniane w budynku i na etapie budowy z certyfikatem FSC zaświadczającym o tym, że drewno jest legalnego pochodzenia,\nstarannie zaplanowane tereny zielone z rodzimymi gatunkami roślin,\nstojaki rowerowe w garażu,\nzadaszona wiata dla rowerów.\nStilla - lokalizacja\nRaków, ul. Fajansowa\n5,5 km do centrum\n6 min. spacerem do przystanku autobusowego\n11 min. spacerem do przystanku tramwajowego\n6 min. samochodem do stacji SKM\ndojazd do centrum: 18 min samochodem\nIstotne punkty w okolicy inwestycji Kawiarnia (169 m, 3 minuty spacerem)\nRestauracja (169 m, 3 minuty spacerem)\nWeterynarz (175 m, 3 minuty spacerem)\nPrzedszkole (412 m, 6 minut spacerem)\nPrzystanek autobusowy (450 m, 6 minut spacerem)\nPoczta (492 m, 6 minut spacerem)\nPiekarnia (492 m, 6 minut spacerem)\nLekarz (495 m, 7 minut spacerem)\nSklep: Biedronka (552 m, 7 minut spacerem)\nApteka (627 m, 8 minut spacerem)\nCentrum Łopuszańska 22 (787 m, 10 minut spacerem)\nPrzystanek tramwajowy (861 m, 11 minut spacerem)\nSklep: Lidl (870 m, 11 minut spacerem)",
  "ownership": "full_ownership",
  "constructionStatus": "to_completion",
  "scrapedAt": "2026-11-10T04:35:09Z"
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

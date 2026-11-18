# Otodom Scraper: Poland Property Listings & Price Monitor

Export flats, houses and plots for sale or rent from Otodom.pl, Poland's biggest property portal, to a spreadsheet or an API. Each row gives you the price in PLN, price per m², area, rooms, floor, build year, city and district, the market (new or resale), the agency or private owner, and the photos. Run it on a schedule and you get only the new listings and price changes since the last run.

**Run it in the cloud, no setup:** [https://apify.com/redomic/otodom-scraper](https://apify.com/redomic/otodom-scraper). Works on Apify's free plan; about $1.20 per 1,000 results
(less on paid plans). Schedules, API, webhooks and CSV/Excel/JSON export are built in.

## What you get
Flats for sale across Poland, from the default input on 15 November 2026:

| Listing | Price (PLN) | PLN per m² | Area (m²) | Rooms | Built | City | District | Market |
|---|---|---|---|---|---|---|---|---|
| 2 pokoje, 41,4 m², Muranów, ul. Niska | 775,000 | 18,720 | 41.4 | 2 | 1956 | Warszawa | Wola | resale |
| Wygodne 2 pokoje z ogródkiem | 754,560 | 19,200 | 39.3 | 2 | 2026 | Warszawa | Włochy | new |
| Komfortowe mieszkanie 2-pokojowe | 580,000 | 15,960 | 36.34 | 2 | 2020 | Gdańsk | Orunia Górna | resale |
| Apartament premium w kamienicy z windą | 1,069,000 | 18,630 | 57.38 | 2 | 2024 | Wrocław | Stare Miasto | resale |
| 4 niezależne pokoje, Stare Miasto | 1,231,000 | 21,982 | 56 | 4 | 1968 | Kraków | Stare Miasto | resale |

Prices also come ready to paste into a spreadsheet: `priceFormatted` and `pricePerM2Formatted` spell the number the Polish way, with a space between thousands and a comma before decimals. The Ursus flat listed further down returns:

```json
{ "priceFormatted": "659 000 PLN", "pricePerM2Formatted": "16 475 PLN/m²" }
```

Some sellers hide the asking price on Otodom ("Zapytaj o cenę"). Those ads carry `priceOnRequest: true` next to an empty `priceAmount`, so you can tell a deliberately hidden price from a field that failed to load; add `includeNoPrice: true` to get them. Every listing with a published price has `priceOnRequest: false`. A four-room 74 m² flat in Kraków's Bieżanów-Prokocim returns:

```json
{
  "title": "4-pokojowe mieszkanie 74m2 + balkon Bezpośrednio",
  "city": "Kraków",
  "district": "Bieżanów-Prokocim",
  "priceAmount": null,
  "priceOnRequest": true
}
```

Each row also carries the floor and the building's total floors, monthly charges (czynsz), heating, building type and material, ownership type, elevator, energy certificate, available-from date, the street, GPS coordinates, the agency name, the listing and update dates, the description and the photos. Listings also carry `daysOnMarket`, the whole number of days since the ad was first posted, so you can tell a stale listing from a fresh one at a glance.

Location goes down to the neighbourhood as well: `subdistrict` names the area inside the district, the name buyers actually search for. A Żabiniec rental in Kraków sits in Prądnik Biały district and returns:

```json
{ "district": "Prądnik Biały", "subdistrict": "Żabiniec" }
```

The Ursus flat returns `"subdistrict": "Skorosze"`, and a listing with no published neighbourhood leaves the key out.

Rental rows also carry `deposit`, the refundable kaucja a landlord asks for, when the advertiser publishes it on the offer page. For example, a two-room 40 m² flat in Kraków's Prądnik Biały listed at 2,600 PLN a month shows a 3,000 PLN deposit and 600 PLN in monthly charges.

Listings posted by an agency or developer include `agencyUrl`, the public agency profile on Otodom, so you can open the seller's other listings in one click. The example Ursus flat points to `https://www.otodom.pl/pl/firmy/biura-nieruchomosci/property-in-ID7988985`; private owners have no agency page and leave the field out.

Those rows also carry `agencyLicenseNumber`, the business licence Otodom publishes on the agency's profile, so you can check who is selling before you call. The Ursus agency's licence is 27548; private ads leave the field out.

Every listing carries `coverPhoto`, one direct link to its main photo, so you can put a thumbnail in a spreadsheet without unpacking the full `photos` list. It comes from the search card itself, so even fast runs with full details off return it, and it matches the first photo when details are on. The Żabiniec rental above returns:

```json
{ "coverPhoto": "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjJvOXVibmh0M3ppbjMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.5l4gKBNfx0dLBbM9Tq67Vdr52mFNIYJmj495uiTqwNc/image;s=2048x1536;q=80" }
```

Every search row also carries `amenities`, a plain list of the features Otodom shows on the card — balcony, parking, separate kitchen, secure building — so you can spot a flat with a balcony without opening each ad. It is read from the card itself, so fast runs with full details off still carry it, and a listing with no published features leaves the key out. The Żabiniec rental search row returns:

```json
{ "amenities": ["balcony"] }
```

An Ursus flat for sale lists `"amenities": ["separate kitchen", "basement", "secure building"]`.

Rows also carry the three status flags Otodom publishes on each ad: `isPromoted` marks a listing the seller has paid to advertise, `isExclusiveOffer` marks an exclusive mandate, and `isPremium` marks a listing Otodom places in its premium tile. They come from the listing itself, so fast runs with full details off carry them too; a listing with no published flag leaves the key out. The Żabiniec rental above returns:

```json
{ "isPromoted": false, "isExclusiveOffer": false, "isPremium": false }
```

A Radwanice house listed at 836,000 PLN returns `"isPremium": true` while `"isPromoted"` stays `false`, so you can tell a premium placement from a promoted ad.

When the advertiser publishes a floor plan, the row carries `floorPlans`, direct links to the layout images so you can check the layout without opening the ad. It is read from the offer page, so it appears on detail-enriched rows and listings pasted by URL; a listing with no published plan leaves the key out. The Ursus flat returns:

```json
{ "floorPlans": ["https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImFlbmNmaXVvc2x3ZzEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.QB35E99D1T5GpxYLaX0xVO2GSFk1TV968MaNhWqwgsY/image"] }
```

When the advertiser publishes an online viewing, the row carries `virtualTour`, a direct link to the 360° walkthrough, 3D tour or video so you can see the place before you visit. It appears on rows with full details and on listings pasted by URL; a listing with no published tour leaves the key out. The Ursus flat returns:

```json
{ "virtualTour": "https://sb360.online/u7do5q" }
```

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68583142,
  "url": "https://www.otodom.pl/pl/oferta/2-pokojowe-mieszkanie-47m2-2-ogrodki-ID4DLBI",
  "title": "2-pokojowe mieszkanie 47m2 + 2 ogródki",
  "priceAmount": 862280,
  "priceCurrency": "PLN",
  "pricePerM2": 18062,
  "areaM2": 47.74,
  "rooms": 2,
  "floor": 0,
  "totalFloors": 6,
  "buildYear": 2028,
  "market": "primary",
  "advertiserType": "agency",
  "agencyName": "RONSON DEVELOPMENT",
  "city": "Warszawa",
  "district": "Bemowo",
  "street": "Marynin",
  "region": "mazowieckie",
  "lat": 52.245304,
  "lng": 20.927654,
  "imagesCount": 7,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-10-30T13:48:31Z",
  "modifiedAt": "2026-11-18T22:40:09Z",
  "daysOnMarket": 19,
  "priceFormatted": "862 280 PLN",
  "pricePerM2Formatted": "18 062 PLN/m²",
  "agencyUrl": "https://www.otodom.pl/pl/firmy/biura-nieruchomosci/ronson-development-ID10556616",
  "coverPhoto": "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjlibG9vMndvdmpuZTItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.48ytLIGjWPSgEIhyft2Rksq9HsCq-UXKf1gHfiFGzvg/image;s=2048x1536;q=80",
  "amenities": [
    "garden",
    "terrace",
    "parking",
    "ground floor"
  ],
  "isPromoted": false,
  "isExclusiveOffer": false,
  "isPremium": false,
  "priceOnRequest": false,
  "subdistrict": "Górce",
  "features": [
    "garage",
    "terrace",
    "garden"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjlibG9vMndvdmpuZTItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.48ytLIGjWPSgEIhyft2Rksq9HsCq-UXKf1gHfiFGzvg/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImcyM3QxOW14OTR1dC1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.x3JPJesfaIC1mcZS4vdP38_kZiZhV9PiTPS97IR4daM/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InZjNDVob2F3dGpjMzMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.jSWy57bMQlNnS5yOsal4Jx7EGrQlHG-Ys6jQXxaQ1Sg/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjQ3NnFmcDZ2czR2ci1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.x6t9901DvPdqym-8hiWHoWllGWKhaaz3DeY0DvbALH8/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im1mOWxnYnk1dmp0cDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.GyFiTLjGsoKZwJp_2ldDVoyLUO_gBnfzV-pPOIt3JDg/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InozNzRuMWozODZpeDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.q-5IeMzB8xMkc9Ob1ky-1oTY_4n7rY6vO9dvp_Ywm4s/image;s=2048x1536;q=80"
  ],
  "floorPlans": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImcyM3QxOW14OTR1dC1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.x3JPJesfaIC1mcZS4vdP38_kZiZhV9PiTPS97IR4daM/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InZjNDVob2F3dGpjMzMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.jSWy57bMQlNnS5yOsal4Jx7EGrQlHG-Ys6jQXxaQ1Sg/image"
  ],
  "description": "2-pokojowe mieszkanie numer B56 na parterze w Inwestycji Halo Bemowo Dewelopera RONSON DEVELOPMENT\nTeren spacerowy Polana (1 minuta pieszo)\nPrzystanek tramwajowy (5 minut pieszo)\nPark im. Księcia Janusza (15 minut pieszo)\nRynek Bazar na Kole (20 minut pieszo)\nCH Wola Park (8 minut samochodem)\nHalo Bemowo - poznaj inwestycję\nHalo Bemowo to nowa inwestycja mieszkaniowa grupy RONSON Development, realizowana przy ul. Marynin w warszawskiej dzielnicy Bemowo.\nHalo Bemowo – to propozycja dla osób, które chcą mieszkać w spokojnej okolicy, a jednocześnie pozostać blisko miasta. Zróżnicowana oferta mieszkań odpowie na potrzeby rodzin, singli i par.\nAtutem lokalizacji jest wygodny dostęp do komunikacji miejskiej oraz szybki dojazd do centrum Warszawy dzięki bliskości linii tramwajowej oraz niedalekiej odległości od stacji Metra.\nW pobliżu znajdują się tereny rekreacyjne, szkoły, przedszkola oraz Wola Park.\nMieszkania od 36 m2\nBalkony i tarasy z widokiem na zieleń\nInfrastruktura rowerowa\nNowoczesne elewacje i dopracowane detale\nPodziemny parking   i komórki lokatorskie\nHalo Bemowo - lokalizacja\nBemowo, ul. Marynin\n6,0 km do centrum\n6 min. spacerem do przystanku autobusowego\n6 min. spacerem do przystanku tramwajowego\n8 min. samochodem do stacji metra\ndojazd do centrum: 19 min samochodem\nIstotne punkty w okolicy inwestycji Przystanek autobusowy (414 m, 6 minut spacerem)\nPrzystanek tramwajowy (430 m, 6 minut spacerem)",
  "elevator": false,
  "availableFrom": "2028-10-16",
  "ownership": "full_ownership",
  "constructionStatus": "to_completion",
  "scrapedAt": "2026-11-18T23:03:40Z"
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

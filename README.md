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

The Output tab opens on a Listings table of the main listing fields, with the province (voivodeship) next to the city, so a nationwide export is already grouped by region. A second Market & price history table groups `market`, `pricePerM2` and `priceChangePct` — Otodom publishes the market and the price change for only some ads, and price change appears after a repeat monitor run — so the first table does not open on columns that are empty for most ads. A third New builds table groups `developmentTitle` and `developmentUrl`, which only ads in a published project carry.

Otodom leaves the per-m² price off rental cards. The scraper works it out from the advertised price and the area, so renters get the same comparison column as buyers: the Żabiniec flat below, 2,600 PLN a month for 40 m², returns `"pricePerM2": 65` and `"pricePerM2Formatted": "65 PLN/m²"`.

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

Every row carries `province`, the voivodeship (województwo) Otodom publishes with the address — the column other Otodom exports call Province — so you can group a nationwide export by region or line the rows up with another Polish property dataset. It comes from Otodom's own location data on search cards and offer pages, so fast runs carry it too; a listing with no published province leaves the key out. A search across all Poland returns:

```json
{ "city": "Kraków", "province": "małopolskie" }
```

A Gdańsk studio in the same run returns `"province": "pomorskie"`.

Location goes down to the neighbourhood as well: `subdistrict` names the area inside the district, the name buyers actually search for. A Żabiniec rental in Kraków sits in Prądnik Biały district and returns:

```json
{ "district": "Prądnik Biały", "subdistrict": "Żabiniec" }
```

The Ursus flat returns `"subdistrict": "Skorosze"`, and a listing with no published neighbourhood leaves the key out.

Rental rows also carry `deposit`, the refundable kaucja a landlord asks for, when the advertiser publishes it on the offer page. For example, a two-room 40 m² flat in Kraków's Prądnik Biały listed at 2,600 PLN a month shows a 3,000 PLN deposit and 600 PLN in monthly charges.

Houses and plots carry `plotArea`, the size of the land in square metres, so you can tell the building's floor area from the plot it stands on. It comes from the offer page and is left out when the advertiser does not publish it. A lakeside house in Góry with 350 m² of living space returns:

```json
{ "areaM2": 350, "plotArea": 2364 }
```

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

Detail-enriched rows also carry `media`, the utilities the advertiser publishes on the offer page — internet, cable television and phone in a flat, plus water, electricity and sewage on a house — so you can tell a wired home before arranging a viewing. It is read from the offer page, so fast runs with full details off leave it out, and a listing with no published utilities leaves the key out. The Ursus flat returns:

```json
{ "media": ["internet", "cable television", "phone"] }
```

Buyers who need parking can read `parkingType` without opening each ad: `garage` when Otodom lists a garage, `parking` for a parking spot. It comes from the listing card, so fast runs with full details off still carry it, and a listing with no published parking leaves the key out. The Kraków flat below, 940,000 PLN for 54 m² in Łagiewniki-Borek Fałęcki, returns:

```json
{ "parkingType": "parking", "amenities": ["balcony", "top floor", "parking", "secure building"] }
```

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

When a seller advertises an open house, the row carries `openDay`, the date and viewing window Otodom publishes, so you can plan a visit without opening each ad. It comes from the offer page, so it appears on detail-enriched rows and listings pasted by URL; a listing with no published open day leaves the key out. A listing advertising a viewing on 16 November returns:

```json
{ "openDay": "2026-11-16 09:00-19:00" }
```

New-build units also carry `developmentTitle` and `developmentUrl`, the name of the project the flat belongs to and a link to its Otodom development page, so you can see the rest of the scheme from one row. A two-room 42.71 m² flat in Warsaw's Mokotów listed at 850,869 PLN returns:

```json
{ "developmentTitle": "Mokopolis", "developmentUrl": "https://www.otodom.pl/pl/inwestycja/mokopolis-ID4ub4w" }
```

Resale ads and units with no published project leave both keys out.

Set `includeDevelopments: true` to also return the new-build projects Otodom mixes into sale searches, instead of dropping them. Each project becomes one row with its unit count, starting price per m², area and rooms ranges, delivery quarter and build state, so you can compare whole schemes rather than single units:

```json
{
  "title": "Osiedle Metr Kwadrat",
  "city": "Wrocław",
  "unitsNumber": 69,
  "pricePerM2": 13500,
  "pricePerM2Formatted": "13 500 PLN/m²",
  "areaMinM2": 34.1,
  "areaMaxM2": 56,
  "roomsMin": 2,
  "roomsMax": 4,
  "estimatedDelivery": "2026-Q1",
  "investmentState": "in building",
  "agencyName": "INKOM",
  "url": "https://www.otodom.pl/pl/investment/osiedle-metr-kwadrat-ID4za7N"
}
```

Project rows carry `propertyType: "development"` and no `priceAmount`, because the scheme publishes a per-m² price rather than one asking price; the Output tab opens a New-build projects table for them.

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68637001,
  "url": "https://www.otodom.pl/pl/oferta/hit-duza-kawalerka-z-duzym-zielonym-ogrodem-ponad-60-m-ogrodu-ID4DZCp",
  "title": "HIT! Duża kawalerka z dużym zielonym ogrodem! | Ponad 60 m² ogrodu",
  "priceAmount": 584204.06,
  "priceCurrency": "PLN",
  "pricePerM2": 18599.3,
  "areaM2": 31.41,
  "rooms": 1,
  "floor": 0,
  "totalFloors": 5,
  "buildYear": null,
  "market": "primary",
  "advertiserType": "agency",
  "agencyName": "AS Nieruchomości i Finanse",
  "city": "Warszawa",
  "district": "Praga-Południe",
  "street": "Żupnicza",
  "region": "mazowieckie",
  "lat": 52.251347,
  "lng": 21.062424,
  "imagesCount": 6,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-11-16T08:32:41Z",
  "modifiedAt": "2026-11-16T08:32:41Z",
  "daysOnMarket": 4,
  "priceFormatted": "584 204,06 PLN",
  "pricePerM2Formatted": "18 599,3 PLN/m²",
  "agencyUrl": "https://www.otodom.pl/pl/firmy/biura-nieruchomosci/as-nieruchomosci-i-finanse-ID10869110",
  "coverPhoto": "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im1ydGJhZ2tnb3pydzEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.J9aiYVneClX-TOhhQyA-HE_8RiAD1NrAomv4j6hCF9I/image",
  "amenities": [
    "garden",
    "terrace",
    "parking",
    "secure building",
    "ground floor"
  ],
  "parkingType": "garage",
  "isPromoted": false,
  "isExclusiveOffer": false,
  "isPremium": false,
  "priceOnRequest": false,
  "subdistrict": "Kamionek",
  "province": "mazowieckie",
  "monthlyCharges": 400,
  "features": [
    "garage",
    "garden",
    "terrace",
    "lift",
    "anti_burglary_door",
    "entryphone",
    "monitoring"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im1ydGJhZ2tnb3pydzEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.J9aiYVneClX-TOhhQyA-HE_8RiAD1NrAomv4j6hCF9I/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjBzM3d5OGhkdG1scjEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.SxP2TOUr0KwxbgdRh89GcWc2mzhBxN9-0Leksttbah4/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImxsdWhrZ3JhM2VubS1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.UV3JumN4RRGlNURP-e7nK8fqle-OkuUsRgma9KBg8jo/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImRmMXZlanZqNXQzMzEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Oh1cwKtfP3IikqgeR9Ilif1M-gUbWOSuh1AsWKz2TvI/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImJkNDdybGFtbndtZzEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.5N183f7A7nqhST34O6sr_0G3UP3cBv2AAndV6czWTN8/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNmNzFqcm84OGVrMDMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.__pMy2Bj5-H1mfxaZJi44ZjBLj5lRg5TAeEplTvq8XI/image"
  ],
  "floorPlans": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjBzM3d5OGhkdG1scjEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.SxP2TOUr0KwxbgdRh89GcWc2mzhBxN9-0Leksttbah4/image"
  ],
  "description": "Fantastyczna Lokalizacja - dobra komunikacja ! :)\nPoznaj mieszkanie, które zachwyca od pierwszego wejrzenia. Jasne, przestronne, idealnie zaprojektowane – stworzone z myślą o komforcie codziennego życia.\nTo doskonałe miejsce dla tych, którzy cenią nowoczesność, funkcjonalność i dobrą energię we wnętrzach. Tu wszystko ma swoje miejsce, a każdy detal ma znaczenie.\nZobacz, zakochaj się i zamieszkaj!\nLokalizacja: Praga Południe - Kamionek:\n• Bliskość planowanej stacji II linii metra\n• W sąsiedztwie liczne sklepy , punkty usługowe, restauracje i kawiarnie\n• Dobra komunikacja miejska – autobusy i tramwaje w bezpośrednim zasięgu\n• W pobliżu stacja PKP Warszawa Wschodnia\n• Tereny zielone i parki – m.in. Park Skaryszewski i Kamionkowskie Błonia Elekcyjne\n• Łatwy dojazd do centrum miasta i innych dzielnic\n• Bliskość uczelni (np. SWPS) i obiektów sportowych\n• Dynamicznie rozwijająca się część Pragi z dużym potencjałem inwestycyjnym\nSpecjalnie dla Ciebie:\nMOŻLIWOŚĆ NEGOCJACJI HARMONOGRAMU\nMiejsce postojowe w SUPER CENIE - PROMOCJA DO KOŃCA MIESIĄCA\nZamieszkaj w miejscu, które daje więcej niż tylko dach nad głową – to przestrzeń do życia, relaksu i rozwoju.\nZadzwoń i umów się na prezentację!\nMieszkanie od dewelopera- w ofercie cała inwestycja/ różne metraże\n0% prowizji | 0% podatku PCC | zakup bezpośrednio od dewelopera\nZe mną uzyskujesz najlepsze warunki na rynku!\n- Możliwość wykończenia \"pod klucz\"\n- Darmowa pomoc rzetelnego doradcy kredytowego\n- Pomagam przejść przez cały proces – od wyboru po zakup\nZdjęcia w ogłoszeniu to wizualizacje o charakterze poglądowym. Nie stanowią oferty handlowej w rozumieniu art. 66 § 1 Kodeksu cywilnego.",
  "elevator": true,
  "heating": "urban",
  "buildingType": "apartment",
  "buildingMaterial": "other",
  "windowsType": "wooden",
  "ownership": "full_ownership",
  "constructionStatus": "to_completion",
  "scrapedAt": "2026-11-20T23:58:36Z"
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

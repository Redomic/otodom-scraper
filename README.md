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

Detail-enriched rows also carry `condition`, the finishing state the advertiser publishes, so you can spot a home you can move straight into without opening each ad. The Ursus flat returns:

```json
{ "condition": "do zamieszkania" }
```

Rows where the advertiser publishes no condition leave the key out.

Every row carries `province`, the voivodeship (województwo) Otodom publishes with the address — the column other Otodom exports call Province — so you can group a nationwide export by region or line the rows up with another Polish property dataset. It comes from Otodom's own location data on search cards and offer pages, so fast runs carry it too; a listing with no published province leaves the key out. A search across all Poland returns:

```json
{ "city": "Kraków", "province": "małopolskie" }
```

A Gdańsk studio in the same run returns `"province": "pomorskie"`.

Small-town and rural listings also carry `county`, the powiat Otodom places between the province and the town, so you can group a nationwide export by region and then by county. It comes from the same location data as the province, read from search cards and offer pages; city flats usually have no county and leave the key out. The lakeside house in Góry returns:

```json
{ "city": "Góry", "province": "lubuskie", "county": "sulęciński" }
```

Location goes down to the neighbourhood as well: `subdistrict` names the area inside the district, the name buyers actually search for. A Żabiniec rental in Kraków sits in Prądnik Biały district and returns:

```json
{ "district": "Prądnik Biały", "subdistrict": "Żabiniec" }
```

The Ursus flat returns `"subdistrict": "Skorosze"`, and a listing with no published neighbourhood leaves the key out.

Every row also carries `displayAddress`, the full address in one string — street, neighbourhood, district, city, county where published, and voivodeship — so a spreadsheet reads like the Otodom page without joining five separate columns. It is built from the location Otodom already publishes, so fast runs, monitor changes and listings pasted by URL carry it too; a listing with no published address leaves the key out. The Ursus flat returns:

```json
{ "displayAddress": "Obrońców Helu, Skorosze, Ursus, Warszawa, mazowieckie" }
```

The lakeside house in Góry returns `"displayAddress": "Góry, sulęciński, lubuskie"`, including its county.

Rental rows also carry `deposit`, the refundable kaucja a landlord asks for, when the advertiser publishes it on the offer page. For example, a two-room 40 m² flat in Kraków's Prądnik Biały listed at 2,600 PLN a month shows a 3,000 PLN deposit and 600 PLN in monthly charges.

Listing rows also carry `rentPrice`, the monthly rent or charge (czynsz) Otodom prints on the listing card — the same figure the offer page reports as `monthlyCharges`. It is read from the card, so fast runs with full details off and monitor changes carry it too; a card with no published charge leaves the key out. A two-room flat on Warsaw's Powiśle listed at 3,000 PLN a month returns:

```json
{ "priceAmount": 3000, "rentPrice": 1000 }
```

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

Need a flat with a balcony and parking? Set `amenities` to the features every returned listing must list on its card; Polish names such as `balkon` or `ogrod` work too. With `"amenities": ["balcony", "parking"]`, a two-room flat in Kraków returns:

```json
{ "title": "2-pokojowe mieszkanie 51m2 + balkon", "amenities": ["balcony", "parking"] }
```

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

If you need a field we do not flatten, set `includeRaw: true` to get `raw`, Otodom's complete original JSON object for each listing, with phone numbers and email addresses removed. It is off by default so ordinary rows stay small, and it works on search cards, full-detail runs, listings pasted by URL and monitor changes. The Kraków flat with a balcony above carries this excerpt in its `raw`:

```json
{
  "id": 66612363,
  "title": "2-pokojowe mieszkanie 51m2 + balkon",
  "totalPrice": { "value": 622739, "currency": "PLN" },
  "pricePerSquareMeter": { "value": 12155.75, "currency": "PLN" },
  "areaInSquareMeters": 51.23,
  "tags": [
    { "value": "BALCONY", "weight": 35 },
    { "value": "PARKING_SPOT", "weight": 15 }
  ]
}
```

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68608777,
  "url": "https://www.otodom.pl/pl/oferta/garderoba-balkon-z-widokiem-na-park-klimatyzacja-osobna-toaleta-ID4DShb",
  "title": "Garderoba | Balkon z widokiem na park | Klimatyzacja| Osobna toaleta",
  "priceAmount": 848000,
  "priceCurrency": "PLN",
  "pricePerM2": 13721.68,
  "areaM2": 61.8,
  "rooms": 3,
  "floor": 3,
  "totalFloors": 3,
  "buildYear": 1982,
  "market": "secondary",
  "advertiserType": "agency",
  "agencyName": "Open Estate Nieruchomości",
  "city": "Warszawa",
  "district": "Praga-Południe",
  "street": "21 Pułku Piechoty Dzieci Warszawy",
  "region": "mazowieckie",
  "lat": 52.222904,
  "lng": 21.081621,
  "imagesCount": 17,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-11-06T13:49:32Z",
  "modifiedAt": "2026-11-06T13:49:32Z",
  "daysOnMarket": 15,
  "rentPrice": 1390,
  "priceFormatted": "848 000 PLN",
  "pricePerM2Formatted": "13 721,68 PLN/m²",
  "agencyUrl": "https://www.otodom.pl/pl/firmy/biura-nieruchomosci/open-estate-nieruchomosci-ID10293798",
  "coverPhoto": "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNqczM3ZHhyaW9ldzItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.By-WLTlv3WtIN-Fv-NkwDtBklBXXLB-az3LgG2EJwZU/image",
  "amenities": [
    "separate kitchen",
    "balcony",
    "basement",
    "top floor"
  ],
  "isPromoted": false,
  "isExclusiveOffer": false,
  "isPremium": false,
  "priceOnRequest": false,
  "subdistrict": "Gocław",
  "province": "mazowieckie",
  "displayAddress": "21 Pułku Piechoty Dzieci Warszawy, Gocław, Praga-Południe, Warszawa, mazowieckie",
  "monthlyCharges": 1390,
  "agencyLicenseNumber": "23917",
  "condition": "do zamieszkania",
  "features": [
    "balcony",
    "basement",
    "separate_kitchen",
    "furniture",
    "fridge",
    "oven",
    "dishwasher",
    "entryphone"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNqczM3ZHhyaW9ldzItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.By-WLTlv3WtIN-Fv-NkwDtBklBXXLB-az3LgG2EJwZU/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InhjejY5ZWpjMjUxMjMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.ycqot-sm6xRSMlG_ktJ7--2x0pBVqEX4M1E5cb_bxu0/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNxNjlldnhrZWVobzMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.pS3KqcTJB4foYvTFA3QMdXLmgTaDj5R1Sa2q0LqTdSY/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InYwMDl4eWp3OHF6bjItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.DcDqGrigjqyFE0DTTmikxEUP91YX2jEmvb4GOSTl_Ko/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImcwY2V6MG5hd2k1cDMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.K7KY1yRJYwRY6__339hbPczYUbOJQ67H0apVQj-lNig/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InA5amQwNXZva2dxeTEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.898LDy3Ngwicimhk0OimL2BEdy28d8QXhtONpWALVDc/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im9yZTFoYzNibG5ybjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.G4WXqR9N0YWR5ABVptRyzf_dw_wQHAIYHveZXjRxO0M/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ink0MDloMDdodGU0ZjMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.jHzZkHy649Zku6eH1R5mjtagnDfR8M-MJCr4tJUPfTs/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjN3YWV2dWY4MDB0cjItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.UXvfsnhGXktg7txETiTZhzX7qBt5LciYBKQymKYHNKw/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InNub3pybm55a2dyYjItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.UrGDaEnKXf53Rj3hOxwJf-BTRQjkmN3OUhzCofgOTUs/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InVhOG02OHU1dG93YjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.fr4v9a62eobokeJTLo4CJIAYt37H53hS-V3iLmwKHqU/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im1iMmoxd2pkZzMweDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.1dWhh_87-Uyl3xbEySWPPLUmSn2Jnk8hLSQOSJ6Z_uk/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImI2b3FwcDh3dXYzazItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.0lTi7HtBHNKQoC2Kf7QJHG7HsilouxBuYkohSqEDBpg/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjU3czl0MW12NW5yZTMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.iYPWvvDFZMwx31jdqnbiWff4VPPKwiykTeT1Z13sHlA/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImdydmU2cm96eGVycy1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.BQ8SArJVOL2l2r6fCYCKApWSqRYmPaKYLVmNfYdsa1o/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjVoYmlyYThwb3dmdzEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.a0xo40uaw1BAmeLLcBvLhYHCJXr_khp15Qpq-80SWNA/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InBqd2I4aDRsc2lidjMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.r2hZ3vDvUFSVx-C6GB_oF7V59K69ZVCwTa2znlyfO9k/image"
  ],
  "description": "Na sprzedaż mieszkanie o powierzchni 61,8 m², położone przy ul. 21 Pułku Piechoty Dzieci Warszawy na warszawskim Gocławiu, w dzielnicy Praga-Południe. Mieszkanie znajduje się na 3. piętrze niskiego, 3-piętrowego budynku z wielkiej płyty, wybudowanego w 1982 roku. Budynek nie posiada windy.\nLokal składa się z trzech pokoi, oddzielnej kuchni, łazienki oraz osobnej toalety. Ma bardzo dobry, funkcjonalny rozkład, który pozwala wygodnie wykorzystać całą powierzchnię. Mieszkanie jest dwustronne, z ekspozycją wschód–zachód. Z balkonu rozpościera się widok na zieleń i park.\nMieszkanie wymaga remontu lub odświeżenia, w zależności od oczekiwań przyszłego właściciela. W lokalu znajduje się klimatyzacja, a do mieszkania przynależy również piwnica.\nStan techniczny i wyposażenie:\nMieszkanie jest w stanie do remontu/ drobnego remontu. Na podłogach znajduje się deska oraz wykładzina, okna są wykonane z PCV. W lokalu zamontowana jest klimatyzacja.\nOgrzewanie pochodzi z miejskiej instalacji centralnego ogrzewania, a woda dostarczana jest z wodociągu miejskiego. Miesięczny czynsz administracyjny wynosi około 1390 zł.\nUkład mieszkania:\nPowierzchnia 61,8 m² obejmuje trzy pokoje, oddzielną kuchnię, garderobę, łazienkę i osobną toaletę.\nOddzielna kuchnia zapewnia niezależną przestrzeń do przygotowywania posiłków, a osobna toaleta jest wygodnym rozwiązaniem przy większej liczbie domowników.\nMieszkanie jest dwustronne, z oknami skierowanymi na wschód i zachód. Dzięki temu wnętrza są doświetlone o różnych porach dnia. Z balkonu widok rozpościera się na zieleń i pobliski park.\nBudynek i dodatkowe informacje:\nBudynek został wybudowany w 1982 roku i ma trzy kondygnacje mieszkalne. Jest to niska zabudowa z wielkiej płyty.\nDo mieszkania przynależy piwnica, która zapewnia dodatkowe miejsce do przechowywania.\nNieruchomość stanowi spółdzielcze własnościowe prawo do lokalu.\nLokalizacja – Gocław, Praga-Południe:\nGocław to jedna z najbardziej rozwiniętych pod względem infrastruktury części Pragi-Południe. W najbliższej okolicy znajdują się sklepy, punkty usługowe, restauracje, placówki edukacyjne i przychodnie. Większość codziennych spraw można załatwić bez konieczności wyjeżdżania z osiedla.\nW pobliżu znajduje się Centrum Handlowe Gocław przy ul. gen. Augusta Emila Fieldorfa „Nila”, gdzie dostępne są sklepy i punkty usługowe. Nieco dalej położona jest Promenada, jedno z większych centrów handlowych na prawym brzegu Wisły, z szeroką ofertą sklepów, restauracji, kina i usług.\nGocław jest dobrze skomunikowany z pozostałymi częściami Warszawy. W okolicy znajdują się przystanki autobusowe, m.in. Os. Jantar i Algierska, z których można dojechać w kierunku innych części Pragi-Południe, centrum miasta oraz stacji metra.\nDojazd samochodem ułatwia bliskość głównych ulic dzielnicy, w tym ul. gen. Augusta Emila Fieldorfa „Nila”, Ostrobramskiej i al. Stanów Zjednoczonych.\nW okolicy nie brakuje również terenów zielonych. W pobliżu znajduje się Park nad Balatonem, z jeziorkiem, alejkami spacerowymi i miejscami do odpoczynku. To popularne miejsce spacerów i rekreacji mieszkańców Gocławia. W dalszej okolicy dostępne są również inne tereny zielone Pragi-Południe.\nGocław jest chętnie wybierany przez rodziny ze względu na rozwiniętą infrastrukturę osiedlową, dużą liczbę szkół, przedszkoli, sklepów i punktów usługowych. Jednocześnie jest to część Warszawy, w której wciąż można znaleźć spokojniejsze osiedla mieszkaniowe, z dala od ścisłego centrum miasta.\nW zasięgu najbliższej okolicy:\n- przystanki autobusowe – Os. Jantar i Algierska,\n- Centrum Handlowe Gocław – w pobliżu,\n- Centrum Handlowe Promenada – kilka minut samochodem,\n- Park nad Balatonem – w okolicy,\n- sklepy spożywcze, restauracje i punkty usługowe – w najbliższym sąsiedztwie,\n- szkoły i przedszkola – na terenie Gocławia.\nPodsumowanie:\n- 61,8 m², 3 pokoje\n- oddzielna kuchnia, łazienka i osobna toaleta\n- bardzo dobry, funkcjonalny rozkład\n- dwustronne mieszkanie, ekspozycja wschód–zachód\n- balkon z widokiem na zieleń i park\n- garderoba\n- klimatyzacja\n- piwnica\n- budynek z 1982 roku, wielka płyta\n- spółdzielcze własnościowe prawo do lokalu\n- czynsz administracyjny około 1390 zł\n- mieszkanie do remontu lub odświeżenia",
  "elevator": false,
  "heating": "urban",
  "buildingType": "block",
  "buildingMaterial": "concrete_plate",
  "windowsType": "plastic",
  "ownership": "limited_ownership",
  "constructionStatus": "ready_to_use",
  "scrapedAt": "2026-11-22T00:21:55Z"
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

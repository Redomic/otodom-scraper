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

Each row also carries the floor and the building's total floors, monthly charges (czynsz), heating, building type and material, ownership type, elevator, energy certificate, available-from date, the street, GPS coordinates, the agency name, the listing and update dates, the description and the photos. Listings also carry `daysOnMarket`, the whole number of days since the ad was first posted, so you can tell a stale listing from a fresh one at a glance. Every row also carries `asOf`, the exact moment the Actor observed it, so you can prove how current a row is even months after export — including rows in monitor mode.

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

Agency rows also include `agencyId`, Otodom's own number for the agency or developer, so you can group or join listings from the same seller across exports. The Ursus flat returns `"agencyId": 7988985`; private ads leave the field out.

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
  "id": 68559126,
  "url": "https://www.otodom.pl/pl/oferta/stegny-2-pok-43-8-m-9-pietro-widok-ID4DFmm",
  "title": "Stegny, 2 pok. 43,8 m², 9 piętro, widok",
  "priceAmount": 660000,
  "priceCurrency": "PLN",
  "pricePerM2": 15068.49,
  "areaM2": 43.8,
  "rooms": 2,
  "floor": 9,
  "totalFloors": 15,
  "buildYear": 1978,
  "market": "secondary",
  "advertiserType": "agency",
  "agencyName": "Wilsons Nieruchomości Sp. z o.o.",
  "city": "Warszawa",
  "district": "Mokotów",
  "street": "Iberyjska",
  "region": "mazowieckie",
  "lat": 52.178513,
  "lng": 21.05715,
  "imagesCount": 18,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-10-23T12:23:27Z",
  "modifiedAt": "2026-11-22T20:20:01Z",
  "daysOnMarket": 30,
  "rentPrice": 507,
  "priceFormatted": "660 000 PLN",
  "pricePerM2Formatted": "15 068,49 PLN/m²",
  "agencyUrl": "https://www.otodom.pl/pl/firmy/biura-nieruchomosci/wilsons-nieruchomosci-sp-z-o-o-ID3767159",
  "agencyId": 3767159,
  "coverPhoto": "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImcweTVxNG9nNTY3azItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Hzb9ykH5XX5HiMfzgBRNc2sQ-L8ak5sv8KzI5nOtXfA/image",
  "amenities": [
    "separate kitchen",
    "balcony",
    "storage room"
  ],
  "isPromoted": false,
  "isExclusiveOffer": false,
  "isPremium": false,
  "priceOnRequest": false,
  "subdistrict": "Stegny",
  "province": "mazowieckie",
  "displayAddress": "Iberyjska, Stegny, Mokotów, Warszawa, mazowieckie",
  "monthlyCharges": 507,
  "agencyLicenseNumber": "12165",
  "condition": "do zamieszkania",
  "features": [
    "lift",
    "balcony",
    "separate_kitchen",
    "usable_room",
    "entryphone"
  ],
  "media": [
    "internet",
    "cable television"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImcweTVxNG9nNTY3azItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Hzb9ykH5XX5HiMfzgBRNc2sQ-L8ak5sv8KzI5nOtXfA/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjBpdW85amtsYmtsaTItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.5LrpPwLpECrtfY59UIkdgby7Hdi9Y2ddps2C96YBEaQ/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImdmNDR6ZnJlZnQ3ZjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.KGQNc0W3g9UMAJaAHS8bPjVGrDmA1p3ktfUOB4PHu70/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InByN2U2NWw2dWs4ZjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.uJ6Z6i90MNtA-n5ZuO0FgFid4EI5RhedXHc9GsC8PdY/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ijh4enhodHY5Zm82eTEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.OR8lUp9UOoer4smLLQcUBCLtH4jY1yWkHTdAiU9fJDY/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im1saHc5eGp4azBxeDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.DX3oJ2KZXNrQaU-kLCJYdXBgZUVRZMYnuCFi0dksX18/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Img1bGtnNDkxdzRsdy1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.KsZIwciP3KYTXmxhNswgmfx3htL0xucKycTUYO_U6uc/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImZ2bnJoMDE2eW94YjMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0._1OaQbtzqvuM8GD4lKVf2NJqdLWSs7kQBE6HDPCmKcc/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNqeGwwdjlwd2ZudTEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.QOybyDBJP3Ha1KxmORhyRX_GHxe2IJ3tR3a68w66JaM/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjdkMGdtZXBvdG5qbS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.y7C2BGWPXWWcpIhruc8iZm1e_WrBNuUW9BjB20mUP7g/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImF1eWxnMnMwY2hqbzMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.3_RrsBpIlxQ-6MzRSA7GlDVwrkuZADhS_kZUdyOphWM/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Inl2cnh3MXVkZ3VuajItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0._4wy_XcYRwyGAFHyCI_E5P5XwsAOtcjjOpIlaIZiu8c/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImNpeThoZzBmam94ZS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.V3WZM-f4Y3zE_yBUSBtEquZVKmiqKZi_vg-7ZyeyVIE/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjgyYzY1ZXFsa2c4dDMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.xftHh950beL-DX-pvJP1_yHpcFI7NDM_hgsItvsRDvo/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImE3M3pmMWFsNHI5ZS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.qlVs-0SMy9afl1meLkguzyZzm6nz29xitYCL--LOVQg/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im9mcnVvdzFoeGIzdS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.LYguVLyuB5oZd2i-9H8CW4gsgBWkSNollkpSdV5I-ZM/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImVyaWdsNWFiNWYxdDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.FKZm4-gBw37wAbCa5uyMEE0LO6ppL0ugeCfTxNSAhUg/image",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InUyNzdxb2Jqeno0cS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.CfZrOXnep1G8hKb6E-DRF7XqoGz2OjMyhBApyTFoRX8/image"
  ],
  "floorPlans": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Img1bGtnNDkxdzRsdy1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.KsZIwciP3KYTXmxhNswgmfx3htL0xucKycTUYO_U6uc/image"
  ],
  "virtualTour": "https://my.matterport.com/show/?m=NXAgu1pBvUr",
  "description": "Możliwość uzyskania 3 pokoi/niski czynsz/księga wieczysta/ widok na miasto.\nZapraszam na prezentację mieszkania o powierzchni 43,8 m2 położonego przy ul Iberyjskiej 4 na Warszawskim Mokotowie - osiedle Stegny.\nTo doskonała oferta dla pary, niedużej rodziny lub w celach inwestycyjnych.\nBudynek oddany do użytku w 1978 roku, wyposażony w 2 windy, po wszelkich modernizacjach, administrowany przez SM Energetyka.\nMieszkanie znajduje się na 9 piętrze, dzięki czemu jest ono świetnie doświetlone , widok z okien niczym nie przysłonięty, strona północno- wschodnia. Wysokość mieszkania to 2,6m.\nMieszkanie ma duże możliwości aranżacyjne: można pozostać przy obecnym układzie (2 pokoje i osobna kuchnia) lub utworzyć pokój z aneksem kuchennym uzyskując 2 sypialnie.\nNa obecny układ mieszkania składa się:\n- pokój dzienny ok. 18 m2 plus loggia ok. 3,0 m2,\n- kuchnia ok. 7,0 m2,\n- sypialnia ok. 10 m2,\n- łazienka z wc ok. 3,2 m2\nDodatkową powierzchnię do przechowywania stanowią dwa schowki na korytarzu tuż obok mieszkania oraz wydzielona zamykana część korytarza.\nMieszkanie spółdzielcze własnościowe z założoną księgą wieczystą bez żadnych obciążeń, możliwość kredytowania zakupu.\nZ aletą jest niski czynsz do administracji - 507 zł.\nMieszkanie zostało odświeżone i przygotowane do zamieszkania:\n- parkiet dębowy odnowiony,\n- nowe drzwi Gerda o podniesionej klasie akustycznej,\n- wymieniono wszystkie drzwi wewnętrzne,\n- odświeżono łazienkę oraz instalacje,\n- zamontowano okna PCV.\nW okolicy:\n- liczne tereny rekreacyjne, place zabaw i boiska,\n- liczne punkty handlowo-usługowe min Auchan, Rossmann, Pepco nieco dalej CH Sadyba Best\n- na osiedlu znajdują się 2 szkoły publiczne oraz przedszkola.\n- bardzo dobrze rozwinięta komunikacja miejska, przy ul Sobieskiego znajduje się, kilka linii autobusowych oraz tramwajowych - dojazd do centrum zajmuje ok 20 minut.\nZapraszam na prezentację w dogodnym terminie.\nJeżeli Twój zakup wiąże się ze sprzedażą innej nieruchomości zadzwoń do mnie. Bezpiecznie przeprowadzę cały proces.\nŚwiadczymy naszym klientom usługę bezpłatnej konsultacji eksperta kredytowego oraz pomoc w uzyskaniu kredytu hipotecznego. Serdecznie zapraszam na prezentacje oraz do kontaktu telefonicznego.\nNiniejsza prezentacja oferty nie jest ofertą w rozumieniu Kodeksu Cywilnego i ma charakter wyłącznie informacyjny.",
  "elevator": true,
  "availableFrom": "2026-10-31",
  "heating": "urban",
  "buildingType": "block",
  "buildingMaterial": "concrete_plate",
  "windowsType": "plastic",
  "ownership": "limited_ownership",
  "constructionStatus": "ready_to_use",
  "scrapedAt": "2026-11-22T20:25:37Z",
  "asOf": "2026-11-22T20:25:37Z"
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

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

Each row also carries the floor and the building's total floors, monthly charges (czynsz), heating, building type and material, ownership type, elevator, energy certificate, available-from date, the street, GPS coordinates, the agency name, the listing and update dates, the description and the photos. Listings also carry `daysOnMarket`, the whole number of days since the ad was first posted, so you can tell a stale listing from a fresh one at a glance.

Rental rows also carry `deposit`, the refundable kaucja a landlord asks for, when the advertiser publishes it on the offer page. For example, a two-room 40 m² flat in Kraków's Prądnik Biały listed at 2,600 PLN a month shows a 3,000 PLN deposit and 600 PLN in monthly charges.

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68583116,
  "url": "https://www.otodom.pl/pl/oferta/2-pok-ustawne-do-wejscia-balkon-inwestycja-winda-0-prowizji-ID4DLBi",
  "title": "2 pok.| Ustawne | Do wejścia | Balkon | Inwestycja| Winda | 0 prowizji",
  "priceAmount": 730000,
  "priceCurrency": "PLN",
  "pricePerM2": 21313.87,
  "areaM2": 34.25,
  "rooms": 2,
  "floor": 3,
  "totalFloors": 8,
  "buildYear": 2022,
  "market": "secondary",
  "advertiserType": "agency",
  "agencyName": "Heimstaden",
  "city": "Warszawa",
  "district": "Mokotów",
  "street": "Bokserska 54",
  "region": "mazowieckie",
  "lat": 52.17223,
  "lng": 20.995308,
  "imagesCount": 16,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-10-30T13:46:16Z",
  "modifiedAt": "2026-11-16T20:00:18Z",
  "daysOnMarket": 17,
  "monthlyCharges": 206,
  "features": [
    "balcony",
    "garage",
    "lift",
    "usable_room",
    "furniture",
    "washing_machine",
    "dishwasher",
    "fridge",
    "stove",
    "oven",
    "entryphone",
    "monitoring",
    "closed_area"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjA1ZDFnMGlpeWUxNC1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.HK8mWV5ELxLtSCJNSJe5XBAS-n6jKdKtUpG7kj9SPlY/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InVhdHU0Z3AweWViMzMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.2wn08M8kT17F84zfe6t4B7l7fjFTl3fh3MbbRfhMSoA/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InNoaHR4N3ZueDF6cC1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.jytZ7b7sCW5uz0y1AhR4P_H306rN26UfLPnk7Ys4uqE/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IngzbHZqMXp1MXcyeC1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.isy4JGnnQrG3sT-uryX0gZv24XqiGZW-mulQx7kbjOY/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjJ3dnBkc3oyenNkZDEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.AkUjRr1qJFDlgr834qfv-FUXM5fb-Y-q6v42dRMYId4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImltYjg2MXNsb2wyNTMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.6R1Ee6bg7uINXx7Q2at9cKIpbX4GQVbks5x1sFtgemk/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImVqNGxjM25reXY1dzItQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Pj44CMcG7XgVEO3JEhzHUdx87dX928NN8yIkE3fbva4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Imw0b2lqM3MzNDFmZDEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.f6UV2HRa57LIWkPg_Bvqb3_Cbe04zjQJKnNVvfV_-So/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im9vdjZrdTI1Z20xNjItQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.p5s41ynkKL2BdhYy8MqeiRxKCDL3cVfE5Dx-EPoaAk4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InRyNHJtcmU3eXVmczMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.C9Df5YZ7cDe-pMRQi6V7YVyfDQwlDQbHX3KrcxzJDKY/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im1ra2s0NjV0MXcxay1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.dtowloe864NqzIdmkRMe_q7KdzToqMszDseRiSSrlng/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IndnbDBmbWZkeXBpODMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.s89w3vQrVG9C1MC_VSX--iEaPM5VB0W9If7r1FyD4Xc/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImYycjl5ZXZ5dHFmZTMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.NRRHPK9ajXNT793V_BkB4LdQQbXKMzLhKNNJHug3k18/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjJnM25lcDd5dG5kZjMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.0gGCqgcEjVUw3b4_lCCWj0hGl9vAq_cCcwJ5a8KrRCg/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjBheGZwbzI4amN1Ny1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.UTN2PfxVd4XSviBomt0cDk5VwAJBluMCODkOIGCLziU/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im83eWtxMzB0dXdmMTEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.SH7qh5a0kyrr9udbtzTwpJ-cQ5316keAM6c0ZHa3YB4/image;s=2048x1536;q=80"
  ],
  "description": "NIERUCHOMOŚĆ:\nPrezentujemy przytulne, gotowe do wprowadzenia (umeblowane i wyposażone) mieszkanie na zielonym osiedlu na Mokotowie.\nBez prowizji, bez pośredników, od właściciela.\nPowierzchnia 34,25 m2\nPiętro 3\nBalkon\nWykończone i umeblowane\n5 lat rękojmi!\nROZKŁAD POMIESZCZEŃ:\nSalon + aneks kuchenny - 15,82 m2\nSypialnia - 8,08 m2\nŁazienka - 4,55 m2\nHol - 4,97 m2\nBalkon - 5,24 m2\nChcesz obejrzeć mieszkania 2-pokojowe o innych rozkładach w tym samym budynu? Zadzwoń do mnie - przedstawię dostępne opcje!\nOSIEDLE:\nMoko Botanika – nowoczesne mieszkania na zielonym Mokotowie!\nZamieszkaj w wyjątkowej inwestycji Moko Botanika, położonej przy ul. Bokserskiej – w sercu Mokotowa, jednej z najbardziej pożądanych dzielnic Warszawy.\nTo idealne miejsce dla osób, które cenią komfort, styl i bliskość natury.\nNowoczesna architektura, wysoki standard wykończenia i przemyślane układy mieszkań – od funkcjonalnych kawalerek po przestronne, pięciopokojowe apartamenty – sprawiają, że każdy znajdzie tu przestrzeń idealną dla siebie. Każde mieszkanie posiada loggię lub taras, które pozwalają cieszyć się chwilą w otoczeniu zieleni.\nInwestycja wyróżnia się ekologicznymi rozwiązaniami – panele fotowoltaiczne oraz certyfikat BREEAM potwierdzają jej energooszczędny charakter. Dodatkowo projekt zdobył prestiżowe wyróżnienie Top Site Innovation, które potwierdza najwyższą jakość i innowacyjność wykonania.\nLOKALIZACJA:\nW pobliżu znajdują się sklepy, punkty usługowe, szkoły i przedszkola, a także dogodne połączenia komunikacyjne – przystanki autobusowe i stacje kolejowe zapewniają szybki dojazd do centrum miasta.\nDodatkowym atutem jest bliskość centrum handlowego Westfield Mokotów (Galeria Mokotów) oraz terenów zielonych i rekreacyjnych, idealnych do spacerów i aktywnego wypoczynku.\nCENA:\nmieszkanie 730 000 zł\nmiejsce parkingowe w garażu podziemnym - 55 000zł/szt.\nkomórka lokatorska (o różnej powierzchni) -7500 zł/m2 (nieobowiązkowa).\nZadzwoń i umów się na prezentację – mieszkanie jest dostępne od zaraz.\nOgłoszenia na portalu nie stanowią oferty w rozumieniu art. 66 Kodeksu cywilnego.\nZdjęcia mieszkania są poglądowe- zapraszamy na prezentację!\nO SPRZEDAJĄCYM:\nWłaścicielem nieruchomości jest Heimstaden – wiodąca europejska firma działająca w sektorze nieruchomości mieszkaniowych. Grupa jest obecna w Norwegii, Szwecji, Danii, Finlandii, Wielkiej Brytanii, Niemczech, Holandii, Czechach i Polsce. Heimstaden dysponuje około 159 tys. mieszkań o łącznej wartości około 30 mld euro. Firma koncentruje się na nabywaniu, budowie, sprzedaży i zarządzaniu budynkami w oparciu o najwyższej jakości standardy współpracy z partnerami i klientami.\nENG\nPROPERTY:\nWe present a cozy, ready-to-move-in (furnished and equipped) apartment in a green neighborhood in Mokotów.\nNo commission, no agents, from the owner.\nArea 34.25 m2\n3rd floor\nBalcony\nFinished and furnished\n5-year warranty!\nROOM LAYOUT:\nLiving room + kitchenette - 15.82 m2\nBedroom - 8.08 m2\nBathroom - 4.55 m2\nHallway - 4.97 m2\nBalcony - 5.24 m2\nWould you like to see 2-bedroom apartments with different layouts in the same building? Give me a call! I'll show you the available options!\nDEVELOPMENT:\nMoko Botanika – modern apartments in green Mokotów!\nLive in the unique Moko Botanika development, located on Bokserska Street – in the heart of Mokotów, one of the most desirable districts of Warsaw.\nIt is the perfect place for people who value comfort, style, and proximity to nature.\nModern architecture, high standard of finish and well-thought-out apartment layouts – from functional studios to spacious five-room apartments – mean that everyone will find the perfect space for themselves here.\nThe investment stands out for its ecological solutions – photovoltaic panels and BREEAM certification confirm its energy-efficient character. In addition, the project with\nPRICE:\napartment PLN 730 000\nparking space in the underground garage PLN 55,000/each\nstorage room (various sizes) PLN 7,500/m² (optional).\nCall and make an appointment for a viewing – the apartment is available immediately.\nAdvertisements on the portal do not constitute an offer within the meaning of Article 66 of the Civil Code.\nThe photos of the apartment are for illustrative purposes only – we invite you to a presentation!\nABOUT THE SELLER:\nThe property is owned by Heimstaden – a leading European company operating in the residential real estate sector. The group is present in Norway, Sweden, Denmark, Finland, the United Kingdom, Germany, the Netherlands, the Czech Republic, and Poland. Heimstaden has approximately 159,000 apartments with a total value of approximately EUR 30 billion. The company focuses on the acquisition, construction, sale, and management of buildings based on the highest standards of cooperation with partners and customers.",
  "elevator": true,
  "availableFrom": "2026-10-30",
  "heating": "urban",
  "buildingType": "block",
  "buildingMaterial": "reinforced_concrete",
  "windowsType": "plastic",
  "ownership": "full_ownership",
  "constructionStatus": "ready_to_use",
  "scrapedAt": "2026-11-16T20:05:03Z"
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

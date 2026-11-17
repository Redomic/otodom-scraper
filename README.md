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

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68299258,
  "url": "https://www.otodom.pl/pl/oferta/przepiekny-apartament-ul-mokotowska-srodmiescie-ID4CzKV",
  "title": "Przepiękny apartament ul. Mokotowska Śródmieście",
  "priceAmount": 9500,
  "priceCurrency": "PLN",
  "pricePerM2": null,
  "areaM2": 76,
  "rooms": 3,
  "floor": 2,
  "totalFloors": 4,
  "buildYear": 1904,
  "market": null,
  "advertiserType": "agency",
  "agencyName": "OtoVilla Real Estate",
  "city": "Warszawa",
  "district": "Śródmieście",
  "street": "Mokotowska",
  "region": "mazowieckie",
  "lat": 52.22261,
  "lng": 21.019772,
  "imagesCount": 18,
  "propertyType": "flat",
  "dealType": "rent",
  "createdAt": "2026-08-10T13:19:53Z",
  "modifiedAt": "2026-11-17T22:45:46Z",
  "daysOnMarket": 99,
  "agencyUrl": "https://www.otodom.pl/pl/firmy/biura-nieruchomosci/otovilla-real-estate-ID7911275",
  "coverPhoto": "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImppZnJjOXhubThlOC1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.eyuBhlqFMiLd888IKA_598bG25wBuwLLDRdnDun-nyk/image;s=2048x1536;q=80",
  "amenities": [
    "air conditioning"
  ],
  "subdistrict": "Śródmieście Południowe",
  "monthlyCharges": 500,
  "agencyLicenseNumber": "24573",
  "features": [
    "air_conditioning",
    "dishwasher",
    "furniture",
    "fridge",
    "stove",
    "oven",
    "washing_machine",
    "tv",
    "entryphone"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImppZnJjOXhubThlOC1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.eyuBhlqFMiLd888IKA_598bG25wBuwLLDRdnDun-nyk/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ind4c2V1MHk3cGl5dDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.pOuRxlAmnkH9hWZos3Zb8vAXMPlQcZje6FsvxkMRcIg/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImN4bmV6YXI2MDhyNC1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.5fMGMW_5irTiXtwNEmns6AYAuzQcKwoT6A4FY2VRUjQ/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImYwbHJ1czIwMXRnbDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.CfoUwbwmXKCvA7Ow8rzIkTa8y6Uo-be2z9robSUZc_M/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjcwMmtyNWR6eDE0ZjEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.oVJQnwKPvdSjGONApLtbO7d4_ItIm5L-13-wEoaaq3I/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjN5cm52NnhteDM2bDMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.-77V1sbAyNEka5oSnND5lKUIuHtrgGXf1MjNidOKe8g/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Inc5MXRiYTJ5ZjhxdzItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.0LVJwST_239tEWwyCbzLhcheqsqH5G7-AxIdTrhaTbw/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Indxazk0eHhuMmw5dDMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.F4YFo5w5vOQ264fRF3nzlumEeagKpCQcI1rjuwiDvQU/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNlemp1aWx6bnJydDItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.-w1kNmCKxPlT2Cqxq3EmlQXf-xI6wYyjgHzpp2CNTFw/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjdxY3J3bXdsYWN2MjItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.roI2hw_gLWx9rUSs6MaJBfXa_fRhweh2nmQfxsVC39M/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InRoMW9oc3hhMHcwNzEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.8ZPfcs4SrursMFD_xC2-rNwlW5LWui1aTGIz71w3x9c/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Im96ejVob2lpNHZ4dDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.UKI897p4LaADlnZQngplo90DKutadOHsYV-QRTe_DS8/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjYxM211dDZycHd5cjItRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.iTkNrWVP1D6Gye4CDTNJzxxb1mGRq69ptI32hjA84QY/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImQ0dmdlZnNmd2NmOTEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.p1pMhVI0dFUv6iHSDRPixBfqnJIBqqJKyLYgDB7dHSA/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImY4ZmtnN3hxaG1jZDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Lz3A7_h5HHhVf-vhNBJbXpzkS2ewV49amgYd4LVM3PU/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InV5MnFjZ3BybXNrZjMtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.Emi2rRMbNnT5rhPZ8Hv2eZso8c9JZULp9i2aIGY7nSU/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImtuajJ4ZGNoa3h4cS1FQ09TWVNURU0iLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.IkAEahGuKVtEjv7pu-QSmg9cGOOa5pc51nbbGpa8zyg/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImZqazd6dmU1bm43MDEtRUNPU1lTVEVNIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.YmKbHPI8522FakW8460iZXQolrcNBgyVRpmHmnN1QQo/image;s=2048x1536;q=80"
  ],
  "description": "Nowoczesny apartament w przepięknej kamienicy przy ul. Mokotowskiej w Warszawie\nTrzypokojowy apartament z dwiema łazienkami wykończony w bardzo wysokim standardzie, całkowicie umeblowany i gotowy do wprowadzenia.\nLokalizacja:\nWyjątkowy adres na mapie Warszawy. Kamienica przy ul. Mokotowska oferuje idealne warunki dla osób poszukujących ekskluzywnych apartamentów. Ulica ta należy dziś do najbardziej prestiżowych lokalizacji, chętnie wybieranych przez polskich projektantów, artystów oraz właścicieli butików i eleganckich lokali gastronomicznych.\nMokotowska mieści się w południowej części Śródmieścia, zapewniając dogodny i szybki dojazd do innych dzielnic zarówno komunikacją miejską, jak i samochodem. Podróż autem do Port Lotniczy im. Fryderyka Chopina zajmuje około 20 minut. W bezpośrednim sąsiedztwie znajdują się Park Ujazdowski, Łazienki Królewskie, Parlament oraz Hotel Sheraton.\nBudynek:\nCzteropiętrowa, całkowicie odrestaurowana kamienica zachwyca starannie odtworzonymi detalami historycznej architektury oraz wyjątkową dbałością o estetykę.\nBudynek wzniesiony w 1904 roku dla hrabiego Rodryga Mroczkowskiego, znany jako „Krakowiacy i Górale”, wyróżnia się charakterystycznymi rzeźbami zdobiącymi główną elewację.\nRozkład:\nApartament o powierzchni 74 m² zlokalizowany jest na 2. piętrze. Składa się z przestronnego salonu z otwartą kuchnią, sypialni z prywatną łazienką, gabinetu, łazienki z prysznicem oraz hallu z dużą szafą wykonaną na wymiar. Wnętrze zostało zaprojektowane przez architekta, co zapewnia spójny styl, komfort i funkcjonalność.\nDodatkowe informacje i koszty:\nApartament jest klimatyzowany i w pełni wyposażony.\nMedia płatne dodatkowo według zużycia – w formie zaliczki 400 zł miesięcznie – oraz prąd i opcjonalnie internet.\nZapraszam na prezentację\nModern apartment in a beautiful historic tenement house on Mokotowska Street in Warsaw\nA three-room apartment finished to a very high standard, fully furnished and ready to move in.\nLocation:\nA truly exceptional address in the heart of Warsaw. The tenement house on ul. Mokotowska offers an ideal setting for those seeking an exclusive residence. Today, this street is one of the most desirable locations for Polish designers, artists, as well as owners of boutique shops and upscale dining venues.\nMokotowska is situated in the southern part of Śródmieście, ensuring quick access to other districts both by public transport and private car. The drive to Warsaw Chopin Airport takes approximately 20 minutes. The building is located near Ujazdowski Park, Łazienki Królewskie, the Parliament of Poland and the Sheraton Hotel.\nBuilding:\nA four-storey, fully restored tenement house, showcasing original architectural details and outstanding craftsmanship.\nBuilt in 1904 for Count Rodryg Mroczkowski, the building is also known as “Krakowiacy i Górale” due to the characteristic sculptures adorning its main façade.\nLayout:\nThe 74 m² apartment is located on the 2nd floor. It consists of a spacious living room with an open kitchen, a bedroom with an en-suite bathroom, a study, a bathroom with a shower, and a hall with a large custom-made wardrobe. The interior was designed by an architect, ensuring a cohesive style, comfort, and high functionality.\nAdditional information and costs:\nThe apartment is air-conditioned and fully equipped.\nUtilities are paid additionally based on consumption – in the form of a 400 PLN monthly advance payment – plus electricity and optional internet.\nTreść niniejszego ogłoszenia nie stanowi oferty handlowej w rozumieniu Kodeksu Cywilnego.\nOferta wysłana z programu IMO dla pośredników",
  "elevator": false,
  "heating": "urban",
  "buildingType": "tenement",
  "windowsType": "wooden",
  "constructionStatus": "ready_to_use",
  "scrapedAt": "2026-11-17T22:57:21Z",
  "changeType": "new",
  "priceHistory": [
    {
      "priceAmount": 9500,
      "priceCurrency": "PLN",
      "pricePerM2": null,
      "observedAt": "2026-11-17T22:57:21Z"
    }
  ],
  "priceDirection": "new",
  "priceChangePct": null
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

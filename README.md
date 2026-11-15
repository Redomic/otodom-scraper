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

Each row also carries the floor and the building's total floors, monthly charges (czynsz), heating, building type and material, ownership type, elevator, energy certificate, available-from date, the street, GPS coordinates, the agency name, the listing and update dates, the description and the photos.

## Sample output
Real rows from a recent run: [`sample.json`](sample.json) · [`sample.csv`](sample.csv)

```json
{
  "id": 68623536,
  "url": "https://www.otodom.pl/pl/oferta/sloneczne-3-pokoje-6-min-od-metra-idealny-uklad-ID4DV7e",
  "title": "Słoneczne 3 pokoje 6 min od metra. Idealny układ",
  "priceAmount": 759000,
  "priceCurrency": "PLN",
  "pricePerM2": 12524.75,
  "areaM2": 60.6,
  "rooms": 3,
  "floor": 3,
  "totalFloors": 12,
  "buildYear": 1978,
  "market": "secondary",
  "advertiserType": "agency",
  "agencyName": "PL Nieruchomości Wołomińskie Centrum Nieruchomości",
  "city": "Warszawa",
  "district": "Targówek",
  "street": "Heleny Junkiewicz",
  "region": "mazowieckie",
  "lat": 52.267597,
  "lng": 21.054724,
  "imagesCount": 20,
  "propertyType": "flat",
  "dealType": "sale",
  "createdAt": "2026-11-11T18:05:17Z",
  "modifiedAt": "2026-11-15T19:36:12Z",
  "monthlyCharges": 1300,
  "features": [
    "basement",
    "lift",
    "anti_burglary_door",
    "entryphone"
  ],
  "photos": [
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjNqeGswYjV2OWEwOS1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.HZtlmIAYIonkNH90lwimzjYZxZ3PhvY226RYU--d-Ps/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ijc5b2xkNWRodWRmZjEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.8F4h6EKr8OsAflbcsjXgJtyUxMfT4Ye8Olwa65M3qb4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InppODM1aHBqcmNqYzMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.ccjslcV7oCxmT9xKuPgtZEhpd7ZfwmTAzmk6R0nZ2x4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjEzbW10NWxpeHQwOTMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.dvVV2uTmk91TCsXR14deAF2ZvstxBlH5jvbYpWN6wzo/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjhjMDE4OHcxNW9oby1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.-310BMLnDy-o1fnUtFBLkdIg6Hi3G7Vp_nZwEtyZ4XU/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjZld2FvcDhiZHF6NC1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.dTIRIRj8HdohC_NerXy7LF-A1N6uxvU1fu8oWTTwHoU/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImhtemltMnpsMTZ6bS1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.IqXecXDpRcqtQW0UrhoK-3albcLuz5axDzcjdg18ZWQ/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImZqY3I3YXF5emt0ODEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.aOOF89HO1ldpPcIXv5o17g4DMdrlyH_XOe8lMEsm6gs/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImhuMmp6ZTExeTdwYzItQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.nUO-1BPK2WKQBZsyqj1lxFsqBlK5SRpyt4_3q2Qal5M/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Iml6cGs2ZGVhd2cyNTEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.4-phoxy5Xu8CQjwWOwZEkh7gAKulF3kWAWkrxZ7TAF8/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ijc2b3Q5b2QycHRjcjEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.pZlqWnnZkTfvnzEx7yJcYz87AE4yxRyY2EY21YQvGCA/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjExZmFjcGhzbG85NTMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.eGeQCHcAEIPX7_9lwR19q89tn23xZyKubIH6JIrt77E/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InpiMDA4cm4zYjF5ei1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.LSwEgcSbq8VHaYoZftACH_gFv3t-VX0lUUhAx99rC6c/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6Ijl6dmZ2dThzOWsxdi1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.hsOIGprRFn1vy0qZ2FpxOsvcYPLsXI7gR-CCqxweUHk/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6ImV3azJuZ295NTBlazItQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0._iJeWn8tsPTpMorMJirxpYG1NejUUWFvciqnRJfEc2Y/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjN0c3k5OTBpYjc3cTEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.G3HlZzSnxEQnojntlSTj3A6DbgZojcvEHYLb79JqGzE/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6IjFtbDEyZzVuMWJ1ay1BUEwiLCJ3IjpbeyJmbiI6ImVudmZxcWUxYXk0azEtQVBMIiwicyI6IjE2IiwiYSI6IjAiLCJwIjoiMTAsLTEwIn1dfQ.cOH8Y3DPurz75VLXAuKOWGVe82fGnGgRFrccWaxufo4/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InBhcmpkN2owODlwdTEtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.8mR9cerq3D1_xqR1jk1uaQbnHCM0K9xa7uByth2bYPQ/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InJudHVjb2lueGhkbDItQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.DBBrwnOBjMfn-4AJBO9ZYSCtUyWHx3ExgRFWvX7RFBM/image;s=2048x1536;q=80",
    "https://ireland.apollo.olxcdn.com/v1/files/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbiI6InF3YnJiajl2OGZmZTMtQVBMIiwidyI6W3siZm4iOiJlbnZmcXFlMWF5NGsxLUFQTCIsInMiOiIxNiIsImEiOiIwIiwicCI6IjEwLC0xMCJ9XX0.giBY1y9Y5w_AcfTZ0zb0ukC88YvCEOGGTfuGcPNq2nQ/image;s=2048x1536;q=80"
  ],
  "description": "Słoneczne 3 pokoje 6 min od metra | Idealny układ do własnej aranżacji Targówek\nSzukasz mieszkania z potencjałem, w którym każdy metr kwadratowy pracuje na Twoją wygodę? Poznaj 60,6 m przy ul. Heleny Junkiewicz nieruchomość o ponadczasowym, w pełni rozkładowym układzie, położoną w jednej z najlepiej skomunikowanych i najbardziej zielonych części Targówka Mieszkaniowego.\nMieszkanie znajduje się na 3. piętrze w zadbanym, 12-piętrowym budynku po termomodernizacji, z odnowioną elewacją i wyremontowanymi balkonami. Kameralne sąsiedztwo zapewniają zaledwie 4 lokale na piętrze.\nFunkcjonalny rozkład:\nDo każdego z pomieszczeń wchodzisz bezpośrednio z przedpokoju brak pokoi przechodnich gwarantuje pełną swobodę wszystkim domownikom:\nSalon (ok. 17 m): doskonale doświetlony dzięki południowej ekspozycji, z wyjściem na przytulną loggię (2,6 m) z widokiem na zieleń.\n2 niezależne sypialnie (po ponad 10,5 m): ustawne, chłodniejsze pokoje z oknami na północ idealne na strefę nocną, pokój dziecięcy lub gabinet do pracy zdalnej.\nOsobna, widna kuchnia: daje pełną niezależność podczas gotowania (z opcją otwarcia na salon przy remoncie).\nOddzielna łazienka i WC: ogromne ułatwienie w codziennym funkcjonowaniu rodziny lub przyjmowaniu gości.\nWewnętrzna garderoba: praktyczna przestrzeń do przechowywania, która pozwala zrezygnować z wielkich szaf w pokojach.\nDodatkowa przestrzeń: do lokalu przynależy piwnica (ok. 1 m).\nCzysta karta urządzasz bez dopłacania za cudzy remont.\nLokal wymaga generalnego remontu. To dobra sytuacja, jeśli nie chcesz przepłacać za wykończenie, które i tak planujesz zmienić. Zyskujesz pełną kontrolę nad budżetem, standardem instalacji i finalnym stylem wnętrza.\nLokalizacja, która oszczędza Twój czas.\nOsiedle wyróżnia się przemyślaną, niską intensywnością zabudowy między blokami dominują drzewa, alejki i place zabaw, a nie ciasne parkingi.\nSzybki dojazd: 6 minut spacerem do stacji Metro Targówek Mieszkaniowy, 1,5 minuty do przystanku autobusowego. Do centrum lub na Stare Miasto dotrzesz w kilkanaście minut komunikacją miejską albo w 10 minut rowerem.\nWszystko pod ręką: tuż obok budynku działają Biedronka, Rossmann, apteka i kancelaria notarialna. W promieniu 5 minut pieszo masz Lidla, bibliotekę, punkty gastronomiczne oraz rozbudowaną sieć ścieżek rowerowych i biegowych.\nPełne zaplecze edukacyjne i kulturalne: w bezpośrednim sąsiedztwie znajdują się żłobki, przedszkola, szkoły podstawowe i licea, a także Park Wiecha i Teatr Rampa.\nZainteresowana/y?\nZadzwoń i umów się na prezentację. Zobacz ten układ na żywo i oceń jego możliwości bez pośpiechu i zbędnych formalności.\nLokalizacja: Warszawa/Targówek\nCena: 759 000 zł ( 12 525 zł/m2 )\nCena podlega negocjacji\nPowierzchnia: 60,6 m2,\nPiętro: 3 / 12\nRok budowy: 1978,\nBudynek: blok,\nPOKOJE:\nLiczba pokoi: 3,\nPokój 1: 16,6 m2, podłoga: panele, strona świata: płd,\nPokój 2: 10,5 m2, podłoga: panele, strona świata: płn,\nPokój 3: 10,8 m2, podłoga: panele, strona świata: płn,\nKUCHNIA:\nPowierzchnia: 8,2 m2,\nAmfilada: Nie;\nRodzaj kuchni: widna i zamknięta,\nPodłoga: terakota,\nOkno kuchni wychodzi na: płd,\nPołożona glazura: jest;\nŁAZIENKA:\nLiczba łazienek: 1,\nPowierzchnia: 3,7 m2,\nPodłoga: terakota,\nPołożona glazura: jest;\nZ oknem;\nWC:\nLiczba: 1,\nPowierzchnia: 1,5 m2,\nPodłoga: terakota,\nPołożona glazura: jest;\nCzynsz od administracji: 1300 zł,\nstan lokalu: do remontu, głośność: ciche, wolne od: 2026-11-12, ochrona: monitoring, os. niezamknięte, domofon, winda, TV kablowa, Internet, piwnica 1 m2, garderoba 2 m2, liczba stron: dwustronne, materiał: płyta, ciepła woda: miejska, ogrzewanie: miejskie,\nWięcej informacji oraz możliwość umówienia spotkania:\nAgent prowadzący:\nPaweł Wcisło\nGrupa PL Nieruchomości - Wołomińskie Centrum Nieruchomości\nGodziny otwarcia: pon. - pt.: 9:00 - 19:00; sob.: 9:00 - 14:00\n--- Zapraszamy ---",
  "elevator": true,
  "availableFrom": "2026-11-12",
  "heating": "urban",
  "buildingType": "block",
  "buildingMaterial": "concrete_plate",
  "windowsType": "plastic",
  "ownership": "full_ownership",
  "constructionStatus": "to_renovation",
  "scrapedAt": "2026-11-15T19:44:26Z"
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

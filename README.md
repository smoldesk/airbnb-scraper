# Airbnb Scraper

![Airbnb Scraper](https://raw.githubusercontent.com/smoldesk/apify-scrapers/main/assets/airbnb/banner.webp)

Scrape Airbnb listings for any city, neighborhood or list of listing URLs, with the price for your dates, full listing details, guest reviews and the 12-month availability calendar. No login, no browser, no API key. Runs on [Apify](https://apify.com/smoldesk/airbnb-scraper); you pay per result.

## What you get

- **Listings**: name, type, city, coordinates, rating, review count, superhost and guest-favorite flags, photos, and the **price for your stay** (total, per night, nights, original price, and a breakdown with discounts, cleaning and resort fees) in 20 currencies.
- **Full details** (on by default): description (also as Markdown), every amenity by group, house rules, safety and property info, sleeping arrangements, capacity, bedrooms, beds and bathrooms, six category ratings and the star distribution, every photo with its room, and the host (superhost, verified, years hosting, response rate and time, co-hosts).
- **Guest reviews**: text, rating, date, language, English translation, stay length and the host's response.
- **Availability calendar**: up to 12 months of open and booked nights, check-in/check-out days and minimum/maximum stay, with a booked-share summary per listing.
- **Search filters**: dates, adults, children, infants, pets, type of place, price per night, bedrooms, beds, bathrooms. Or paste a search URL copied from Airbnb.

## Run it

Open the [Airbnb Scraper on Apify](https://apify.com/smoldesk/airbnb-scraper), type a place and your dates and click Start. Or call it from code with your [Apify token](https://console.apify.com/settings/integrations):

```bash
curl -X POST "https://api.apify.com/v2/acts/smoldesk~airbnb-scraper/run-sync-get-dataset-items?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"locationQueries": ["Lisbon"], "checkIn": "2026-12-10", "checkOut": "2026-12-14", "adults": 2, "maxListingsPerSearch": 20}'
```

Python (`pip install apify-client`):

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_TOKEN")
run = client.actor("smoldesk/airbnb-scraper").call(run_input={
    "locationQueries": ["Austin, TX"],
    "checkIn": "2026-12-10",
    "checkOut": "2026-12-14",
    "roomTypes": ["entire"],
    "maxListingsPerSearch": 100,
})
for row in client.dataset(run["defaultDatasetId"]).iterate_items():
    if row["type"] == "listing":
        print(row["name"], row["price"]["total"], row["price"]["perNight"], row["rating"])
```

Reviews and calendars for your competitors' listings (JavaScript, `npm install apify-client`):

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_TOKEN' });
const run = await client.actor('smoldesk/airbnb-scraper').call({
    startUrls: [{ url: 'https://www.airbnb.com/rooms/53997462' }],
    includeReviews: true,
    maxReviewsPerListing: 100,
    includeCalendar: true,
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
const calendar = items.find((r) => r.type === 'calendar');
console.log(`booked share next 12 months: ${calendar.occupancyRate}`);
```

## Sample output

```json
{
  "type": "listing",
  "id": "736570764306678404",
  "url": "https://www.airbnb.com/rooms/736570764306678404",
  "name": "Modern chic homely central one bedroom apartment",
  "city": "Greater London",
  "price": {"total": 1137, "originalTotal": 1383, "perNight": 345.53, "nights": 4, "currency": "USD"},
  "rating": 4.8,
  "reviewsCount": 41,
  "isSuperhost": true,
  "personCapacity": 2,
  "bedrooms": 1,
  "ratings": {"cleanliness": 4.8, "accuracy": 4.9, "checkIn": 5, "communication": 5, "location": 5, "value": 4.7},
  "host": {"name": "Lisa", "yearsHosting": 4, "responseRate": "100%"}
}
```

In our test, 240 listings in London and Bali with full details, 667 reviews and 240 calendars took 5 minutes.

## Pricing

| Per 1,000 | Free | Starter | Scale | Business |
|---|---|---|---|---|
| Listing | $2.00 | $1.50 | $1.20 | $1.00 |
| Full listing details | $2.00 | $1.50 | $1.20 | $1.00 |
| Guest review | $2.00 | $1.60 | $1.30 | $1.00 |
| Availability calendar (12 months) | $4.00 | $3.50 | $3.00 | $2.50 |

## Limits

- About 270 listings per search (Airbnb's limit). Search neighborhoods or split by price for more.
- Airbnb no longer shows nightly prices in its calendar, so the calendar has availability and stay rules only. Set dates for the stay price.
- `daysBlocked` counts nights booked or blocked by the host; Airbnb doesn't tell them apart.
- Check-in times and exact addresses are shown only after booking, so they're not included.

## More scrapers

Airbnb Scraper is one of 20 no-login scrapers by Smoldesk Studios. See the full list: [smoldesk/apify-scrapers](https://github.com/smoldesk/apify-scrapers).

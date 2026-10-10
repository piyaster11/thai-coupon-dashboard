# Updating the dashboard

All content lives in `deals.json`; `index.html` never needs editing.

## Schema
```json
{
  "date": "YYYY-MM-DD",          // list date, shown in header; "expires soon" = expiry within 3 days of this
  "deals": [{
    "rank": 1,
    "brand": "Grab",              // Grab | Airalo | Klook | TikTok Shop | Shopee (others get grey chip)
    "category": "Food",           // Food | Rides | Travel | Shopping
    "title": "Short name",
    "discount": "฿50 off",
    "minSpend": "฿300",
    "expiry": "2026-10-31",       // or null if no end date
    "codes": [{"code": "CARDXFOOD", "label": "short note"}],  // [] = "No code, collect in app"
    "why": "Generic note, e.g. Needs a CardX card",
    "source": "https://..."
  }]
}
```
Keep it public-safe: deals only, no personal data.

## Daily update
```bash
cd /workspace/thai-coupon-dashboard
git pull
# edit deals.json
python3 -m json.tool deals.json > /dev/null && git add deals.json && git commit -m "Deals for $(date +%F)" && git push
```
GitHub Pages redeploys in ~1 minute.

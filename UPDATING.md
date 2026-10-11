# Updating the dashboard

All content lives in `deals.json`; `index.html` usually does not need editing.

The page builds a **brand filter row** automatically from the `brand` values in today's `deals.json` (sorted by deal count, with brand chip colors). No schema change is required — just keep using a consistent `brand` string that matches a key in `COLORS` in `index.html` when you want a colored chip. Users can deep-link with `?brand=Grab` (and `?lang=th`). Brand and category filters combine with AND.

## Schema
```json
{
  "date": "YYYY-MM-DD",          // list date, shown in header; "expires soon" = expiry within 3 days of this
  "deals": [{
    "rank": 1,                    // 1-10 = Top 10, 11+ = extra picks
    "brand": "Grab",              // powers brand filter chips + COLORS in index.html (unknown → grey)
    "category": "Food",           // Food | Restaurants | Supermarkets | Shopping | Rides | Travel | AI Tools | Card Perks
    "title": "Short name",
    "discount": "฿50 off",
    "minSpend": "฿300",
    "expiry": "2026-10-31",       // or null if no end date
    "codes": [{"code": "CARDXFOOD", "label": "short note"}],  // [] = "No code"
    "claim": "How to claim when codes is [] (optional)",
    "why": "Generic note, e.g. Needs a CardX card",
    "source": "https://..."
  }]
}
```
## Thai fields (optional, but fill them in every day)
The page has a TH | EN switch (also `?lang=th` / `?lang=en`). Each English text field has an optional Thai twin; the page falls back to English when missing:
`title_th`, `discount_th`, `minSpend_th`, `why_th`, `claim_th`, and per code `label_th`.
Codes, brand, category, expiry and source are never translated. **Daily updates should fill in all `_th` fields** using natural Thai (e.g. "ลด ฿50", "ขั้นต่ำ", "ใช้ได้ 2 ครั้ง/เดือน").

Keep it public-safe: deals only, no personal data.

## Daily update
```bash
cd /workspace/thai-coupon-dashboard
git pull
# edit deals.json
python3 -m json.tool deals.json > /dev/null && git add deals.json && git commit -m "Deals for $(date +%F)" && git push
```
GitHub Pages redeploys in ~1 minute.

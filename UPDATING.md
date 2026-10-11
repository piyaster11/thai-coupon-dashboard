# Updating the dashboard

All content lives in `deals.json`; `index.html` never needs editing.

## Schema
```json
{
  "date": "YYYY-MM-DD",          // list date, shown in header; "expires soon" = expiry within 3 days of this
  "deals": [{
    "rank": 1,                    // 1-10 = Top 10, 11+ = extra picks
    "brand": "Grab",              // see COLORS in index.html (unknown brands get grey chip)
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

## Copy counter (Cloudflare Worker)
- Each code box shows "Copied N times / คัดลอกแล้ว N ครั้ง" (hidden at 0 or if the API is down).
- Worker: https://coupon-copy-counter.piya-deals.workers.dev (source: /workspace/cf/copy-counter, deploy with `wrangler deploy` using Node 22 at ~/.local/node22).
- Endpoints: `GET /counts` (all counts), `POST /hit/:code` (+1; only from https://piyaster11.github.io; max 20 hits/min per IP).
- Storage: KV namespace `COUNTS` (id 20e09f64390c4e9b944450f759e4a63e), keys `c:<CODE>`. No personal data; IPs only as short-lived hashes (~2 min) for rate limiting.
- Free tier: 100k Worker requests/day, KV 100k reads and 1,000 writes/day (each counted copy = 2 writes, so ~500 counted copies/day). Over the limit, counting fails silently; the page still works.
- Set in index.html via the `COUNTER_URL` constant at the top of the script.
- Daily deals.json updates need NO counter changes: counts are keyed by code, and new codes start at 0 automatically.

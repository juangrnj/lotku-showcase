# Lotku — SaaS for Indonesian small businesses (showcase)

Lotku is a family of web apps for small businesses in Indonesia. I designed, built, deployed and run them
myself, from database to UI to a Linux VPS. The source code is private because these are commercial products.
This repo documents what they do and how they are built.

## Products

### 1. Lotku Bisnis — point of sale & operations · live at https://app.lotku.com
An offline-first cashier and back-office app for many kinds of business (retail, food & beverage, laundry,
workshop, clinic, rental).
- **POS**: cart, discounts and promos, split payment, digital receipt, and printing through the browser, Bluetooth or RawBT
- **Operations**: tables, kitchen display, shifts, staff PIN, attendance, purchasing & suppliers, stock
- **Industry flows**: job tickets for laundry/workshop, appointments, rental with late fees, a customer QR menu
- **Offline sync**: local database in the browser, sync queue and conflict handling, installable PWA
- **Billing**: subscription plans, trial periods and a payment-gateway integration
- **Security**: hashed passwords, rate limiting, bot protection, 2FA (TOTP) for the admin console

### 2. Lotku Commerce — order & warehouse system for multi-store online sellers
An internal OMS/WMS for running many marketplace stores (TikTok Shop, Tokopedia, Shopee, Lazada…) from one place.
- **Orders**: import from seller-center CSV/XLSX exports, manual orders, dispute evidence
- **Warehouse**: picking waves by rack, scan-to-pack with an automatic video per waybill, courier hand-over, returns,
  packer performance. Works offline on a phone.
- **Analytics**: rising/slowing products, dead stock, ABC analysis, frequently-bought-together, customer risk,
  courier & region performance, net profit, ad ROAS, restock suggestions
- **Security**:
  - New devices must be approved by the owner.
  - Staff phones get scoped access.
  - Staff accounts can be frozen or blocked.
  - Every action goes into an audit log.
  - The origin server only accepts traffic from Cloudflare.

### 3. Lotku Porto — portfolio-website builder (in development)
Users fill in a form, pick a profession-themed template with scroll animations and publish their page to their own URL.
It has email-OTP login, an in-browser photo editor (on-device background removal), and the page goes live only after payment.

## Architecture

```
Browser (PWA, vanilla JS, Service Worker, IndexedDB/localStorage)
   │  HTTPS (Cloudflare)
Nginx reverse proxy  ── rate limits, TLS (Let's Encrypt)
   │
Python stdlib HTTP server (no framework)  ── systemd service per app
   │
SQLite (one database per app) + daily backups
External services: transactional email (Brevo), payment gateway, Cloudflare Turnstile
```

- **No framework, few dependencies.** The servers use only the Python standard library, and the frontends are
  vanilla JavaScript. That keeps hosting cheap (one small VPS runs several apps) and the attack surface small.
- **Each app is isolated.** Every product has its own process, port, database and deploy script on the same VPS.
- **Tested.** Every product has an automated test suite for its server API (accounts, permissions, sync, billing edge cases).

## Scale of the work

| | Lines of code (server + client) | Status |
|---|---|---|
| Lotku Bisnis | ~11,000 | Live |
| Lotku Commerce | ~16,000 | Live (private use) |
| Lotku Porto | ~1,600 | In development |

## Stack
Python 3 (stdlib `http.server`, `sqlite3`) · JavaScript (ES2020, PWA) · HTML/CSS · Nginx · systemd · Ubuntu VPS ·
Cloudflare · Brevo · payment-gateway APIs

*Code samples can be shared on request.*

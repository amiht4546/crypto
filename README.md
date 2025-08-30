# Crypto SaaS (Production-ready skeleton) - Vercel
This repo is a **production-ready skeleton** for a crypto SaaS using Next.js + Prisma. It includes:
- User auth (register/login) with hashed passwords and JWT
- Admin role with user management (grant/revoke access)
- Billing flow skeleton using Coinbase Commerce (or another crypto payment processor) via webhooks
- Prisma ORM with SQLite for local development; easy to switch to Postgres/PlanetScale/Neon for production
- Deployment instructions for Vercel + environment variables

IMPORTANT: You MUST configure a real **persistent database** (PlanetScale, Neon, Supabase, AWS RDS, etc.) for production. Do **NOT** use SQLite on Vercel.

## Quick local setup (dev)
1. Install dependencies: `npm install`
2. Initialize Prisma & DB: `npx prisma migrate dev --name init`
3. Run dev server: `npm run dev`
4. Open http://localhost:3000

## Switch to production DB (Postgres / PlanetScale / Neon)
- Update `DATABASE_URL` in Vercel project settings to your provider's connection string.
- On local machine, set `DATABASE_URL` env var and run `npx prisma db push` to push schema (or run migrations).

## Payment provider integration
- This skeleton includes `/api/pay/checkout` and `/api/pay/webhook` endpoints for Coinbase Commerce style flow.
- Sign up for a crypto payment processor (Coinbase Commerce, NOWPayments, CoinPayments, etc.) and add your API key and webhook secret to Vercel env vars.
- Replace the simulated checkout logic with the provider's checkout creation API and verify webhooks on `/api/pay/webhook`.

## Environment variables (set in Vercel or .env locally)
- `JWT_SECRET` - strong secret for signing tokens
- `DATABASE_URL` - Prisma DB connection string
- `COINBASE_COMMERCE_API_KEY` - (optional) for creating charges
- `COINBASE_COMMERCE_WEBHOOK_SHARED_SECRET` - (optional) for verifying webhooks
- `ADMIN_EMAIL` - initial admin email to create manually or via migration script

## Production checklist (must do)
- Use a robust DB (PlanetScale, Neon, Supabase)
- Use real payment gateway & verify webhooks
- Add rate limiting, input validation, CSP, security headers
- Use HTTPS (Vercel provides TLS)
- Add logging and error monitoring (Sentry)
- Add backup and monitoring for DB


## New features added in updated build
- Settings page to save Binance API key & secret (used for live trading orders)
- Trading page with Binance websocket candlestick stream and Buy/Sell market order UI
- Trade API endpoint that signs orders with user's saved API secret and submits to Binance REST API
- Chat page that remains disabled until the user purchases the 30-day plan
- Auto-expiry: `api/auth/me` will automatically revoke paid access when `expiresAt` is in the past

## Vercel deployment notes (very important)
- **Do NOT** use SQLite on Vercel. Use PlanetScale, Neon, or another managed DB. Set `DATABASE_URL` env var.
- Set `JWT_SECRET` in Vercel.
- If you plan to place live orders on Binance, **DO NOT** store API secrets in plaintext in a DB without encryption. Consider using a secrets manager.
- Binance trading requires proper permissions and careful risk handling. Test on Binance Testnet first by setting `BINANCE_API_BASE` to https://testnet.binance.vision and using testnet API keys.
- Configure webhook URL and payment provider as described earlier.

# URL Shortener Template

A fast, multi-user URL shortener built with Cloudflare Workers and D1. Auto-deploys via GitHub Actions.

## Features

- ⚡ Fast redirects via Cloudflare edge network
- 👥 Multi-user (each user has private links)
- 🔐 Auth via Cloudflare Access (email, Google, GitHub)
- 📊 Click tracking
- 📤 Export/Import links
- 🚀 Auto-deploy on git push
- 💰 100% free (Cloudflare free tier)

## Quick Start

**[→ Full Setup Guide](SETUP.md)** (~15 minutes)

**TL;DR:**
1. Use this template to create your repo
2. Create a D1 database in Cloudflare
3. Update `wrangler.toml` with your database ID
4. Add `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` to GitHub secrets
5. Push to deploy
6. Set up Cloudflare Access for auth

## Using Claude AI

If you have Claude with Cloudflare MCP connected, just say:

```
Create a D1 database called "my-shortener" with a links table 
for a URL shortener (code, destination, clicks, user_email, created_at)
```

Claude will create the database. You still need to:
- Update `wrangler.toml` with the database ID
- Add GitHub secrets
- Deploy
- Set up Cloudflare Access

## API

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/admin` | Admin dashboard |
| GET | `/api/links` | List your links |
| POST | `/api/links` | Create link |
| DELETE | `/api/links/:code` | Delete link |
| GET | `/api/export` | Export links as JSON |
| POST | `/api/import` | Import links from JSON |
| GET | `/:code` | Redirect (public) |

## Files

| File | Purpose |
|------|---------|
| `worker-multiuser.js` | Main worker (multi-user with auth) |
| `worker.js` | Simple single-user version |
| `wrangler.toml` | Cloudflare config |
| `schema-multiuser.sql` | Database schema |
| `SETUP.md` | Detailed setup guide |
| `.github/workflows/deploy.yml` | Auto-deploy workflow |

## Costs

Cloudflare free tier covers everything:
- 100,000 requests/day
- 5GB database storage
- 50 authenticated users
- No egress fees
- No surprise bills

## License

MIT - Do whatever you want with it.

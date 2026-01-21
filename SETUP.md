# Setup Guide

Complete setup guide for deploying your own URL shortener using this template.

**Time required:** ~15 minutes  
**Cost:** Free (Cloudflare free tier)

---

## Prerequisites

- Cloudflare account (free)
- GitHub account
- A domain managed by Cloudflare (optional but recommended)

---

## Step 1: Use This Template

1. Click "Use this template" → "Create a new repository"
2. Name your repo (e.g., `my-shortener`)
3. Make it Private or Public
4. Click "Create repository"

---

## Step 2: Create Cloudflare D1 Database

### Option A: Using Claude AI

If you have Claude with the Cloudflare MCP integration:

```
Create a D1 database called "my-shortener" with a links table containing:
- id (primary key, autoincrement)
- code (text, unique)
- destination (text)
- clicks (integer, default 0)
- user_email (text)
- created_at (datetime)
```

Claude will create it and give you the database ID.

### Option B: Manual Setup

1. Go to **Cloudflare Dashboard** → **Workers & Pages** → **D1**
2. Click **Create database**
3. Name: `my-shortener` (or your project name)
4. Click **Create**
5. Go to your database → **Console** tab
6. Paste and run:

```sql
CREATE TABLE links (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  code TEXT UNIQUE NOT NULL,
  destination TEXT NOT NULL,
  clicks INTEGER DEFAULT 0,
  user_email TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_links_user_email ON links(user_email);
```

7. Copy the **Database ID** from the database overview page

---

## Step 3: Update wrangler.toml

Edit `wrangler.toml` in your repo:

```toml
name = "my-shortener"  # Your project name
main = "worker-multiuser.js"
compatibility_date = "2024-01-01"

[[d1_databases]]
binding = "DB"
database_name = "my-shortener"  # Must match your D1 database name
database_id = "abc123-your-database-id-here"  # Paste your Database ID
```

---

## Step 4: Create Cloudflare API Token

1. Go to: **Cloudflare Dashboard** → **My Profile** (top right) → **API Tokens**
2. Click **Create Token**
3. Use template: **"Edit Cloudflare Workers"**
4. Account Resources: Include → **Your Account**
5. Zone Resources: Include → **All zones**
6. Click **Continue to summary** → **Create Token**
7. **Copy the token** (you won't see it again!)

---

## Step 5: Get Account ID

1. Go to: **Cloudflare Dashboard** → **Workers & Pages**
2. Find **Account ID** in the right sidebar
3. Copy it

---

## Step 6: Add GitHub Secrets

1. Go to your GitHub repo → **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add these two secrets:

| Name | Value |
|------|-------|
| `CLOUDFLARE_API_TOKEN` | The token from Step 4 |
| `CLOUDFLARE_ACCOUNT_ID` | The ID from Step 5 |

---

## Step 7: Deploy

Option A: Push any change to trigger deploy
```bash
git commit --allow-empty -m "Trigger deploy"
git push
```

Option B: Manual trigger
1. Go to repo → **Actions** tab
2. Click **"Deploy to Cloudflare Workers"**
3. Click **"Run workflow"**

Check the Actions tab - it should complete in ~30 seconds.

---

## Step 8: Add Custom Domain (Optional)

1. Go to: **Cloudflare Dashboard** → **Workers & Pages** → your worker
2. Click **Settings** → **Domains & Routes**
3. Click **Add** → **Custom domain**
4. Enter your domain (e.g., `links.yourdomain.com`)
5. Cloudflare auto-creates the DNS record

---

## Step 9: Set Up Authentication

1. Go to: **https://one.dash.cloudflare.com** (Zero Trust dashboard)
2. If first time, create a team name and select Free plan
3. Go to: **Access** → **Applications** → **Add an application**
4. Select **Self-hosted**
5. Configure:
   - Application name: `URL Shortener`
   - Session duration: 24 hours
   - Click **Add public hostname**
   - Subdomain: `links` (or your subdomain)
   - Domain: select your domain
6. Click **Next**
7. Create policy:
   - Policy name: `Allowed Users`
   - Action: Allow
   - Include: Selector = `Everyone`
   - Require: Selector = `Login Methods`, Value = `One-time PIN`
8. Click **Next** → **Save**

---

## Done! 🎉

Visit your URL:
- **Short links:** `https://links.yourdomain.com/code`
- **Admin panel:** `https://links.yourdomain.com/admin`

You'll be prompted to log in via email (One-time PIN).

---

## Adding More Login Methods

To add Google login:

1. Go to: Zero Trust → **Settings** → **Authentication** → **Login methods**
2. Click **Add new** → **Google**
3. Follow the Google OAuth setup (requires Google Cloud Console)
4. Go back to your application policy and add Google as an option

---

## Troubleshooting

**Deploy failed:**
- Check that `database_id` in `wrangler.toml` is correct
- Verify GitHub secrets are set correctly

**"Unauthorized" at /admin:**
- Cloudflare Access isn't set up yet, or you're not logged in
- Check the Access application is configured for your domain

**Links not working:**
- Make sure the database has the `links` table
- Check the worker is deployed (Workers & Pages dashboard)

---

## Next Steps

- Customize the admin UI in `worker-multiuser.js`
- Add more users to the Access policy
- Set up Google/GitHub login for easier access

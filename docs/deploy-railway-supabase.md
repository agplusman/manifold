# Deploy Manifold on Railway + Supabase

This guide walks you through deploying your own Manifold instance using Railway (compute) and Supabase (database).

## Prerequisites
- Railway account (https://railway.app)
- Supabase account (https://supabase.com)
- GitHub account (for Railway integration)

## Architecture

```
┌─────────────────────────────────────────┐
│              Railway                     │
│  ┌─────────────┐    ┌────────────────┐  │
│  │  Frontend   │◄──►│  Backend API   │  │
│  │  (Next.js)  │    │  (Docker/PM2)  │  │
│  └─────────────┘    └────────────────┘  │
└─────────────────────────────────────────┘
              │                │
              ▼                ▼
┌─────────────────────────────────────────┐
│           Supabase                       │
│  ┌─────────────────────────────────┐    │
│  │  PostgreSQL Database            │    │
│  │  + Auth (optional)              │    │
│  │  + Storage (optional)           │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

## Step 1: Set Up Supabase

1. Create a new Supabase project at https://supabase.com/dashboard
2. Note down your credentials:
   - Project URL
   - Anon Key
   - Service Role Key
   - Database connection string

3. Run the database migrations:
   ```bash
   cd backend/supabase
   # Apply all SQL files in order
   # Or use Supabase CLI: supabase db push
   ```

## Step 2: Deploy to Railway

### Option A: One-Click Deploy (Recommended)
[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/manifold)

### Option B: Manual Setup

1. Fork this repository to your GitHub account

2. Create a new Railway project:
   ```bash
   railway login
   railway init
   ```

3. Add the Frontend service:
   ```bash
   cd web
   railway link
   railway up
   ```

4. Add the Backend API service:
   ```bash
   cd backend/api
   railway link
   railway up
   ```

5. Configure environment variables in Railway dashboard for both services

## Step 3: Configure Environment Variables

### Frontend (web) Service
| Variable | Description |
|----------|-------------|
| `NEXT_PUBLIC_SUPABASE_URL` | Your Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anon/public key |
| `NEXT_PUBLIC_API_URL` | URL of your backend service |
| `NEXT_PUBLIC_FIREBASE_*` | Firebase config (for auth) |

### Backend (api) Service
| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | Supabase PostgreSQL connection string |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key |
| `SUPABASE_JWT_SECRET` | JWT secret for token validation |

## Step 4: Set Up Custom Domain (Optional)

1. In Railway dashboard, go to your service settings
2. Add a custom domain
3. Update DNS records as instructed
4. Update `NEXT_PUBLIC_API_URL` to use your custom domain

## Estimated Costs

| Service | Free Tier | Paid Estimate |
|---------|-----------|---------------|
| Railway | $5 credit/month | ~$20-50/month |
| Supabase | 500MB DB, 1GB storage | ~$25/month (Pro) |

## Troubleshooting

### Backend not connecting to database
- Verify `DATABASE_URL` format: `postgresql://user:pass@host:5432/db`
- Check Supabase connection pooler settings
- Ensure Railway service can reach Supabase (no IP restrictions)

### Frontend can't reach backend
- Verify `NEXT_PUBLIC_API_URL` is set correctly
- Check CORS settings in backend
- Ensure both services are deployed and running

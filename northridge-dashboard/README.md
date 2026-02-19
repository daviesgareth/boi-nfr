# Northridge Finance — Buyback & Risk Analysis Dashboard

## Deploy to Railway

### Option 1: One-click (GitHub)
1. Push this folder to a GitHub repo
2. Go to [railway.app](https://railway.app)
3. Click **New Project → Deploy from GitHub repo**
4. Select your repo
5. Railway auto-detects the Dockerfile and deploys
6. Click **Generate Domain** in Settings to get your URL

### Option 2: Railway CLI
```bash
# Install Railway CLI
npm install -g @railway/cli

# Login
railway login

# Create project and deploy
railway init
railway up
```

### Option 3: Docker (run locally)
```bash
docker build -t northridge-dashboard .
docker run -p 8080:8080 northridge-dashboard
```

Then open http://localhost:8080

## Architecture

- **Single HTML file** (~10MB) with all data embedded
- **Express** serves static files with **gzip compression** (compresses to ~1.5MB over the wire)
- **/health** endpoint for Railway health checks
- No database, no API calls — everything runs client-side

## Security Note

This dashboard contains **redacted** financial data. Ensure your Railway deployment is:
- Not publicly indexed (Railway domains aren't by default)
- Consider adding basic auth if sharing externally (see below)

### Adding Basic Auth (optional)
Add to server.js before the static middleware:
```js
app.use((req, res, next) => {
  const auth = req.headers.authorization;
  if (!auth || auth !== 'Basic ' + Buffer.from('northridge:yourpassword').toString('base64')) {
    res.setHeader('WWW-Authenticate', 'Basic realm="Northridge Dashboard"');
    return res.status(401).send('Authentication required');
  }
  next();
});
```

## Tabs
- **Contracts** — Filterable table of all live contracts with risk scoring
- **Risk Analysis** — 6-factor risk scoring model with distributions
- **Stats & Arbitrage** — Trade price arbitrage by make, model, fuel type
- **Retention** — Customer retention KPIs with probabilistic matching
- **Forecasting** — 60-month portfolio projections + UK legislative timeline
- **Methodology** — Full documentation of all models and assumptions

# FieldStation v0.4 — Weather for better gardening

FieldStation is an installable weather and gardening dashboard for a selected location. The public site is published at [permanentlychill.github.io/FieldStation](https://permanentlychill.github.io/FieldStation/).

## Current features

- Current conditions, a seven-day outlook, and an hourly forecast for the current local day.
- Precipitation timeline combining observed radar from the past two hours with a coarse, 12-hour model precipitation outlook over a local grid. Forecast overlays are model estimates, not radar imagery or storm-track guarantees. Observed radar by [RainViewer](https://www.rainviewer.com/), forecast data by [Open-Meteo](https://open-meteo.com/), and map data by [OpenStreetMap](https://www.openstreetmap.org/copyright).
- Metric or imperial display units.
- Gardening indicators for modelled soil temperature and moisture, ET₀, vapor pressure deficit, dew point, rain chance, wind gusts, UV, and growing degree days.
- Rule-based field notes based on the forecast.
- Daily historical weather archive, seasonal filters, summaries, and GDD chart. ERA5 coverage begins in 1940 where available.
- Local history stored in IndexedDB, with optional two-way synchronization to the user's own Supabase project.
- Responsive FieldStation branding and installable PWA icons.

## Data notes

Forecasts, historical reanalysis, and physical station readings are separate concepts. Historical ERA5 values are gridded estimates, not measurements at the selected address; the archive typically has a delay of several days. Soil temperature and moisture are model estimates, not on-site sensor readings. FieldStation does not create physical station observations.

GDD uses daily maximum and minimum temperatures with a base of 10°C / 50°F and no upper cap. Crop-specific methods can require different bases or upper thresholds.

## Run locally

Serve this folder over localhost rather than opening index.html directly:

    python -m http.server 8000

Then visit http://localhost:8000.

## Enable cloud sync

1. Create a project at https://supabase.com.
2. In Supabase SQL Editor, run supabase-schema.sql.
3. In Project Settings → API, copy the project URL and publishable key (or legacy anon key).
4. In FieldStation, open Cloud & settings, enter the URL and key, then save.
5. Create an account or sign in. If email confirmation is enabled, confirm the email before signing in.
6. Choose Sync local history now. Sync also runs after a successful refresh when signed in.

Only use a Supabase publishable/anon key in the browser. Never enter a service-role or secret key. Project settings and login session are stored in the current browser; avoid shared devices for personal accounts. The supplied SQL uses row-level security to restrict each account to its own records.

## Deployment

The GitHub Actions workflow publishes the repository root to GitHub Pages whenever main is updated. The site is served over HTTPS. On Android, open the site in Chrome and choose Install app / Add to Home screen.

## Known limitations

- Archive values are modelled estimates, and recent historical data can be delayed.
- Variables can be unavailable for a model, date, or region; unavailable values are left blank rather than fabricated.
- Air-quality history is not included.
- Cloud sync requires the user's own Supabase project.
- History advances when the app is opened or refreshed; there is no background data collection.
- Check current provider terms for Open-Meteo, RainViewer, and map tiles before commercial use.

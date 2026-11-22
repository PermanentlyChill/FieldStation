# FieldStation v0.3 — Daily weather archive and growing conditions PWA

## What's in v0.3
- Keeps the existing current weather, forecast, growing-condition dashboard, local IndexedDB storage, and optional Supabase sync.
- Imports long-term **daily** historical weather for a searched location, beginning in 1940 where ERA5 data is available.
- Uses daily summaries only for the long-term archive: daily mean/high/low temperature, apparent temperature, precipitation, rain, snowfall, wind, humidity, dew point, pressure, cloud cover, solar radiation, ET₀, wet-bulb temperature, VPD, and daily soil temperature/moisture estimates where supplied by the selected dataset.
- Adds a year selector and historical summaries for high/low records, mean temperature, precipitation, snowfall, and growing degree days.
- Imports history in five-year batches, saves each batch locally, and skips complete date blocks on later runs. If an import is interrupted, run it again; already-complete blocks are skipped.
- Continues to save recent daily updates when the app refreshes.

## Run locally
Serve this folder over localhost (not by opening `index.html` directly):

```bash
python -m http.server 8000
```

Then visit http://localhost:8000.

## Historical archive behavior
1. Search for a city/town/ZIP or use the location button.
2. Press **Import full daily history (1940–present)** to backfill the selected location.
3. FieldStation requests daily ERA5 reanalysis in five-year blocks and saves each completed block to IndexedDB.
4. Use the year selector to inspect an individual year or all years. The table shows daily high/low/mean temperatures, precipitation, snowfall, and estimated GDD.
5. Press **Update recent days** to refresh the latest available historical records without running the full import again.

The archive intentionally does not store decades of hourly observations. ERA5 historical data is updated with a delay (commonly around five days), so the newest few days may not yet be present. Historical values are gridded reanalysis estimates, not observations from a station at the selected address. Coverage and available variables depend on the selected model and API response.

## Enable cloud sync
1. Create a project at https://supabase.com.
2. In Supabase, open **SQL Editor** and run `supabase-schema.sql`.
3. Open **Project Settings → API** and copy the Project URL and the publishable key (or legacy anon key).
4. In FieldStation, select **Cloud & settings**, paste the URL and key, and save.
5. Create an account or sign in. If email confirmation is enabled, confirm the email before signing in.
6. Select **Sync local history now**. Sync also runs after a successful refresh when signed in.

### Security
- Use only the Supabase publishable/anon key in the browser.
- Never use the `service_role` or secret key in FieldStation.
- The supplied SQL enables row-level security and restricts each account to its own records.
- The project URL/key and login session are stored in this browser's local storage. Do not use a shared/public device for a personal account.
- If you host the app publicly, use HTTPS and review Supabase Auth settings, password policy and email confirmation settings.

## Data definitions
- `reanalysis_estimate`: past daily values retrieved from Open-Meteo's Historical Weather API. These are gridded reanalysis estimates, not observations from a personal station.
- `forecast_snapshot`: forecast values captured at refresh time. They remain forecast data and may be replaced by a later forecast for the same target date.
- `station_observation`: reserved for future physical sensor readings; v0.3 does not create these records.

GDD is calculated from daily maximum and minimum temperature using base 10°C / 50°F, without an upper cap. This is a simple growing-degree-day convention; crops can require different bases or upper thresholds.

## Deployment
Upload the contents of this folder to a static HTTPS host such as GitHub Pages, Netlify or Cloudflare Pages. Open the deployed URL in Chrome on Android and choose **Install app** / **Add to Home screen**.

## Known limitations
- The historical archive uses modelled gridded estimates. It is not a record of exact conditions at a specific property or weather station.
- Some variables may not be available for every model, date, or region; unavailable values are stored as null rather than fabricated.
- Air-quality history is not included in this archive import and needs a separate data source.
- Cloud sync is optional and requires the user's own Supabase project. Large archives take longer to sync than the earlier 30-day dataset.
- No background scheduled collection; recent history advances when the app is opened/refreshed.
- Soil temperature and moisture are model estimates, not on-site sensor readings.
- Open-Meteo API availability and terms apply. Review current provider terms before commercial/public use.

# Talos Layers

Branch-based configuration repository for **GVA BMS** (Battlespace Management System).

Each branch provides a ready-to-use set of JSON configuration files for a specific demo location.
Switch branches in GVA BMS via **Settings → GitHub → Sync Layers** to change location.

---

## Available Demo Locations

| Branch | Location | Centre |
|--------|----------|--------|
| `london` | Farnborough, UK | 51.2777° N, 0.7761° W |
| `paris` | CDG Airport, France | 49.0097° N, 2.5479° E |
| `belfast` | Belfast Harbour, Northern Ireland | 54.5973° N, 5.9301° W |
| `brisbane` | Fortitude Valley, QLD, AU | 27.4568° S, 153.0357° E |
| `perth` | Perth CBD, WA, AU | 31.9505° S, 115.8605° E |

---

## Files per Branch

| File | Purpose |
|------|---------|
| `bms_settings.json` | Main settings — map centre, connector config, pointer to layers file |
| `bms-data-layers.json` | Data layer definitions — airports, airspaces, maritime zones, etc. |
| `demo_symbols.json` | Sample pre-placed military symbols around the demo area |
| `ais_config.json` | AIS vessel tracking connector config (AISStream.io WebSocket) |
| `adsb_config.json` | ADS-B aircraft tracking connector config (OpenSky Network REST) |
| `gtfs_sources_*.json` | GTFS real-time feed sources for the branch location |

---

## API Keys — Environment Variables

API keys and credentials are **not stored in these JSON files**. Instead the JSON
files reference environment variables using `${VAR_NAME}` syntax. GVA BMS expands
these at startup from the process environment.

Set the variables below before launching BMS. For VS Code development, add them
to `terminal.integrated.env.linux` in `.vscode/settings.json`:

```json
"terminal.integrated.env.linux": {
    "AISTREAM_TOKEN":      "<your AISStream.io key>",
    "BODS_API_KEY":        "<your UK BODS key>",
    "NTA_API_KEY":         "<your NTA/TfI key>",
    "OPENSKY_CLIENT_ID":   "<your OpenSky username>",
    "OPENSKY_CLIENT_SECRET": "<your OpenSky password>"
}
```

For production / systemd deployments, set them in the service `Environment=`
block or an `EnvironmentFile=`.

### Variable Reference

| Variable | Used in | Service | Cost | Registration |
|----------|---------|---------|------|--------------|
| `AISTREAM_TOKEN` | `ais_config.json` → `apiKey` | AISStream.io WebSocket vessel positions | Free tier available | [aisstream.io/authenticate](https://aisstream.io/authenticate) |
| `BODS_API_KEY` | `gtfs_sources_*.json` → `token` (Translink feeds) | UK Bus Open Data Service — Translink Ulsterbus, Metro Belfast | Free | [data.bus-data.dft.gov.uk](https://data.bus-data.dft.gov.uk/account/signup/) |
| `NTA_API_KEY` | `gtfs_sources_*.json` → `token` (TfI feeds) | Transport for Ireland GTFS-RT — buses, DART, Enterprise rail | Free | [developer.nationaltransport.ie](https://developer.nationaltransport.ie/signup) |
| `OPENSKY_CLIENT_ID` | `adsb_config.json` → `clientId` | OpenSky Network ADS-B — higher rate limits | Free (anonymous also works) | [opensky-network.org/index.php?option=com_users&view=registration](https://opensky-network.org/index.php?option=com_users&view=registration) |
| `OPENSKY_CLIENT_SECRET` | `adsb_config.json` → `clientSecret` | OpenSky Network — paired with `OPENSKY_CLIENT_ID` | Free | Same as above |

> **ADS-B without credentials**: OpenSky Network works anonymously at lower rate
> limits (10 requests / 10 s). Leave `OPENSKY_CLIENT_ID` and
> `OPENSKY_CLIENT_SECRET` empty if you don't have credentials.

---

## Belfast Branch — Live Feed Summary

| Connector | Feed | Radius | Env var |
|-----------|------|--------|---------|
| AIS | AISStream.io WebSocket | 200 nm (Irish Sea, North Channel) | `AISTREAM_TOKEN` |
| ADS-B | OpenSky Network REST | 370 km / 200 nm (NI, RoI, Scotland) | `OPENSKY_CLIENT_ID` / `OPENSKY_CLIENT_SECRET` (optional) |
| GTFS | Translink Ulsterbus | Northern Ireland rural & intercity | `BODS_API_KEY` |
| GTFS | Translink Metro Belfast | Belfast city buses | `BODS_API_KEY` |
| GTFS | Transport for Ireland vehicles | RoI + cross-border services | `NTA_API_KEY` |
| GTFS | Transport for Ireland rail | DART, commuter, Enterprise Belfast–Dublin | `NTA_API_KEY` |

---

## Usage

1. Open GVA BMS in standalone mode.
2. Click the **Settings** toolbar button.
3. Select the **GitHub** tab.
4. The repository URL defaults to `https://github.com/Astute-Systems/talos-layers`.
5. Choose a **Demo Location** from the drop-down.
6. Click **Sync Layers** — BMS clones the branch and writes the JSON files locally.
7. Ensure the required environment variables are set (see above).
8. Re-launch BMS — connectors will use the synced settings automatically.

---

## Adding a New Location

1. Create a new branch from `main`.
2. Edit `bms_settings.json` with the correct default lat/lon and zone name.
3. Edit `bms-data-layers.json` to reference the correct regional data files.
4. Add `ais_config.json` and `adsb_config.json` with centre coordinates and radius.
5. Add a `gtfs_sources_<location>.json` with the relevant regional GTFS-RT feeds.
   Use `${VAR_NAME}` for any API tokens — do **not** commit raw keys.
6. Optionally update `demo_symbols.json` with representative symbols.
7. Push the branch — it will appear in the BMS branch selector after the next pull.

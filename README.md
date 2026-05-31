# Talos Layers

Example layer configuration branches for **GVA BMS** (Battlespace Management System).

Each branch provides a ready-to-use set of JSON configuration files for a specific demo location.
Use the **GitHub → Sync Layers** panel inside GVA BMS to download and apply a branch.

## Available Demo Locations

| Branch | Location | Centre |
|--------|----------|--------|
| `london` | Farnborough, UK | 51.2777° N, 0.7761° W |
| `paris` | CDG Airport, France | 49.0097° N, 2.5479° E |
| `brisbane` | Fortitude Valley, QLD, AU | 27.4568° S, 153.0357° E |
| `perth` | Perth CBD, WA, AU | 31.9505° S, 115.8605° E |

## Files per Branch

| File | Purpose |
|------|---------|
| `bms_settings.json` | Main settings — map centre coordinates, connector config, and pointer to the layers file |
| `bms-data-layers.json` | Data layer definitions — airports, airspaces, maritime zones, etc. |
| `demo_symbols.json` | Sample pre-placed military symbols around the demo area |

## Usage

1. Open GVA BMS in standalone mode.
2. Click the **Settings** toolbar button.
3. Select the **GitHub** tab.
4. The repository URL defaults to `https://github.com/Astute-Systems/talos-layers`.
5. Choose a **Demo Location** from the drop-down.
6. Click **Sync Layers** — BMS clones the branch and writes the JSON files locally.
7. Re-launch BMS with the synced settings file:
   ```
   gva-app-bms --settings ~/.local/share/gva-app-bms/talos-layers/bms_settings.json
   ```

## Adding a New Location

1. Create a new branch from `main`.
2. Edit `bms_settings.json` with the correct default lat/lon and zone name.
3. Edit `bms-data-layers.json` to reference the correct regional data files.
4. Optionally update `demo_symbols.json` with representative symbols.
5. Push the branch — it will appear in the BMS branch selector after the next pull.

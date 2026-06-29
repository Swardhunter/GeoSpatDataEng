# GeoSpatDataEng — Finland Data Center Site Suitability

A reproducible geospatial pipeline for data-center site selection in Finland. It ingests 9 spatial layers, harmonizes CRS, clips to an area of interest, applies a two-stage suitability model (hard exclusions + weighted scoring), and produces a ranked shortlist plus an interactive map.

**Region-agnostic:** Oulu is the example AOI, but it works for any major Finnish city (Helsinki, Tampere, Turku, Oulu) at a 50 or 100 km buffer — set it in `project/config/aoi.json` or pick interactively with `city_picker.py`.

## Repository layout

```
.
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
└── project/
    ├── config/        # aoi.json, keys.json, config.py
    ├── templates/     # city_picker.html
    ├── scripts/       # fetching, etl, scoring, orchestrators
    ├── data/          # raw (ignored) + etl vector layers
    ├── outputs/       # candidates.geojson, scores, final_map.html
    └── requirements.txt
```

See [project/README.md](project/README.md) for the full pipeline, scoring model, and data sources.

## Quick start (Docker)

From the repo root:

```bash
docker compose -f docker/docker-compose.yml build
MML_KEY=your-key docker compose -f docker/docker-compose.yml run --rm pipeline python scripts/fetch_all.py
docker compose -f docker/docker-compose.yml up
```

`project/data`, `project/outputs`, and `project/config` are volume-mounted, so results persist on the host.

## Quick start (manual)

```bash
cd project
pip install -r requirements.txt
playwright install chromium       # for fetch_datacentermap.py
python scripts/city_picker.py     # pick a city → auto-fetch all layers
python scripts/run_etl.py         # clip, slope, exclusions, QC, scoring
python scripts/visualize_map.py   # interactive map
```

## API key

Get a free MML key at [omatili.maanmittauslaitos.fi](https://omatili.maanmittauslaitos.fi), then either set `MML_KEY` in the environment or put it in `project/config/keys.json`. Parcels and DEM are skipped if no key is set.

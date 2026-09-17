# European offshore wind: capacity, pipeline and coastal distance

Data preparation for an interactive dashboard of offshore wind farms in European
seas, built from the EMODnet Human Activities wind farm database.

The script reads the point layer, harmonises the status field across releases,
derives capacity and distance columns, and writes a flat CSV that any
visualisation tool can consume. The dashboard itself is published separately.

## Data

EMODnet Human Activities, Energy, Wind Farms. Created in 2014 by CETMAR for the
European Marine Observation and Data Network and updated annually. Covers 18
countries with attributes for name, number of turbines, status, country, year,
power (MW), distance to coast and area.

Licence: CC-BY 4.0. Originator: CETMAR. Source:
https://emodnet.ec.europa.eu/en/human-activities

## Usage

    pip install -r requirements.txt

    # from the CSV export (needs pandas only)
    python prepare_windfarms.py --input data/raw/windfarms.csv --summary

    # from a shapefile or GeoPackage
    python prepare_windfarms.py --input data/raw/windfarms_points.shp

    # or straight from the EMODnet WFS
    python prepare_windfarms.py --wfs --summary

Output defaults to `data/processed/wind_farms_clean.csv`.

## Important matters to note

- Records with no recorded capacity are dropped, since a project of unknown size
  cannot contribute to any capacity view. The count is logged.
- Status labels vary between annual releases, so they are mapped onto a fixed
  ordered set and anything unrecognised is logged as a warning rather than
  silently grouped.
- Shapefile field names are truncated to ten characters, so known truncations
  are mapped back to their full names on load.
- Coordinates are resolved from separate columns or from a WKT geometry column,
  whichever the export provides, so the CSV and spatial routes give the same
  output. geopandas is imported only when a spatial source is used.
- Start year is missing for many pipeline projects. It is kept as a nullable
  integer rather than imputed, and the count of missing years is reported in the
  summary so that the time series view can state its own coverage.

## Dashboard

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
https://public.tableau.com/app/profile/alejandra.g.cabanillas/viz/Europesoffshorewindpipeline/Dashboard1?publish=yes


## Exploratory checks

- GDP per head was checked as a plausibility test on the joined table. All
  values fall in a defensible European range, so no region is the product of a
  bad match.
- Irish regions show GDP per head near 168,000 EUR, a known artefact of
  multinational profit accounting rather than local economic activity. Their
  exposure ratios are correspondingly understated.
- NUTS3 units are not comparable in kind. Some are large rural provinces,
  others are single city districts such as Emden, which concentrates economic
  output in a small boundary. Ratios across region types should be read with
  that in mind.
- Population was added as point size in the scatter to confirm that capacity
  and GDP both rise with regional size. They do, so the regions of interest
  are the small points sitting high on the capacity axis.

  

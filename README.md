# Clear-sky-index observations from a dense pyranometer network

This repository provides processed clear-sky-index observations from
a dense network of nine pyranometers deployed on the Inner Mongolia
Plateau, China. The dataset supports studies of cloud-dependent
irradiance variability, spatial correlation, and the representativeness
of individual radiation measurements.

## Files

| File | Description |
|---|---|
| `clear_sky_index.csv.gz` | Compressed CSV file containing one-minute clear-sky-index observations from nine stations |
| `selected_days.csv` | Dates selected for the three sky-condition categories |
| `sites.csv` | Station identifiers and geographical coordinates |

## Clear-sky-index observations

The clear-sky index is the ratio of measured global horizontal
irradiance (GHI) to the corresponding modeled clear-sky GHI.
It is dimensionless.

`clear_sky_index.csv.gz` contains 91 days of processed observations,
from 10 July to 8 October 2016. Each day contains 540 samples at
one-minute resolution for nine stations.

Each row contains the observation date, a within-day sample index,
and clear-sky-index values for the nine stations. Missing values are
represented by `NaN`.

Sample indices preserve the ordering of the archived processed data.
Exact clock-time alignment should be verified against the original
processing records before matching individual samples to external
observations.

The file can be decompressed with a standard gzip-compatible utility
and opened as a CSV file. Software supporting gzip-compressed CSV
files can read it directly.

## Station information

`sites.csv` contains the following fields:

- `site_id`: station identifier, numbered from 1 to 9.
- `latitude_degrees_north`: latitude in decimal degrees north.
- `longitude_degrees_east`: longitude in decimal degrees east.

Station 1 corresponds to the central station C1. The station order
matches that of the clear-sky-index data.

## Selected days and sky conditions

`selected_days.csv` identifies the days selected for the
cloud-regime analysis:

| Code | Sky condition | Number of selected days |
|---|---|---:|
| `cl` | Clear sky | 12 |
| `ov` | Overcast | 8 |
| `bc` | Broken cloud | 18 |

The file contains:

- `sky`: sky-condition code.
- `matlab_day_index_1based`: one-based day index in the original
  91-day processed dataset.
- `date`: observation date in `YYYY-MM-DD` format.

The selected days are a subset of the full dataset. Days not listed
in this file are not assigned a sky-condition category by this release.

## Scope and use

This repository contains processed clear-sky-index observations,
station coordinates, and selected-day classifications. It does not
include raw GHI observations or analysis and plotting scripts.

Clear-sky-index values should not be interpreted as irradiance in
W m−2. Multiplying them by a fixed reference irradiance does not
recover the original GHI observations.

The nine stations provide discrete spatial samples. Their arithmetic
mean should not be assumed to equal the continuous area-mean irradiance.

For details of the instruments, clear-sky calculations, and analysis
methods, please refer to the associated manuscript and its
supplementary information.

# Fig2-Fig6: Python reproduction package

This package translates the figure sections of `original/paper_fig_v2.m`.
It runs without MATLAB, starting from the archived processed clear-sky-index
observations. Original files were copied without modification and have SHA-256
checksums in `original/manifest.json`.

## Run

Python 3.11 or newer is recommended. In a fresh virtual environment:

```sh
python -m pip install -r requirements.txt
python run_all.py --mode clean --layout single --fig6-networks nine
python run_all.py --mode clean --layout double --fig6-networks nine
python validate.py
```

Paths are resolved relative to the scripts, not the working directory. Each run
contains Fig2-Fig6 as 600 dpi PNG, vector PDF and editable SVG, plus CSV source
tables and a JSON run report. The default `single` preset is 8.5 cm wide:
Fig2 is 8.5 x 10.5 cm and Fig3-Fig6 are 8.5 x 6.4 cm. The `double` preset is
17.5 cm wide: Fig2 is 17.5 x 14.0 cm and Fig3-Fig6 are 17.5 x 10.5 cm. All
figure text is 7 pt at final size. Override with `--width-cm`, `--font-pt`,
`--dpi` and `--output PATH` if a target journal specifies different artwork.
The old MATLAB export mixed screen pixels and print DPI, so its nominal
6 x 4.5 cm was not reliable. Layout is recreated for publication legibility,
not pixel-identical rendering.

The submission outputs generated for this archive are in
`outputs/submission_single/` and `outputs/submission_double/`. Fig6 contains
only the corrected nine-site area-mean versus center-site result. Pass
`--fig6-networks both` only for historical comparison with the six-site subset.

`prepare_data.py --source PATH` rebuilds the portable dataset from the original
MATLAB folder. It is unnecessary for normal use: converted inputs are included.
Its MCOS parser is specific to the archived fit file; it asserts the model
formula, parameter names and saved fit SSE before exporting the coefficients.

## Two explicitly different results

- **legacy** reproduces the execution order and array reuse in the original
  MATLAB script, including known residual-array errors. Use for comparison
  with the archived figures, not as an automatically endorsed scientific result.
- **clean** resets arrays independently for each sky type and network size.
  It fixes those implementation errors only. Fig6 still uses the archived
  power-law coefficients, omitted diagonal and original distance convention,
  so this is a controlled comparison, not a replacement physical model.

Fig2, Fig3 and Fig5 are identical between modes. Fig4 and Fig6 can change
substantially. Review `AUDIT_zh.md` before choosing figures for submission.

## Figure / code / data map

| Figure | Computation | Required inputs |
|---|---|---|
| Fig2 | Site 1 examples, broken clouds / overcast / clear sky | kt, sky day indices, dates |
| Fig3 | Same broken-cloud day, smooth spans 1, 11, 61, 241 | kt, broken-cloud indices |
| Fig4 | Mean and between-day SD of daily temporal sample SD | kt, three day groups |
| Fig5 | 36 station pairs x 9 spans, nonnegative daily Pearson correlations; nonlinear power fit | kt, site coordinates, day groups |
| Fig6 | Analytical nine-site area-mean minus center-site SD | site coordinates, stored power fits, site-1 daily SD |

Numerical routines: `analysis.py`. Figure styling and CSV exports: `run_all.py`.
Data conversion and provenance: `prepare_data.py`. Verification: `validate.py`.

## Data dictionary

`data/analysis_inputs.npz` contains only numeric/string arrays (no pickle):

- `kt`: float64, shape (540 minutes, 9 stations, 91 days), dimensionless
  clear-sky index. Missing station-days are all NaN; no partial missing series.
- `site`: shape (2, 9), row 0 latitude in degrees north, row 1 longitude in
  degrees east. Site 1 is the center station used by the source script.
- `dates`: 91 ISO date strings converted from the archived MATLAB datenums.
- `cl`, `ov`, `bc`: zero-based day indices for clear, overcast and broken clouds.
  The selection file overrides same-named minute-level masks in the larger MAT.

`clear_sky_index.csv.gz` is a portable text copy: one row per date and sample,
with nine station values. NaN means missing. Sample 1-540 follows the existing
figure's nominal local-time axis, 08:00-16:59; exact timestamps are NOT asserted:
the two archived preprocessing scripts slice 182:721 and 181:720 differently.
`sites.csv` gives coordinates. `selected_days.csv` uses MATLAB one-based indices
and ISO dates. `legacy_fits.json` contains full-precision archived a,b in rho=a*x^b.
CSV outputs preserve actual odd smoothing spans separately from nominal labels.

## Numerical conventions retained

- `smooth(y, span)`: centered odd moving mean; endpoints shrink symmetrically
  (1,3,5,... samples). No zero padding and no interpolation of missing days.
- Temporal and between-day standard deviations use N-1, matching nanstd(x,0,...).
  Fig4 bars show SD across daily SD values, not standard errors or confidence intervals.
- Nominal spans 1,5,10,20,30,60,120,180,240 minutes correspond to actual spans
  1,5,11,21,31,61,121,181,241 samples. Fig5's denominator uses the actual span.
- Fig5 excludes negative daily correlations before averaging each station pair.
  The fit minimizes squared residuals in rho space, not logarithmic rho space.
- Fig5 distance is (100.2*Euclidean_latlon_degrees - 0.1627)*1000 metres.
  Fig6 omits the -0.1627 km offset, as in the source. Neither is a geodesic formula.
- The right axes multiply delta Kt by a fixed 300 W/m2, preserving the source
  convention; they are not independently calculated irradiance observations.
- Fig6 power fits are unbounded; self-correlations are NaN. These scientific
  assumptions remain explicit and require review before interpreting results.

MATLAB reference documentation:
[smooth](https://www.mathworks.com/help/curvefit/smooth.html),
[power models](https://www.mathworks.com/help/curvefit/power.html),
[least squares](https://www.mathworks.com/help/curvefit/least-squares-fitting.html).

## Scope and submission status

This is a reproducible package from **processed observations to figures**.
Raw daily observations exist in the parent project's `paper code/data correction1`,
but the required `cal_L_scale` implementation is absent. No substitute clear-sky
model was invented. Rebuilding kt from raw irradiance and regenerating the
selected sky-day list from documented classification rules are not established.
The archive therefore includes processed source MAT files, portable exports,
and the available original processing scripts, but does not claim a verified
raw-observation-to-figure pipeline. Dates, timezone and site labels should be
checked against the manuscript and station metadata before release.

No journal-specific formatting or public license has been assumed. Fill in
paper title, authors, repository DOI and data/code license when preparing the
final public archive. Known legacy errors must not be concealed in submission.

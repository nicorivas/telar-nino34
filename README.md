# telar-nino34

The sea surface temperature of the Niño 3.4 region (5°N–5°S, 170°W–120°W), day by day, and a map of the
Pacific, as a tab of the [telar](https://github.com/nicorivas/telar) dashboard.

- **The chart** follows [Climate Reanalyzer](https://www.climatereanalyzer.org/clim/sst_daily/?dm_id=nino3.4):
  every year since 1982 faint, the 1991–2020 mean dotted with its ±2σ band, last year and this year
  highlighted, and the preliminary days. It is drawn in braille (2×4 dots per character), at the width of
  the panel.
- **The map** is NOAA's daily OISST v2.1 file (NCEI), averaged to 1°, from 45°S to 45°N and from 100°E to
  70°W: temperature or anomaly in colour, one half block (▀) per pixel, with the Niño 3.4 box drawn on it.

Hovering says the date or the place, and the values.

## Install

You need telar with modules installed from a repository (its `main` after 0.2.0, or the next release),
Python 3.11+, and [uv](https://docs.astral.sh/uv/) for the map (it installs netCDF4 by itself).

```sh
telar modulos instalar https://github.com/nicorivas/telar-nino34
```

Install it on every machine that uses it: the one that shows the tab and the one that runs the scheduled
jobs. On the latter, `telar periodicos aplicar` puts the download in its crontab. Then add `bloque:nino34`
to `[dashboard] orden` if you want the tab in a given place.

## How it works

| piece | what it does | where it runs |
| --- | --- | --- |
| `bin/nino34 bajar` | downloads the series (Climate Reanalyzer's JSON) and the map, and leaves `~/.local/state/nino34/YYYY-MM-DD.json` (keeps 30 days) | the scheduled job `nino34-bajar` (7:30 and 15:30), where telar's scheduled jobs live |
| `bin/pacifico-sst` | reads NCEI's daily netCDF and reduces it to a 1° grid | called by `bajar` |
| `bin/nino34 pagina` | reads the latest day (`telar resultado nino34`, over the bus if it lives on another machine) and builds the page: a canvas with the HTML of the chart and the map | the machine that shows the tab |

If the map fails, the chart still comes out, and the error is in `telar periodicos log nino34-bajar`.

## Data

The series is from [Climate Reanalyzer](https://climatereanalyzer.org) (Climate Change Institute, University
of Maine), built on NOAA OISST v2.1. The map is NOAA OISST v2.1 from
[NCEI](https://www.ncei.noaa.gov/products/optimum-interpolation-sst). Both are public; this module only
downloads and draws them.

## License

MIT.

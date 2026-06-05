# Japan Meteorological Agency Weather Forecast Archive

Japanese version: [README.md](README.md)

This repository archives weather forecasts issued by the Japan Meteorological Agency (JMA). Past observed weather can be obtained retrospectively, but past weather **forecasts** are not publicly provided as a historical dataset, so I have been collecting daily forecast data regularly. This dataset lets you inspect the history of past forecasts.

The repository currently provides three types of data.

- Detailed forecasts (`forecast`)
- Overview forecasts (`overview_forecast`)
- Warnings and advisories (`warning`)

I previously collected `overview_week`, but it is not published here because that source appears to have stopped being updated and published.

## Detailed Forecasts (`forecast`)

Detailed forecast JSON files are grouped by month and converted to CSV. Each ZIP contains CSV files for all regions in the month. This is a history of forecasts as they were issued in the past, not later-confirmed observed weather.

- Location: `forecast/forecast_YYYYMM.zip`
- File name inside each ZIP: `forecast_YYYYMM_{region_code}.csv`
- Source: `https://www.jma.go.jp/bosai/forecast/data/forecast/{region_code}.json`
- Period: from `2021-11-04`
- Collection interval: around 3 times per day
- Update timing: one month of data is zipped and normally uploaded at the beginning of the following month.
- Notes: There are no known missing periods, but unknown collection gaps may exist. If correction reports are issued in addition to regular reports, a day may have 4 or more records.

CSV columns for ordinary region codes:

- `publishingOffice`: issuing office name
- `reportDatetime`: report issue time
- `timeDefine`: forecast target datetime
- `areaName`: target area name
- `areaCode`: target area code
- `weatherCodes`: weather code. See `weatherCode.csv` / `weatherCodes.json`.
- `weathers`: forecast weather text
- `winds`: forecast wind text
- `waves`: forecast wave text
- `pops`: probability of precipitation
- `temps`: temperature
- `tempsMin`: minimum temperature
- `tempsMax`: maximum temperature
- `tempsMinUpper`: upper bound of the minimum temperature forecast range
- `tempsMaxUpper`: upper bound of the maximum temperature forecast range
- `tempsMinLower`: lower bound of the minimum temperature forecast range
- `tempsMaxLower`: lower bound of the maximum temperature forecast range
- `reliabilities`: reliability class

The national forecast file, `forecast_YYYYMM_010000.csv`, additionally has `regionName` and `regionCode`, and its forecast target datetime column is named `time`. Rows are emitted from the source JSON time series, so some fields are blank depending on the target time and area.

`reportDatetime` is the time when the forecast was issued. `timeDefine` or `time` is the datetime targeted by the forecast. For example, a forecast issued at 05:00 on May 1 may contain a row targeting 00:00 on May 2.

Region codes:

```text
010000
011000 012000 013000 014100 015000 016000 017000 020000
030000 040000 050000 060000 070000 080000 090000 100000
110000 120000 130000 140000 150000 160000 170000 180000
190000 200000 210000 220000 230000 240000 250000 260000
270000 280000 290000 300000 310000 320000 330000 340000
350000 360000 370000 380000 390000 400000 410000 420000
430000 440000 450000 460100 471000 472000 473000 474000
```

`010000` is the national forecast.

## Overview Forecasts (`overview_forecast`)

Overview forecast JSON files are grouped by month and converted to CSV. Each ZIP contains CSV files for all regions in the month. This is a history of forecast text as it was issued in the past, not later-confirmed observed weather.

- Location: `overview_forecast/overview_forecast_YYYYMM.zip`
- File name inside each ZIP: `overview_forecast_YYYYMM_{region_code}.csv`
- Source: `https://www.jma.go.jp/bosai/forecast/data/overview_forecast/{region_code}.json`
- Period: from `2021-12-15`
- Collection interval: around 3 times per day
- Update timing: one month of data is zipped and normally uploaded at the beginning of the following month.
- Notes: There are no known missing periods, but unknown collection gaps may exist.
- Region codes: same as the detailed forecasts, except `010000` is not included.

CSV columns:

- `publishingOffice`: issuing office name
- `reportDatetime`: report issue time
- `targetArea`: target area
- `headlineText`: headline text
- `text`: overview forecast body. Line breaks in the source JSON are replaced with spaces.

`reportDatetime` is the time when the overview forecast was issued. The target dates and times of the overview forecast are not stored in separate datetime columns; they are included as prose in `headlineText` or `text`.

## Weather Code Tables

The following two files provide lookup tables for `weatherCodes` in `forecast`.

- `weatherCode.csv`: weather code table in CSV format
- `weatherCodes.json`: weather code table in JSON format

Columns in `weatherCode.csv`:

- `weatherCode`: weather code
- `weatherPictDay`: daytime weather icon file name
- `weatherPictNight`: nighttime weather icon file name
- `Group`: weather category group
- `weatherJ`: Japanese weather name
- `weatherE`: English weather name

`weatherCodes.json` is a JSON object keyed by `weatherCode`. Each value is an array in the order `weatherPictDay`, `weatherPictNight`, `Group`, `weatherJ`, and `weatherE`.

## Warnings and Advisories (`warning`)

This data is the issuance history of JMA warnings and advisories. It is not a record of observations or actual weather events.

- Location: `warning/warning_YYYYMM.zip`
- Source: JMA warning/advisory JSON. The current URL format is `https://www.jma.go.jp/bosai/warning/data/r8/{region_code}.json`.
- Published file months: `202502` through `202605`
- Data period: from `2025-02-07T15:50:00+09:00` to `2026-05-28T11:31:00+09:00`, based on `reportDatetime`
- Missing or unpublished periods: records before `2025-02-07T15:50:00+09:00`, records after `2026-05-28T11:31:00+09:00` through `2026-05-31`, and public ZIP files for `2026-06` and later are not included.
- Region codes: same as the detailed forecasts, except `010000` is not included.

Datetime columns are ISO 8601 strings, for example `2025-02-08T04:05:00+09:00`. Spreadsheet software may automatically convert them to serial values when opening the CSV files.

### `warning_headline_YYYYMM_{region_code}.csv`

This file contains warning/advisory headline text organized as an issuance history, mainly at the prefectural level. Because the headline text is generally shared with the finer-area data, this file is built from the data corresponding to `areaType=0`.

Columns:

- `officeCode`: issuing office or region code
- `publishingOffice`: issuing office name
- `reportDatetime`: report time
- `controlDatetime`: control time. This may be blank for data derived from the older source format.
- `infoType`: information type. This may be blank for data derived from the older source format.
- `headlineText`: headline text

Repeated headline text issued in a short period is deduplicated. Dates are not filled in artificially, so a date may have no row when no report was issued or when the headline did not change.

### `warning_status_YYYYMM_{region_code}.csv`

This file contains warning/advisory status history by `areaType` and `areaCode`.

Columns:

- `officeCode`: issuing office or region code
- `publishingOffice`: issuing office name
- `areaType`: area type. `0` represents prefecture-level forecast areas and similar areas; `1` represents finer subdivision areas and similar areas.
- `areaCode`: target area code
- `reportDatetime`: report time
- `controlDatetime`: control time. This may be blank for data derived from the older source format.
- `infoType`: information type. This may be blank for data derived from the older source format.
- `activeWarningCodes`: JSON array of `warningCode` values active at that time
- `warningStatuses`: JSON object containing the status of each `warningCode` confirmed on that row

`activeWarningCodes` is the set of warning/advisory codes active at that time. `warningStatuses` contains statuses for active codes and for codes cancelled on that row. Status values include `発表`, `継続`, `解除`, `警報から注意報`, and `注意報から警報`.

For the same `areaType` and `areaCode`, repeated rows with the same state on the same date are removed. If warnings or advisories continue across dates, the first confirmation row on a new date may remain. A date with no row does not always mean the data is missing; it may mean there was no state change or no confirmation row that needed to be kept.

Example:

```csv
activeWarningCodes,warningStatuses
"[""07"",""15"",""16""]","{""07"":""発表"",""15"":""継続"",""16"":""継続""}"
"[""07"",""16""]","{""07"":""継続"",""15"":""解除"",""16"":""継続""}"
```

## Usage

No prior permission is required to use the weather forecast, precipitation probability, warning, or advisory data in this repository. Please feel free to use it.

If you use the data in public research or another public work, I would appreciate a short note in an Issue comment describing how you used it.

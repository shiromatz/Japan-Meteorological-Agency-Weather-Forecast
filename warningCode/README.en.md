# warningCode Lookup Tables

`warningCode` represents the type of a JMA warning or advisory. Treat it as a string, not as an integer, because some codes contain leading zeroes such as `02` and `03`.

The warning/advisory system changes on 2026-05-29, so the lookup tables are separated by directory.

- `legacy/`: system used through 2026-05-28
- `r8/`: Reiwa 8 system used from 2026-05-29

Each directory contains the same lookup table in two formats.

- `warningCode.csv`: CSV lookup table
- `warningCodes.json`: JSON lookup table

Columns in the CSV files:

- `warningCode`: warning/advisory code, intended to be handled as a string
- `phenomenon`: phenomenon or warning/advisory type
- `level`: warning level. This is blank for the legacy system, release rows, and warnings or advisories whose names do not carry a warning level.
- `category`: special warning, danger warning, warning, advisory, or release
- `nameJa`: Japanese name
- `nameEn`: English name
- `notes`: notes

`warningCodes.json` is a JSON object keyed by `warningCode`. Each value has the same property names as the CSV columns. In JSON, `level` is `null` when no warning level applies.

`warningCode` is a type code, not the status itself. Check `warningStatuses` in `warning_status_YYYYMM_{region_code}.csv` for statuses such as issuance, continuation, or cancellation.

In the Reiwa 8 system, names corresponding to warning levels are used for heavy rain, landslide disaster, storm surge, and related warnings, and Level 4 danger warnings are added. Storm, heavy snow, and high wave warnings mainly keep the existing special warning, warning, and advisory naming. Flood warnings and flood advisories are no longer handled as independent warnings/advisories in the same way as the legacy system. Code `00` is documented as not used in the Reiwa 8 schema.

References:

- JMA: Reorganization of disaster prevention weather information and start of the new system: https://www.jma.go.jp/jma/kishou/know/bosai/keiho-update2026/index.html
- JMA: Warning and advisory criteria tables: https://www.jma.go.jp/jma/kishou/know/kijun/index.html
- DM-D.S.S: Weather warning/advisory JSON Schema / R06 format: https://dmdata.jp/docs/reference/conversion/json/schema/weather-warning/

# warningCode 対応表

`warningCode` は、気象庁の警報・注意報の種類を表すコードです。`02` や `03` のように先頭ゼロを含むため、整数ではなく文字列として扱ってください。

警報・注意報の体系は 2026年5月29日 から令和8年体系に変わるため、参照表はフォルダで分けています。

- `legacy/`: 2026年5月28日以前の体系
- `r8/`: 2026年5月29日以降の令和8年体系

各フォルダには同じ内容の対応表を2形式で置いています。

- `warningCode.csv`: CSV 形式の対応表
- `warningCodes.json`: JSON 形式の対応表

CSV の列:

- `warningCode`: 警報・注意報コード。文字列として扱う想定です。
- `phenomenon`: 現象・種別
- `level`: 警戒レベル。旧体系、解除、警戒レベル名を持たない警報・注意報では空欄です。
- `category`: 特別警報、危険警報、警報、注意報、解除の区分
- `nameJa`: 日本語名
- `nameEn`: 英語名
- `notes`: 注意事項

`warningCodes.json` は `warningCode` をキーにした JSON オブジェクトです。各値は CSV と同じ列名のプロパティを持ちます。JSON では、該当する警戒レベルがない場合の `level` は `null` です。

`warningCode` は警報・注意報の種別コードであり、発表中・継続・解除などの状態そのものではありません。状態は `warning_status_YYYYMM_{地域コード}.csv` の `warningStatuses` で確認してください。

令和8年体系では、大雨・土砂災害・高潮などで警戒レベルに対応した名称が使われ、レベル4危険警報が追加されます。一方で、暴風・大雪・波浪などは従来の特別警報・警報・注意報の名称が主に使われます。洪水警報・洪水注意報は、従来と同じ独立した警報・注意報としては扱われません。また、令和8年体系では `00` は運用しないコードとされています。

参考:

- 気象庁: 防災気象情報の体系整理と新たな防災気象情報の運用開始: https://www.jma.go.jp/jma/kishou/know/bosai/keiho-update2026/index.html
- 気象庁: 警報・注意報発表基準一覧表: https://www.jma.go.jp/jma/kishou/know/kijun/index.html
- DM-D.S.S: 気象警報・注意報 JSON Schema / R06形式: https://dmdata.jp/docs/reference/conversion/json/schema/weather-warning/

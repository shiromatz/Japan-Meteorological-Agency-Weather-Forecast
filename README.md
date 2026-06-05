# 気象庁発表の過去の天気予報のアーカイブ（履歴データベース）

英語版: [README.en.md](README.en.md)

日本の気象庁が過去に発表した天気予報のアーカイブ（履歴データベース）です。過去の実際の天気はさかのぼって入手できますが、過去の天気**予報**は公的に提供されていないため、個人的に日々の天気予報を定期的に取得していました。本データを用いれば、過去の天気予報の履歴を確認できます。

現在、以下の3種類のデータを公開しています。

- 詳細天気予報 (`forecast`)
- 概要予報 (`overview_forecast`)
- 警報・注意報 (`warning`)

以前は全般週間天気予報 (`overview_week`) も取得していましたが、更新・公開が止まっているため本リポジトリでは公開していません。

## 詳細天気予報 (`forecast`)

地域ごとの詳細天気予報 JSON を月単位にまとめ、CSV 化したものです。全地域・1か月分の CSV を ZIP 圧縮しています。これは過去に発表された予報の履歴であり、後から確定した実際の天気ではありません。

- 格納場所: `forecast/forecast_YYYYMM.zip`
- ZIP 内ファイル名: `forecast_YYYYMM_{地域コード}.csv`
- 収集元: `https://www.jma.go.jp/bosai/forecast/data/forecast/{region_code}.json`
- 期間: `2021-11-04` 以降
- 収集間隔: 1日3回前後
- 更新: 1か月分を ZIP 化し、原則として翌月初にアップロードします。
- 備考: 既知の欠測期間はありませんが、未知の取得漏れが含まれる可能性があります。定時発表に加えて訂正報があった場合、1日4回以上になることがあります。

通常の地域コードの CSV 列:

- `publishingOffice`: 発表官署名
- `reportDatetime`: 発表時刻
- `timeDefine`: 予報対象日時
- `areaName`: 対象区域名
- `areaCode`: 対象区域コード
- `weatherCodes`: 天気コード。対応表は `weatherCode.csv` / `weatherCodes.json` を参照してください。
- `weathers`: 天気の予報文
- `winds`: 風の予報文
- `waves`: 波の予報文
- `pops`: 降水確率
- `temps`: 気温
- `tempsMin`: 最低気温
- `tempsMax`: 最高気温
- `tempsMinUpper`: 最低気温の予測範囲上限
- `tempsMaxUpper`: 最高気温の予測範囲上限
- `tempsMinLower`: 最低気温の予測範囲下限
- `tempsMaxLower`: 最高気温の予測範囲下限
- `reliabilities`: 信頼度

全国予報の `forecast_YYYYMM_010000.csv` は、上記に加えて `regionName` と `regionCode` を持ち、予報対象日時の列名が `time` です。各列は元 JSON の時系列ごとに出力しているため、対象時刻や対象区域によって空欄になる項目があります。

`reportDatetime` は「その予報が発表された時刻」です。`timeDefine` または `time` は「予報の対象となる時刻」です。例えば、5月1日5時発表の予報の中に、5月2日0時を対象にした行が含まれることがあります。

地域コード:

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

`010000` は全国予報です。

## 概要予報 (`overview_forecast`)

地域ごとの概要予報 JSON を月単位にまとめ、CSV 化したものです。全地域・1か月分の CSV を ZIP 圧縮しています。これは過去に発表された予報文の履歴であり、後から確定した実際の天気ではありません。

- 格納場所: `overview_forecast/overview_forecast_YYYYMM.zip`
- ZIP 内ファイル名: `overview_forecast_YYYYMM_{地域コード}.csv`
- 収集元: `https://www.jma.go.jp/bosai/forecast/data/overview_forecast/{region_code}.json`
- 期間: `2021-12-15` 以降
- 収集間隔: 1日3回前後
- 更新: 1か月分を ZIP 化し、原則として翌月初にアップロードします。
- 備考: 既知の欠測期間はありませんが、未知の取得漏れが含まれる可能性があります。
- 地域コード: `010000` を除き、詳細天気予報と同じです。

CSV 列:

- `publishingOffice`: 発表官署名
- `reportDatetime`: 発表時刻
- `targetArea`: 対象地域
- `headlineText`: 見出し文
- `text`: 概要予報本文。元 JSON の改行はスペースに置換しています。

`reportDatetime` は「その概要予報が発表された時刻」です。概要予報の対象日時は独立した日時列ではなく、`headlineText` や `text` の本文中に文章として含まれます。

## 天気コード対応表

`forecast` の `weatherCodes` を読み取るための対応表として、以下の2形式を置いています。

- `weatherCode.csv`: CSV 形式の天気コード対応表
- `weatherCodes.json`: JSON 形式の天気コード対応表

`weatherCode.csv` の列:

- `weatherCode`: 天気コード
- `weatherPictDay`: 昼の天気アイコンファイル名
- `weatherPictNight`: 夜の天気アイコンファイル名
- `Group`: 天気分類グループ
- `weatherJ`: 日本語の天気名
- `weatherE`: 英語の天気名

`weatherCodes.json` は `weatherCode` をキーにした JSON オブジェクトで、値は `weatherPictDay`、`weatherPictNight`、`Group`、`weatherJ`、`weatherE` の順の配列です。

## 警報・注意報コード対応表

`warning` の `warningCode` を読み取るための対応表は、警報・注意報の体系別に配置しています。

- `warningCode/legacy/warningCode.csv` / `warningCode/legacy/warningCodes.json`: 2026年5月28日以前の体系
- `warningCode/r8/warningCode.csv` / `warningCode/r8/warningCodes.json`: 2026年5月29日以降の令和8年体系

`warningCode` は `02` や `03` のように先頭ゼロを含むため、文字列として扱ってください。対応表の詳細は [`warningCode/README.md`](warningCode/README.md) を参照してください。

## 警報・注意報 (`warning`)

気象庁が発表した警報・注意報の発表履歴です。観測値や実際に発生した気象現象の記録ではありません。

- 格納場所: `warning/warning_YYYYMM.zip`
- 収集元: 気象庁の警報・注意報 JSON。現在の URL 形式は `https://www.jma.go.jp/bosai/warning/data/r8/{region_code}.json` です。
- 公開済みファイル月: `202502` から `202605`
- データ期間: `reportDatetime` 基準で `2025-02-07T15:50:00+09:00` から `2026-05-28T11:31:00+09:00`
- 欠測・未公開期間: `2025-02-07T15:50:00+09:00` より前、`2026-05-28T11:31:00+09:00` より後から `2026-05-31` まで、および `2026-06` 以降の公開 ZIP は含まれていません。
- 地域コード: `010000` を除き、詳細天気予報と同じです。

日時列は ISO 8601 形式の文字列です（例: `2025-02-08T04:05:00+09:00`）。表計算ソフトで開くとシリアル値などに自動変換される場合があります。

### `warning_headline_YYYYMM_{地域コード}.csv`

警報・注意報の見出し文を、主に都道府県単位の発表履歴として整理したファイルです。見出し文は細分区域側のデータでも概ね共通であるため、代表として `areaType=0` 相当のデータから作成しています。

列:

- `officeCode`: 発表官署・地域コード
- `publishingOffice`: 発表官署名
- `reportDatetime`: 発表時刻
- `controlDatetime`: 管理時刻。旧形式由来のデータでは空欄の場合があります。
- `infoType`: 情報種別。旧形式由来のデータでは空欄の場合があります。
- `headlineText`: 見出し文

同じ見出し文が短時間に繰り返される場合は重複を抑えています。日付の補完は行っていないため、発表がない日や見出し文が変わらない日は、その日の行が存在しないことがあります。

### `warning_status_YYYYMM_{地域コード}.csv`

地域ごとの警報・注意報の状態を、`areaType` と `areaCode` ごとに整理したファイルです。

列:

- `officeCode`: 発表官署・地域コード
- `publishingOffice`: 発表官署名
- `areaType`: 区域種別。`0` は府県予報区等、`1` は二次細分区域等を表します。
- `areaCode`: 対象区域コード
- `reportDatetime`: 発表時刻
- `controlDatetime`: 管理時刻。旧形式由来のデータでは空欄の場合があります。
- `infoType`: 情報種別。旧形式由来のデータでは空欄の場合があります。
- `activeWarningCodes`: その時点で有効な `warningCode` の JSON 配列
- `warningStatuses`: その行で確認された `warningCode` ごとの状態を表す JSON オブジェクト

`activeWarningCodes` は、その時点で発表中の警報・注意報コードの集合です。`warningStatuses` には、発表中のコードの状態と、その行で解除されたコードの状態が入ります。状態値には `発表`、`継続`、`解除`、`警報から注意報`、`注意報から警報` などが含まれます。

`warningCode` の名称は、`reportDatetime` が 2026年5月28日以前の場合は `warningCode/legacy/`、2026年5月29日以降の場合は `warningCode/r8/` の対応表を参照してください。

同じ `areaType`・`areaCode` について、同一日内に同じ状態が繰り返される行は削除しています。一方で、警報・注意報が継続している場合は、日付が変わった最初の確認行が残ることがあります。行がない日は、必ずしも欠測ではなく、状態変化や残すべき確認行がなかったことを意味する場合があります。

例:

```csv
activeWarningCodes,warningStatuses
"[""07"",""15"",""16""]","{""07"":""発表"",""15"":""継続"",""16"":""継続""}"
"[""07"",""16""]","{""07"":""継続"",""15"":""解除"",""16"":""継続""}"
```

## 利用について

本リポジトリの天気予報、降水確率、警報・注意報データを使用する場合、使用許諾等の事前手続きは必要ありません。ご自由にご利用ください。

公開可能な研究などで利用された場合は、事後で構いませんので「こんな形で利用しました」と Issue のコメントなどでお知らせいただけると励みになります。

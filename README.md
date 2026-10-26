# hermes-daily-digest

Hermes Agent（NewsFromAI ジョブ）が毎日 0:00 / 7:00 / 12:00 / 17:00 (JST) に生成する
世界の経済・マーケット・ニュース・AI のダイジェスト置き場。

- `digests/<YYYY-MM-DD>/digest-<YYYYMMDD>-<HHMM>.md` — 全文（ウォッチリスト全銘柄の付録つき）
- `digests/<YYYY-MM-DD>/digest-<YYYYMMDD>-<HHMM>.html` — 単一ファイルの HTML インフォグラフィック

株価・決算・マクロ指標は TradingView MCP から取得。AI 指標は Artificial Analysis MCP。
日本株は直近の東証終値、米株は直近の NY 終値が基準（各ファイル冒頭に基準時刻を記載）。

元のダイジェストは Notion「NewsFromAI」ページ配下にも作成される。

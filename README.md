# hermes-daily-digest

Hermes Agent の cron ジョブ `NewsFromAI`（`0 7,17 * * *`）が**毎日 7:00 / 17:00（JST）**に自動生成する、
世界の経済・マーケット・ニュース・AI のダイジェスト置き場。

- 目次（アーカイブ）: <https://tazrt.github.io/hermes-daily-digest/digests/>
- 公開設定: `wrangler.jsonc`（Cloudflare Workers の静的アセット、`assets.directory` は `./digests`）

## 構成

```
digests/
├── index.html                     # 全ダイジェストの目次（自動生成）
├── YYYY-MM-DD/
│   ├── digest-YYYYMMDD-HHMM.html  # HTML インフォグラフィック（外部アセットなしの単一ファイル）
│   ├── digest-YYYYMMDD-HHMM.md    # 同内容の Markdown（ウォッチリスト全銘柄の付録つき）
│   └── index.html                 # その日の最新版のコピー
├── 2026-11-18-ai/                 # AI 指標版（単発）
└── ai/YYYY-MM-DD/                 # AI 指標版
    ├── digest-ai-YYYYMMDD-HHMM.html
    ├── digest-ai-YYYYMMDD-HHMM.md
    └── index.html
```

- 収録: **ダイジェスト 68本 / 27日分**（2026-10-26 〜 2026-11-22、うち AI 指標版 2本）。
  HTML 68本・Markdown 67本（`2026-11-18-ai/` の1本だけ `.md` 未生成）。日付ごとの `index.html` は14日分
- 生成タイミング: **7:00 と 17:00（JST）**。2026-10-28 〜 11-04 のファイルには旧スケジュール（0:00 / 12:00）で
  作られたものが混在する
- ファイル名の時刻は「生成時刻」であり、市場データの基準時刻ではない（基準は各ファイル冒頭に明記）

## 目次（`digests/index.html`）

- 最新の1本を強調カード、残りを**日付ごとに新しい順**（2026-11-22 → 2026-10-26）で並べる
- 各行の要約は `og:description` → 本文リード → KPI → 基準時刻 の順に拾い、定型句（タイトル＋日時）は除去
- リンクは相対パスなので、GitHub Pages の `/digests/` でも Workers のルートでも同じ HTML が動く
- 生成は `~/.hermes/scripts/build_digest_index.py`。`push_digest_to_github.py` が push 直前に毎回呼ぶため、
  **ダイジェストを追加すれば目次も自動で更新される**
- 単体で作り直す場合:

  ```bash
  python3 ~/.hermes/scripts/build_digest_index.py --no-commit   # 生成のみ
  python3 ~/.hermes/scripts/build_digest_index.py --push        # commit & push まで
  ```

## データソース

| 用途 | 取得元 |
| --- | --- |
| 株価・指数・決算・マクロ指標 | TradingView MCP（クォータ超過時は Yahoo Finance / Finviz で補完し、欠損は「データなし」と明記） |
| ニュース | 公式 RSS、Reuters、Bloomberg など |
| AI モデル指標 | Artificial Analysis MCP（Free tier） |

日本株は直近の東証終値、米株は直近の NY 終値が基準。休場日はその旨をファイル冒頭に記載する。

## 公開

- **GitHub Pages**（main / ルート）: <https://tazrt.github.io/hermes-daily-digest/>
  - 目次: `/digests/`、各ダイジェスト: `/digests/YYYY-MM-DD/digest-YYYYMMDD-HHMM.html`
- **Cloudflare Workers**（`wrangler.jsonc`）
  - `assets.directory: ./digests` のため、デプロイすると目次がサイトのルートになる
  - `git push` だけでは反映されない。`npx wrangler deploy` を実行するか、Cloudflare 側で Git 連携を設定する
- 元のダイジェストは Notion「NewsFromAI」ページ配下にも作成される

## 関連スクリプト（Hermes ホスト上）

| スクリプト | 役割 |
| --- | --- |
| `~/.hermes/scripts/collect_all.sh` | cron `NewsFromAI`（`0 7,17 * * *`）の前段。データ収集 |
| `~/.hermes/scripts/push_digest_to_github.py` | `digests/<日付>/` へコピー → 目次を再生成 → commit & push |
| `~/.hermes/scripts/build_digest_index.py` | `digests/index.html`（目次）の生成 |

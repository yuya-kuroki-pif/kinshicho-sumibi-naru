# kinshicho-sumibi-naru.com

「錦糸町 炭火 成る」公式サイトの静的サイト版です。既存の WordPress サイト
（https://kinshicho-sumibi-naru.com/）の文章・写真をもとに、GitHub Pages で公開できる
HTML / CSS / JS のみで再構築しています（外部フレームワーク・CDN は未使用。Web フォントは Google Fonts の Noto Serif JP のみ）。

## 構成

```
index.html                トップページ（ヒーロー → こだわり → お品書き → 席・空間 → Instagram → お知らせ → 店舗情報 → 姉妹店バナー）
concept/index.html        こだわり
course/index.html         コース（当日おまかせ / オールインクルーシブ / ペアリング付き / 甘鯛づくし）
table/index.html          席・空間（カウンター / テーブル / 個室）
sister-store/index.html   姉妹店紹介
media/index.html          メディア掲載実績
information/index.html    お知らせ一覧（直近3件は本文つき）
privacy/index.html        個人情報保護方針
css/style.css             共通スタイル（既存サイトの配色・明朝体の印象を踏襲）
js/main.js                ハンバーガーメニュー / 現在ページ表示 / フェードイン
images/                   既存サイトから取り込んだ写真・ロゴ（半角英数のファイル名に変更済み）
site.config.json          店名・住所・電話・営業時間・予約 URL などのサイト情報
CNAME                     GitHub Pages の独自ドメイン設定（kinshicho-sumibi-naru.com）
```

- URL は既存サイトと同じパス（`/concept/`, `/course/`, `/table/`, `/sister-store/`, `/media/`）を維持しています。
  既存の `/category`（お知らせ一覧）は `/information/`、`/privacy-policy/` は `/privacy/` に対応します。
- 既存サイトで 404 だった `/food`（お料理）・`/drink`（お飲み物）は作成していません。
- リンク・画像パスはルート相対（`/css/style.css` など）です。独自ドメイン直下に公開する前提です。
- スマホ幅では固定ボトムバー（電話 / 地図 / WEB予約）とハンバーガーメニューを表示します。

## 主な情報（site.config.json と同じ）

- 住所: 〒130-0022 東京都墨田区江東橋2丁目7-6 Uビル2階
- 電話: 03-6659-6886
- 営業時間: 17:00～23:00（L.O.22:00） / 定休日: 不定休
- WEB予約: https://www.tablecheck.com/shops/kinshicho-sumibi-naru/reserve
- Instagram: https://www.instagram.com/sumibinaru

## 更新のしかた

- 店舗情報・営業時間はトップページ `#access` セクションと各ページ共通フッター、`site.config.json` を修正します。
- お知らせは `information/index.html` の記事ブロックと一覧、トップページ `#news` の直近3件を更新します。
- ヘッダー・フッターは全ページに同じ HTML をコピーしているため、変更時は全 `index.html` をまとめて置換してください。

## ローカルで確認

```bash
python -m http.server 8000
```

ブラウザで http://localhost:8000 を開きます。

## 公開までの流れ

1. GitHub にこのリポジトリを push する
2. Settings → Pages で `main` ブランチ / ルートを公開元に設定する
3. ドメイン管理側で DNS を GitHub Pages に向ける（A レコード 4 件 + www の CNAME）
4. Pages 設定で "Enforce HTTPS" を有効にする

## 評価表示（トップページの Google / 食べログ）

- 表示データは `data/ratings.json`。トップページの `js/ratings.js` が読み込んで評価帯（`#ratings`）に表示する。
- **Google**: `.github/workflows/update-ratings.yml` が毎日 03:00（JST）に `scripts/update_google_rating.py` を実行し、Places API から点数・口コミ件数を取得して `data/ratings.json` を更新・コミットする。リポジトリの Secrets に `GOOGLE_MAPS_API_KEY` が必要。初回実行時に `google.query` で店舗を検索して `place_id` を保存する（`matched_name` / `matched_address` で正しい店舗か確認する）。
- **食べログ**: 自動取得は利用規約上できないため手入力。`tabelog.score` / `tabelog.count` / `tabelog.asof` を店舗ページを見て月 1 回程度更新する。
- 点数が未取得（null）の間は、その項目は表示されない。

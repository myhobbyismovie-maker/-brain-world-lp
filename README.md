# BRAIN WORLD LP

BRAIN WORLD（好きな作品を地図と年表に記録できるWebアプリ）のランディングページです。

- **ビルド不要の静的サイト**です（HTML + CSS + 画像だけ。JavaScript・npm なし）。
- アプリ本体とは別に、**サブドメイン**（例：`lp.example.com` / `www.example.com`）で公開する想定です。

## ファイル構成

```
index.html        LP本体
style.css         スタイル（色は先頭の :root 変数でまとめて変更可）
images/           ロゴ・画面スクショ・OGP画像・アイコン
robots.txt        検索エンジン向け設定
sitemap.xml       サイトマップ（Search Console に登録する）
old/              以前のLP（8/31版）。公開対象外・参照用
```

ローカル確認は `index.html` をブラウザで開くだけでOKです。

---

## 公開前に置き換えるもの（必須）

ファイル全体を検索・置換してください。

| 置き換える文字 | 内容 | 場所 |
|---|---|---|
| `LP_DOMAIN` | LPの公開ドメイン（例：`lp.example.com`。`https://` は付けない） | `index.html`（canonical・OGP・構造化データ）、`robots.txt`、`sitemap.xml` |
| `GA_MEASUREMENT_ID` | GA4の測定ID（`G-` で始まる文字列。2か所） | `index.html`（`<head>`） |

> アプリへのリンク先は設定済みです：ログイン `https://www.brain-world.net/login`、新規登録 `https://www.brain-world.net/signup`、アイディア一覧 `https://www.brain-world.net/ideas`

## 中身の差し替え（内容が決まりしだい）

`index.html` 内の `▼` コメントが目印です。

- [ ] **動画**：MOVIEセクションの `.video-box` 内を `<video>` に置き換える（流れ：地図 → 地図のアップ → 年表 → 年表のアップ → ポップアップ）
- [ ] **使い方のスクショ4枚**：`<div class="shot">…</div>` を `<img class="shot-img" src="images/…" alt="…">` に置き換える
- [ ] **アイディアのカード4枚**：トップ画像・タイトル・作成者名・リンク先
- [ ] ファーストビューの年表・地図スクショは概要資料から切り出したものです。高画質版があれば `images/screen-timeline.jpg` / `images/screen-map.jpg` を差し替える

---

## アクセス解析（GA4）

お問い合わせ窓口は置かず、Instagram・XのDMで受ける方針です。SNSとLPの行き来を、次の2つの方向で計測します。

### ① LP → SNS：SNSボタンのクリック（実装済み）

LP内のSNSリンクがクリックされると、GA4に `sns_click` イベントが送られます。

| パラメータ | 値 |
|---|---|
| `sns` | `instagram` / `x` |
| `link_location` | `cta`（下の青いエリアのボタン） / `footer`（フッター） |
| `link_url` | 遷移先URL |

**GA4側の設定（1回だけ）**
1. 管理 → データの表示 → **カスタム定義** → 「カスタムディメンションを作成」で、イベントパラメータ `sns` と `link_location` をそれぞれ登録する（範囲：イベント）。登録しないとレポートで内訳が見られません
2. 管理 → データの表示 → **イベント** で `sns_click` を「キーイベント」にすると、コンバージョンとして集計されます（任意）
3. 確認は レポート → リアルタイム、または 管理 → DebugView で、LPのSNSボタンを押して `sns_click` が届くかを見る

> 外部リンクのクリック自体は GA4 の拡張計測機能（`click` イベント）でも自動で取れますが、`sns_click` の方がSNS別・場所別に分けて見られます。

### ② SNS → LP：プロフィール・投稿に貼るリンク（UTM付き）

[Campaign URL Builder](https://ga-dev-tools.google/campaign-url-builder/) と同じ形式で作ったリンクです。`LP_DOMAIN` を置き換えてから、各SNSに貼ってください。GA4の「トラフィック獲得」レポートで、どこから何人来たかを見られます。

| 貼る場所 | リンク |
|---|---|
| Instagram プロフィール | `https://LP_DOMAIN/?utm_source=instagram&utm_medium=social&utm_campaign=profile` |
| Instagram ストーリーズ（リンクスタンプ） | `https://LP_DOMAIN/?utm_source=instagram&utm_medium=social&utm_campaign=story` |
| X プロフィール | `https://LP_DOMAIN/?utm_source=x&utm_medium=social&utm_campaign=profile` |
| X 投稿 | `https://LP_DOMAIN/?utm_source=x&utm_medium=social&utm_campaign=post` |

- 投稿ごとに分けて見たいときは、末尾に `&utm_content=beatles` のように投稿名を足す（例：BW TIMELINEのビートルズ回）
- 値は小文字・半角英数字でそろえる（`Instagram` と `instagram` は別物として集計されます）
- Instagramの通常投稿のキャプションはリンクが押せないため、プロフィールとストーリーズに貼ります

---

## 公開手順（サブドメイン）

どのホスティングでも「このリポジトリのルートをそのまま公開」すれば動きます。ビルドコマンドは不要、公開ディレクトリはルート（`/`）です。

### Vercel / Netlify / Cloudflare Pages の場合
1. 新規プロジェクトでこのリポジトリを選ぶ（Framework: なし / Other、Build command: 空、Output directory: `.` またはルート）
2. ダッシュボードの Domains で `lp.example.com` などのサブドメインを追加
3. DNS に表示された `CNAME` レコードを追加
4. `main` ブランチにマージされるたびに自動で再公開されます

### GitHub Pages の場合
1. Settings → Pages → Source を `Deploy from a branch`、ブランチ `main` / フォルダ `/(root)` にする
2. Custom domain にサブドメインを入力（リポジトリに `CNAME` ファイルが作られます）
3. DNS に `CNAME  lp  →  <ユーザー名>.github.io` を追加し、Enforce HTTPS をオンにする

> `old/` も一緒に公開されますが、`robots.txt` で検索対象外にしています。不要なら削除してください。

## 公開後にやること（SEO）

- [ ] [Google Search Console](https://search.google.com/search-console) にLPのドメインを登録し、`https://LP_DOMAIN/sitemap.xml` を送信
- [ ] Instagram・X のプロフィールに LP のURLを貼る
- [ ] シェア時のカード表示を確認（X / Facebook の共有プレビュー）

---

## 更新の流れ

修正は Claude 経由でプルリクエストとして出します。内容を確認して `main` にマージすると、ホスティングの自動デプロイでLPに反映されます。**公開後はこのリポジトリを正本**とし、デザインツール側のデータとは同期しません。

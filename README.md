# SINGULA — デプロイ & 広告設定ガイド

## ファイル構成
```
singula/
├── index.html       # メインサイト
├── api/
│   └── chat.js      # Claude APIプロキシ（Vercel Serverless Function）
├── vercel.json      # Vercel設定
└── README.md        # このファイル
```

---

## STEP 1 — Vercelにデプロイ

### 1-1. GitHubにpush
```bash
cd singula
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/teminoma-dotcom/singula.git
git push -u origin main
```

### 1-2. Vercelに接続
1. https://vercel.com にログイン
2. 「Add New Project」→ GitHubのsingulaリポジトリを選択
3. 「Deploy」をクリック

### 1-3. 環境変数を設定
Vercelのプロジェクト設定 → **Environment Variables** に追加：

| Name | Value |
|------|-------|
| `ANTHROPIC_API_KEY` | `sk-ant-xxxxxxxx...` |

設定後「Redeploy」する。

---

## STEP 2 — 忍者AdMax 広告IDの取得

### 2-1. 登録
https://admax.shinobi.jp/ でアカウント登録（無料・審査なし）

### 2-2. 広告枠を作成
1. ダッシュボード →「広告枠を追加する」
2. **PCサイト向け**を選択 → サイズ「728×90」または「300×250」推奨 → 枠名「SINGULA_バナー」「SINGULA_ポップアップ」などで作成
3. **スマホサイト向け**も同様に作成（320×50 など）

### 2-3. PC/SP切替タグを作成
1. 左メニュー「PC/SP広告切替コード」→「切替タグの作成」
2. PC欄・SP欄に先ほどの広告枠を選択
3. 「非同期タグを表示」→ 表示された **data-admax-id の値（IDのみ）** をメモ

### 2-4. index.html に設定
`index.html` 先頭の以下の部分を書き換え：

```javascript
const ADMAX_BANNER_ID  = 'ここにバナー用のID';    // 例: '1234abcd5678efgh'
const ADMAX_POPUP_ID   = 'ここにポップアップ用のID'; // 例: '8765hgfe4321dcba'
```

書き換えたら `git push` → Vercelが自動デプロイ。

---

## 広告の仕様

| 種類 | 表示タイミング |
|------|--------------|
| バナー広告 | ページ上部に常時表示 |
| ポップアップ広告 | スレを開いて2秒後 / 6レスごと |

※ IDが `YOUR_BANNER_ADMAX_ID` のままの場合はプレースホルダー表示になります（エラーにはなりません）

---

## ポイント

- **APIキーはサーバーサイド管理**：`api/chat.js` がプロキシになっているのでAPIキーがブラウザに露出しません
- **モデル**：`claude-haiku-4-5-20251001`（高速・低コスト）を使用
- **1スレ上限**：1000レス到達で書き込み不可になりスレ満員バッジが表示されます

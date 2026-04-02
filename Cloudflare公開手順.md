# Henceforth コーポレートサイト — Cloudflare Pages 公開手順

## 概要

| 項目 | 内容 |
|------|------|
| ホスティング | Cloudflare Pages（無料プラン） |
| デプロイ方式 | GitHub連携（pushで自動デプロイ） |
| ビルド | 不要（静的HTMLをそのまま配信） |
| ドメイン | henceforce.com |
| リポジトリ | github.com/tanahiro16-byte/henceforce_homepage |

---

## Step 1: Cloudflare アカウント準備

1. https://dash.cloudflare.com/ にログイン（アカウントがなければ作成）
2. 左メニュー「Workers & Pages」をクリック

---

## Step 2: Cloudflare Pages プロジェクト作成

1. 「Create」→「Pages」→「Connect to Git」
2. GitHubアカウントを連携し、`henceforce_homepage` リポジトリを選択
3. 設定：
   - **プロジェクト名**: `henceforce-homepage`（好きな名前でOK）
   - **本番ブランチ**: `main`
   - **ビルドコマンド**: **空欄のまま**（入力不要）
   - **ビルド出力ディレクトリ**: **`/`**（ルートそのまま）
4. 「Save and Deploy」

→ これで `henceforce-homepage.pages.dev` でサイトが公開される

---

## Step 3: カスタムドメイン設定（henceforce.com）

### パターンA: ドメインのDNSをCloudflareに移管する場合（推奨）

1. Cloudflare ダッシュボード → 「Websites」→「Add a site」→ `henceforce.com` を追加
2. 表示されるCloudflareのネームサーバーを、現在のドメインレジストラに設定
3. DNS反映を待つ（数時間〜最大48時間）
4. Pages プロジェクト → 「Custom domains」→ `henceforce.com` と `www.henceforce.com` を追加
5. Cloudflareが自動でDNSレコードを追加してくれる

### パターンB: DNSを移管せずCNAMEで接続する場合

1. 現在のDNS管理画面で以下を追加：
   - `CNAME` `henceforce.com` → `henceforce-homepage.pages.dev`
   - `CNAME` `www` → `henceforce-homepage.pages.dev`
2. Pages プロジェクト → 「Custom domains」から追加

※ SSL証明書はCloudflareが自動発行するため、手動設定は不要

---

## Step 4: お問い合わせフォームのバックエンド

現在 `contact.html` のフォームは `action="#"` で、送信先がない。
公開前に以下のいずれかで対応が必要。

| 方法 | 特徴 |
|------|------|
| **Cloudflare Workers** | 無料枠あり。フォームデータを受け取りメール送信。Cloudflareで完結 |
| **Google Forms 埋め込み** | 最も手軽。デザインの自由度は低い |
| **Formspree / Getform** | 外部サービス。HTMLのaction属性にURLを入れるだけ。無料枠あり |
| **Web3Forms** | 無料。APIキーをHTMLに埋め込むだけで動く |

→ おすすめ: **Web3Forms** か **Formspree**（HTML変更だけで済む、サーバー不要）

---

## 公開前チェックリスト

### A. 企業としてのインフラ整備（サイト構築以前の話）

#### メールアドレス（最優先）

サイトに `info@henceforce.com` を掲載しているが、このメールが実際に届く状態でなければ公開できない。

| 方法 | 費用 | 特徴 |
|------|------|------|
| **Cloudflare Email Routing**（推奨） | 無料 | `info@henceforce.com` 宛のメールをGmail等の個人アドレスに転送。Cloudflareでドメイン管理するなら一番手軽 |
| **Google Workspace** | 月680円〜/ユーザー | `info@henceforce.com` でGmailが使える。カレンダー・ドライブ等も付属。企業として本格運用するなら最有力 |
| **さくらのメールボックス** | 月87円 | メールだけなら安い。日本語サポートあり |
| **Zoho Mail** | 無料（1ユーザー） | 無料で独自ドメインメールが使える |

**最小構成:** Cloudflare Email Routing で `info@henceforce.com` → 個人Gmail に転送。
返信時は Gmail の「別のアドレスから送信」設定で `info@henceforce.com` として返信可能。

**注意:** メールが届かない状態でサイトを公開すると、問い合わせが消失する。公開前に必ずテスト送信で確認すること。

#### プライバシーポリシー（公開前に必須）

お問い合わせフォームで名前・メールアドレスを収集するため、**プライバシーポリシーの掲載は必須**。

掲載すべき内容：
- 収集する個人情報の種類（氏名、メールアドレス）
- 利用目的（お問い合わせへの対応）
- 第三者提供の有無（しない）
- 個人情報の管理方法
- 問い合わせ窓口

→ `privacy.html` を新規作成し、フッター＋お問い合わせフォームからリンクする
→ 雛形はこちらのセッションで作成可能。必要なら言ってください

#### 特定商取引法に基づく表記

現時点では通信販売を行っていないため、法的には不要。
ただし、今後バディスタの受講料をオンラインで決済する場合は必要になる。
→ サービス内容に応じて随時判断

---

### B. 技術面の必須対応

- [ ] **Cloudflare Pages プロジェクト作成 & デプロイ確認**
- [ ] **カスタムドメイン設定**（henceforce.com）
- [ ] **メールアドレスの有効化**（info@henceforce.com が届く状態にする）
- [ ] **お問い合わせフォームのバックエンド設定**
- [ ] **プライバシーポリシーページの作成・リンク追加**
- [ ] `pages.dev` ドメインでの表示確認（PC / スマホ）

### C. 推奨（公開前にやると良い）

- [ ] ファビコン（favicon.ico）の追加
- [ ] OGP画像の設定（SNSシェア時のサムネイル）
- [ ] `hero-main.jpg`（1.6MB）の圧縮（目安: 200〜400KB程度に）
- [ ] 各ページの `<title>` / `<meta description>` の最終確認
- [ ] Google Search Console への登録
- [ ] 404ページの作成（Cloudflare Pagesは `404.html` を自動で使う）

### D. 将来対応

- [ ] 経営理念・キャッチコピーの正式策定
- [ ] 写真素材の差し替え（事業に関連するオリジナル写真）
- [ ] お知らせ記事が増えたらCMS化検討
- [ ] Google Analytics 等のアクセス解析導入（※導入時はプライバシーポリシーの更新も必要）
- [ ] Google Workspace 等への移行検討（メールの本格運用時）
- [ ] 特定商取引法に基づく表記（オンライン決済を始める場合）

---

## デプロイの流れ（公開後の運用）

```
コード修正 → git add & commit → git push origin main → 自動デプロイ（約30秒）
```

Cloudflare Pagesは `main` ブランチへのpushで自動的にデプロイされる。
プレビューが必要な場合はブランチを切ってPRを出せば、プレビューURLが自動生成される。

---

## 現在のサイト構成

```
/
├── index.html              ← トップページ
├── company.html            ← 会社情報
├── business.html           ← 事業内容一覧
├── business-individual.html ← 個人向け詳細
├── business-institution.html← 教育機関向け詳細
├── news.html               ← お知らせ
├── contact.html            ← お問い合わせ
├── images/
│   ├── hero-main.jpg       ← トップヒーロー（1.6MB → 要圧縮）
│   ├── biz-individual.jpg  ← 個人向けカード画像
│   ├── biz-institution.jpg ← 教育機関向けカード画像
│   └── page-hero.jpg       ← サブページヒーロー
├── .gitignore
└── ホームページ要求定義書.md  ← 開発用ドキュメント（公開されるが問題なし）
```

# アーキテクチャ概要

## 結論

- MVPでは単一デプロイのモノリシック Web アプリを採用する
- 構成は `Browser` `Web App / Backend` `Database` を基本とする
- 認証は Cookie ベースの `SessionAuth` を採用する
- Markdown / PDF 出力はバックエンド内部モジュールとして実装する
- 将来の拡張に備えて、内部は責務ごとに分離しやすい構造にしておく

## 推奨システム構成

```text
[Browser]
   |
   | HTTPS + Session Cookie
   v
[Web App / Backend]
   |- UI Rendering or SPA Assets
   |- Auth
   |- Profile API
   |- EmploymentHistory API
   |- Project API
   |- Skill API
   |- Resume Variant API
   |- Preview / Export API
   |
   +----> [PostgreSQL]
   |
   +----> [Markdown / PDF Generator]
```

## この構成を採用する理由

### 1. MVPを最短で成立させやすい

- 機能の中心は認証、プロフィール、案件管理、経歴書出力である
- これらは単一アプリケーション内で実装した方が連携しやすい
- 初期段階でサービス分割を行うメリットは小さい

### 2. 認証との相性がよい

- 今回の認証方式は Cookie ベースの SessionAuth である
- Browser と Web App / Backend を近い境界で扱う方が実装しやすい
- ログイン、ログアウト、`/auth/me`、セッション管理を一貫して設計できる

### 3. 出力機能を内包しやすい

- このプロダクトでは Markdown / PDF 出力が主要価値の1つである
- まずはバックエンド内部モジュールとして持つ方が構成が単純になる
- 将来、出力負荷が増えた段階でワーカーや別サービスへ分離できる

### 4. デバッグしやすい

- MVPでは変更速度が重要である
- 単一アプリケーション構成は、認証、API、出力の不具合を追いやすい
- ローカル開発や検証も単純になる

## 各コンポーネントの責務

### Browser

- ユーザー操作を受け付ける
- ログイン状態に応じて画面を出し分ける
- プロフィール、職歴、案件、スキル、経歴書バリエーションを編集する
- プレビュー結果を表示する
- Cookie によりセッションを保持する

### Web App / Backend

- 画面配信またはフロントエンド資産の配信を行う
- REST API を提供する
- SessionAuth を用いた認証と認可を行う
- 入力バリデーションを行う
- ドメインロジックを実行する
- プレビュー用データを組み立てる
- Markdown / PDF 出力を行う

### Database

- ユーザー情報を保持する
- プロフィールを保持する
- 職歴、案件、スキルを保持する
- 経歴書バリエーションを保持する
- セッション関連データを必要に応じて保持する

### Markdown / PDF Generator

- `ResumeVariant` をもとに表示用データを構築する
- Markdown を生成する
- HTML テンプレート経由で PDF を生成する
- 初期段階では Web App / Backend 内部モジュールとして実装する

## 境界の考え方

### 1. クライアント境界

- Browser と Server の間は HTTPS で通信する
- 認証は Session Cookie を用いる
- API 契約は `openapi.yaml` を基準にする

### 2. アプリケーション境界

- UI / API 層
- UseCase 層
- Domain / Data 層
- この責務分離を意識しておくことで、将来の変更に耐えやすくする

### 3. 永続化境界

- DB アクセスは Repository 層などを通じて扱う
- 永続化実装の都合を業務ロジックへ漏らしすぎない

## MVPで採用しない構成

### マイクロサービス

- 現時点では不要
- 機能規模に対して運用負荷が大きい

### 外部 PDF 専用サービス

- 初期は不要
- まずはバックエンド内で出力品質を確立する方がよい

### 複雑なイベント駆動構成

- MVPでは同期的な CRUD と export で十分である
- まずは単純なデータフローを優先する

### 過度に早いマルチテナント設計

- 今回は個人のWebエンジニア向けである
- 企業向けの複数人管理は MVP 対象外である

## 推奨技術的な着地点

- Client: Browser
- App: Single Web Application
- API Style: REST
- Auth: SessionAuth
- Storage: PostgreSQL
- Export: Backend internal module
- Deploy: 1 application + 1 database

## 将来の拡張ポイント

- Export 処理を別ワーカーへ分離する
- 検索や集計のための別 read model を用意する
- 公開プロフィール機能向けの配信系構成を追加する
- BearerAuth を追加し、外部クライアントやモバイルアプリに対応する

## 結論の再確認

- まずは単一デプロイのモノリスで始める
- ただし内部は責務分離された構成を意識する
- SessionAuth を前提に認証と API を一体で扱う
- 出力機能はバックエンド内に持つ
- MVPではシンプルさを優先する

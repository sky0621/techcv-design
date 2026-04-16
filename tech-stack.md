# 使用技術の選定

## 結論

- Frontend: `Next.js`
- Language(Frontend): `TypeScript`
- UI Styling: `Tailwind CSS`
- UI Components: `shadcn/ui`
- Form: `React Hook Form`
- Validation(Frontend): `zod`
- Backend: `Go`
- Router: `Chi`
- DB Access: `sqlc`
- Database: `PostgreSQL`
- Auth: Cookie ベースの `SessionAuth`
- API Contract: `OpenAPI`
- Export: `Markdown` 生成 + `HTML to PDF`

## この技術選定の方針

- MVPを早く作れることを重視する
- SessionAuth と相性がよいことを重視する
- 構造化されたデータと API 設計に素直に乗ることを重視する
- 将来の機能追加やリファクタリングに耐えやすいことを重視する

## Frontend

### `Next.js`

#### 採用理由

- 画面実装を素早く進めやすい
- フォーム中心の UI と相性がよい
- 将来的に SSR / CSR の使い分け余地を残せる
- 単一 Web アプリ構成と整合しやすい

#### 想定用途

- ログイン画面
- プロフィール編集画面
- 職歴・案件編集画面
- 経歴書バリエーション編集画面
- プレビュー画面

### `Tailwind CSS`

#### 採用理由

- MVP段階で UI を素早く組み立てやすい
- 経歴書管理のような管理画面系 UI と相性がよい
- コンポーネント単位の調整がしやすい

### `shadcn/ui`

#### 採用理由

- 管理画面やフォーム中心 UI を早く整えやすい
- `Tailwind CSS` と自然に組み合わせられる
- Dialog, Sheet, Table, Form, Tabs などの部品を揃えやすい
- デザインシステムの最初の土台として使いやすい

#### 想定用途

- 入力フォーム
- 一覧テーブル
- モーダル
- タブ切り替え
- セクションカード

### `React Hook Form`

#### 採用理由

- フォーム入力が多いプロダクトと相性がよい
- パフォーマンスと記述のバランスがよい
- `zod` と組み合わせやすい

### `zod`

#### 採用理由

- フロントエンド側の入力バリデーションを型安全に扱いやすい
- API request 前の検証に使いやすい
- フォームスキーマと相性がよい

## Backend

### `Go`

#### 採用理由

- API 実装が堅く書きやすい
- レイヤード構成と相性がよい
- 単一バイナリで運用しやすい
- 将来の保守性を確保しやすい

### `Chi`

#### 採用理由

- 軽量で扱いやすい
- middleware と routing を素直に組み立てられる
- REST API 中心の構成と相性がよい
- 過剰なフレームワーク依存を避けられる

#### 想定用途

- 認証 middleware
- ルーティング定義
- API handler の接続
- SessionAuth 前提の request 制御

## Database / Data Access

### `PostgreSQL`

#### 採用理由

- `User`, `Profile`, `Project`, `ResumeVariant` などの構造化データに向いている
- リレーショナル設計が自然にできる
- 将来的な拡張にも耐えやすい
- 必要に応じて JSON カラムも使える

### `sqlc`

#### 採用理由

- SQL を明示的に管理できる
- 型安全性が高い
- `Go` と相性がよい
- 設計したエンティティや API に対して、DBアクセスが追いやすい

#### 補足

- ORM に寄せすぎず、SQL を資産として管理する方針に合っている

## Auth

### Cookie ベースの `SessionAuth`

#### 採用理由

- このプロダクトは Web アプリ中心であり、セッション認証と相性がよい
- `HttpOnly` Cookie を使うことで認証情報管理を単純化しやすい
- MVPでは JWT を主軸にするより複雑さを抑えられる

#### 方針

- Cookie は `HttpOnly`, `Secure`, `SameSite=Lax`
- 認証状態確認は `GET /auth/me`
- 状態変更系 API には CSRF 対策を入れる

## API Contract

### `OpenAPI`

#### 採用理由

- API 仕様を正本として管理できる
- フロントエンドとバックエンドの認識を揃えやすい
- request / response / error を明文化しやすい
- 将来的に codegen やテストへ広げやすい

#### 関連ドキュメント

- [openapi.yaml](air-file://74mpjbg0chpcohbk3d4o/Users/sky0621/work/github.com/sky0621/techcv-products/techcv-design/openapi.yaml?type=file&root=%252F)
- [apis.md](air-file://74mpjbg0chpcohbk3d4o/Users/sky0621/work/github.com/sky0621/techcv-products/techcv-design/apis.md?type=file&root=%252F)

## Export

### `Markdown` 生成

#### 採用理由

- 構造を保ったまま出力しやすい
- MVP段階で中間表現として扱いやすい
- デバッグしやすい

### `HTML to PDF`

#### 採用理由

- Web プレビューとの整合を取りやすい
- テンプレート再利用がしやすい
- PDF 専用レイアウトを段階的に改善しやすい

## 非採用候補と理由

### フロントエンドで別案を主軸にしない理由

- 素の React のみだと構成判断を毎回行う必要がある
- Next.js の方が初期の開発速度を出しやすい

### バックエンドで重いフレームワークを使わない理由

- MVPでは軽量で見通しのよい構成を優先したい
- `Chi` で十分に routing / middleware / handler を構成できる

### ORM を主軸にしない理由

- DBアクセスの見通しを良くしたい
- SQL を明示して管理したい
- 今回の設計は `sqlc` と相性がよい

### JWT を主軸にしない理由

- MVP時点では Web 中心である
- SessionAuth の方がシンプルで安全に扱いやすい

## 推奨する最終構成

- Frontend: `Next.js + TypeScript + Tailwind CSS + shadcn/ui + React Hook Form + zod`
- Backend: `Go + Chi + sqlc`
- Database: `PostgreSQL`
- Auth: `SessionAuth`
- Contract: `OpenAPI`
- Export: `Markdown + HTML to PDF`

## 結論の再確認

- この技術選定は MVP を早く形にすることを優先している
- SessionAuth と構造化データを中心とした今回の要件に合っている
- 将来拡張の余地を残しつつ、初期実装の複雑さを抑えられる

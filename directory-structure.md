# ディレクトリ構成と責務

## 方針

- MVPでは単一デプロイのモノリシック Web アプリを前提にする
- ただしコードは責務ごとに分離し、変更しやすい構造にする
- 機能ごとのまとまりとレイヤーごとの責務を両立する
- 認証、プロフィール、職歴、案件、スキル、経歴書バリエーション、出力を主要な機能境界とする

## 全体イメージ

```text
techcv-app/
  frontend/
  backend/
    cmd/
      server/
    internal/
      auth/
      profile/
      employment-history/
      project/
      skill/
      resume-variant/
      export/
      shared/
    migrations/
    openapi/
  docs/
```

## この構成を採用する理由

### 1. MVPを素早く開発しやすい

- デプロイ単位は1つなので、開発と運用が単純である
- その一方でコードは責務分離されているため、機能追加で破綻しにくい

### 2. 機能境界が追いやすい

- `auth`
- `profile`
- `employment-history`
- `project`
- `skill`
- `resume-variant`
- `export`

上記単位でファイル群をまとめることで、変更範囲を追いやすくする

### 3. アーキテクチャ方針と整合しやすい

- [architecture.md](air-file://74mpjbg0chpcohbk3d4o/Users/sky0621/work/github.com/sky0621/techcv-products/techcv-design/architecture.md?type=file&root=%252F) で決めたモノリス構成と相性がよい
- [openapi.yaml](air-file://74mpjbg0chpcohbk3d4o/Users/sky0621/work/github.com/sky0621/techcv-products/techcv-design/openapi.yaml?type=file&root=%252F) の API 群とも対応づけやすい

## Backend の推奨構成

```text
backend/
  cmd/
    server/
      main.*
  internal/
    auth/
      handler/
      usecase/
      domain/
      repository/
    profile/
      handler/
      usecase/
      domain/
      repository/
    employment-history/
      handler/
      usecase/
      domain/
      repository/
    project/
      handler/
      usecase/
      domain/
      repository/
    skill/
      handler/
      usecase/
      domain/
      repository/
    resume-variant/
      handler/
      usecase/
      domain/
      repository/
    export/
      handler/
      usecase/
      domain/
      generator/
    shared/
      middleware/
      session/
      db/
      errors/
      validator/
      types/
  migrations/
  openapi/
```

## Backend 各ディレクトリの責務

### `cmd/server`

- アプリケーションの起動エントリポイント
- DI の初期化
- ルーティング初期化
- サーバー起動処理

### `internal/<feature>/handler`

- HTTP request/response の受け渡し
- OpenAPI と実装の接続
- 入力値の受け取り
- 認証済みユーザー情報の取り出し
- UseCase 呼び出し

### `internal/<feature>/usecase`

- アプリケーションのユースケースを表現する
- 例: `CreateProject`, `UpdateProfile`, `LoginUser`, `ExportResumePdf`
- トランザクション境界の中心になる
- 複数 Repository や Domain を組み合わせる

### `internal/<feature>/domain`

- エンティティ
- 値オブジェクト
- ビジネスルール
- 状態遷移の制約

### `internal/<feature>/repository`

- 永続化の抽象
- DB 実装への依存を隠す
- UseCase から直接 SQL 詳細を見せない

### `internal/export/generator`

- Markdown 生成
- HTML テンプレート組み立て
- PDF 生成
- 出力フォーマットごとの差異吸収

### `internal/shared`

- 機能横断の共通処理
- Middleware
- Session 管理
- DB 接続
- エラーハンドリング
- バリデーション
- 共通型

## Frontend の推奨構成

```text
frontend/
  src/
    pages/
    features/
      auth/
      profile/
      employment-history/
      project/
      skill/
      resume-variant/
      export/
    components/
    api/
    lib/
    types/
```

## Frontend 各ディレクトリの責務

### `pages`

- 画面単位のエントリポイント
- ルーティング対象となる UI を置く

### `features`

- 機能単位の UI と状態管理を置く
- バックエンドの機能境界と対応づけやすくする

### `components`

- 汎用 UI コンポーネントを置く
- 機能に閉じない部品をまとめる

### `api`

- バックエンド API 呼び出しをまとめる
- OpenAPI 由来の client を置いてもよい

### `lib`

- 認証状態の保持
- format 関数
- 共通ユーティリティ

### `types`

- フロントエンド側の型定義を置く
- OpenAPI 由来の型をまとめてもよい

## 最初に作る最小構成

```text
backend/
  cmd/server/
  internal/
    auth/
    profile/
    employment-history/
    project/
    skill/
    resume-variant/
    export/
    shared/
```

- `dashboard` や `master` は後から追加でよい
- MVP ではまず主要ユースケースに集中する

## 命名方針

- ディレクトリ名は `kebab-case` を推奨する
- 例: `employment-history`, `resume-variant`
- 可読性を優先し、略語を増やしすぎない
- 機能名と API 名が対応しやすい名前を選ぶ

## 避けたい構成

### 平坦すぎる構成

- `controllers`, `services`, `models` のみで全機能を混在させる構成
- 機能追加時に追跡しづらくなる

### `shared` への過剰集約

- 本来 feature に属する責務まで `shared` に置かない
- まずは feature 側へ寄せ、横断性が明確になったら共通化する

### Domain の空洞化

- すべてのルールを UseCase 層だけに書かない
- 状態制約や意味的ルールは Domain 側へ寄せる

### 早すぎる分散構成

- `export` を最初から別サービスにしない
- MVP では単純な構成を優先する

## 結論

- ディレクトリは「機能」と「責務」で分ける
- デプロイはモノリスでも、コードは整理された構造にする
- `auth`, `profile`, `employment-history`, `project`, `skill`, `resume-variant`, `export` を主軸にする
- `shared` は横断関心のみに絞る
- MVPでは最小構成で始め、必要に応じて段階的に拡張する

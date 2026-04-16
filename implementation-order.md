# 最初に実装する API 順序

## 方針

- API の実装順は、依存関係の少なさだけでなく MVP 価値を最短で検証できる順を優先する
- まずは認証と本人データの作成を成立させる
- 次に経歴の元データを作れるようにする
- 最後に提出用の見せ方と出力を実装する

## 推奨する実装フェーズ

### フェーズ1: 認証

1. `POST /auth/register`
2. `POST /auth/login`
3. `GET /auth/me`
4. `POST /auth/logout`

#### この順にする理由

- 以降の API はすべて認証済みユーザー前提である
- SessionAuth を先に固めることで、その後の実装方針がぶれにくい
- `GET /auth/me` があると画面初期化とログイン維持確認がしやすい

### フェーズ2: プロフィール

5. `GET /profile`
6. `PUT /profile`

#### この順にする理由

- ログイン直後に最低限の本人情報を編集できるようにするため
- ダッシュボードやプレビューの土台になるため

### フェーズ3: 経歴の元データ

7. `GET /employment-histories`
8. `POST /employment-histories`
9. `PUT /employment-histories/{employmentHistoryId}`
10. `GET /projects`
11. `POST /projects`
12. `GET /projects/{projectId}`
13. `PUT /projects/{projectId}`

#### この順にする理由

- 経歴書の中心データは案件であり、まずここを登録できる必要がある
- ただし案件は職歴に紐づくため、職歴 API を先に揃える
- 一覧取得と作成・更新を先に成立させることで、画面実装が進めやすい

### フェーズ4: スキル連携

14. `GET /skills`
15. `PUT /projects/{projectId}/skills`

#### この順にする理由

- スキルは案件入力と密接に関係するため、案件 API の後が自然である
- MVP ではまず案件に対してスキルを紐づけられればよい
- 個別のスキル CRUD は後続フェーズでも成立する

### フェーズ5: 経歴書バリエーション

16. `GET /resume-variants`
17. `POST /resume-variants`
18. `PUT /resume-variants/{resumeVariantId}/projects`

#### この順にする理由

- 元データが揃った後で、提出先向けの見せ方を作れるようにする
- MVP ではまず 1 つの派生版を作れて、表示対象案件を選べれば十分である

### フェーズ6: プレビュー・出力

19. `GET /resume-variants/{resumeVariantId}/preview`
20. `POST /resume-variants/{resumeVariantId}/export/markdown`
21. `POST /resume-variants/{resumeVariantId}/export/pdf`

#### この順にする理由

- プレビューで見え方を確認できてから出力へ進む方が自然である
- Markdown 出力は PDF より実装負荷が低く、構造確認にも使いやすい
- PDF は最終出力として最後に実装するのが安全である

## 初期実装の優先トップ10

1. `POST /auth/register`
2. `POST /auth/login`
3. `GET /auth/me`
4. `POST /auth/logout`
5. `GET /profile`
6. `PUT /profile`
7. `POST /employment-histories`
8. `GET /employment-histories`
9. `POST /projects`
10. `GET /projects`

## 最初の到達目標

- ユーザー登録ができる
- ログイン状態を Cookie セッションで維持できる
- 自分のプロフィールを取得・更新できる
- 職歴を 1 件以上登録できる
- 案件を 1 件以上登録できる
- 1 つの経歴書バリエーションを作成できる
- プレビューを表示できる
- Markdown で出力できる

## 後回しでよい API

- `DELETE` 系 API
- `POST /resume-variants/{resumeVariantId}/duplicate`
- `GET /projects/{projectId}/contributions`
- `POST /projects/{projectId}/contributions`
- `PUT /projects/{projectId}/contributions/{contributionId}`
- `DELETE /projects/{projectId}/contributions/{contributionId}`
- `GET /master/skill-categories`
- `GET /master/project-phases`
- `GET /dashboard`
- 個別 `Skill` の詳細取得・更新・削除

## 実装メモ

- 削除系 API は MVP 初期段階では不要なことが多く、後回しでよい
- まずは「作れる・見られる・出せる」を成立させることを優先する
- preview と markdown export が通れば、ユーザー価値の検証を早く回しやすい
- PDF は最初から完璧を目指さず、HTML からの変換で段階的に整える

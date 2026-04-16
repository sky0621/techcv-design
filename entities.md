# エンティティ設計

## User

- id
- email
- password_hash
- display_name
- created_at
- updated_at

## Profile

- id
- user_id
- full_name
- nickname
- location
- email
- phone
- summary
- github_url
- zenn_url
- qiita_url
- website_url
- preferred_work_style
- is_email_public
- is_phone_public
- created_at
- updated_at

## EmploymentHistory

- id
- user_id
- company_name
- employment_type
- start_date
- end_date
- note
- sort_order
- created_at
- updated_at

## Project

- id
- user_id
- employment_history_id
- name
- start_date
- end_date
- overview
- role
- team_size
- status
- sort_order
- created_at
- updated_at

## ProjectContribution

- id
- project_id
- phase
- achievement
- improvement
- note
- sort_order
- created_at
- updated_at

## Skill

- id
- user_id
- name
- category
- proficiency_level
- years_of_experience
- source_type
- created_at
- updated_at

## ProjectSkill

- id
- project_id
- skill_id
- created_at

## ResumeVariant

- id
- user_id
- name
- headline
- summary
- visibility_settings
- created_at
- updated_at

## ResumeVariantProject

- id
- resume_variant_id
- project_id
- sort_order
- created_at

## 想定リレーション

- `User` は 1 つの `Profile` を持つ
- `User` は複数の `EmploymentHistory` を持つ
- `EmploymentHistory` は複数の `Project` を持つ
- `Project` は複数の `ProjectContribution` を持つ
- `Project` と `Skill` は `ProjectSkill` を通じて多対多で関連する
- `User` は複数の `ResumeVariant` を持つ
- `ResumeVariant` は `ResumeVariantProject` を通じて複数の `Project` を表示対象として持つ

## 設計メモ

- MVPでは `User` ごとにデータを完全分離する
- `Project` を経歴の中心に置き、提出用の見せ方は `ResumeVariant` 側で調整する
- `Skill` は案件入力から半自動生成しつつ、手動編集もできるようにする
- 公開設定はまず `Profile` と `ResumeVariant` に閉じたシンプルな構造にする
- PDF出力用の専用エンティティは作らず、`ResumeVariant` から都度生成する

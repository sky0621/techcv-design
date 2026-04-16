# API一覧

- 正式な OpenAPI 定義は `openapi.yaml` を参照
- このファイルは API の読み物向けサマリー

## OpenAPI風の前提

- Base URL: `/api`
- Format: `application/json`
- Auth: Bearer Token または Session Cookie
- Scope: すべて認証済みユーザー向け
- Ownership: リソースの所有者は常にログイン中ユーザー本人

## Tags

- `Auth`
- `Profile`
- `EmploymentHistory`
- `Project`
- `ProjectContribution`
- `Skill`
- `ResumeVariant`
- `Export`
- `Dashboard`
- `Master`

## Common Schemas

### User

```yaml
User:
  type: object
  properties:
    id:
      type: string
    email:
      type: string
      format: email
    displayName:
      type: string
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

### Profile

```yaml
Profile:
  type: object
  properties:
    id:
      type: string
    userId:
      type: string
    fullName:
      type: string
    nickname:
      type: string
    location:
      type: string
    email:
      type: string
    phone:
      type: string
    summary:
      type: string
    githubUrl:
      type: string
    zennUrl:
      type: string
    qiitaUrl:
      type: string
    websiteUrl:
      type: string
    preferredWorkStyle:
      type: string
    visibilitySettings:
      type: object
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

### EmploymentHistory

```yaml
EmploymentHistory:
  type: object
  properties:
    id:
      type: string
    userId:
      type: string
    companyName:
      type: string
    employmentType:
      type: string
    startDate:
      type: string
      format: date
    endDate:
      type: string
      format: date
      nullable: true
    note:
      type: string
    sortOrder:
      type: integer
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

### Project

```yaml
Project:
  type: object
  properties:
    id:
      type: string
    userId:
      type: string
    employmentHistoryId:
      type: string
    name:
      type: string
    startDate:
      type: string
      format: date
    endDate:
      type: string
      format: date
      nullable: true
    overview:
      type: string
    role:
      type: string
    teamSize:
      type: integer
    status:
      type: string
      enum: [draft, published]
    sortOrder:
      type: integer
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

### ProjectContribution

```yaml
ProjectContribution:
  type: object
  properties:
    id:
      type: string
    projectId:
      type: string
    phase:
      type: string
    achievement:
      type: string
    improvement:
      type: string
    note:
      type: string
    sortOrder:
      type: integer
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

### Skill

```yaml
Skill:
  type: object
  properties:
    id:
      type: string
    userId:
      type: string
    name:
      type: string
    category:
      type: string
    proficiencyLevel:
      type: string
    yearsOfExperience:
      type: number
    sourceType:
      type: string
      enum: [manual, project]
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

### ResumeVariant

```yaml
ResumeVariant:
  type: object
  properties:
    id:
      type: string
    userId:
      type: string
    name:
      type: string
    headline:
      type: string
    summary:
      type: string
    visibilitySettings:
      type: object
    createdAt:
      type: string
      format: date-time
    updatedAt:
      type: string
      format: date-time
```

## Paths

### `POST /auth/register`

```yaml
summary: Register user
tags: [Auth]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [email, password, displayName]
        properties:
          email:
            type: string
            format: email
          password:
            type: string
            format: password
          displayName:
            type: string
responses:
  '201':
    description: Registered
    content:
      application/json:
        schema:
          type: object
          properties:
            user:
              $ref: '#/components/schemas/User'
            accessToken:
              type: string
```

### `POST /auth/login`

```yaml
summary: Login
tags: [Auth]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [email, password]
        properties:
          email:
            type: string
            format: email
          password:
            type: string
            format: password
responses:
  '200':
    description: Logged in
```

### `POST /auth/logout`

```yaml
summary: Logout
tags: [Auth]
responses:
  '200':
    description: Logged out
```

### `GET /auth/me`

```yaml
summary: Get current user
tags: [Auth]
responses:
  '200':
    description: Current user
```

### `GET /profile`

```yaml
summary: Get profile
tags: [Profile]
responses:
  '200':
    description: Profile
    content:
      application/json:
        schema:
          type: object
          properties:
            profile:
              $ref: '#/components/schemas/Profile'
```

### `PUT /profile`

```yaml
summary: Update profile
tags: [Profile]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        properties:
          fullName:
            type: string
          nickname:
            type: string
          location:
            type: string
          email:
            type: string
          phone:
            type: string
          summary:
            type: string
          githubUrl:
            type: string
          zennUrl:
            type: string
          qiitaUrl:
            type: string
          websiteUrl:
            type: string
          preferredWorkStyle:
            type: string
          visibilitySettings:
            type: object
responses:
  '200':
    description: Updated profile
```

### `GET /employment-histories`

```yaml
summary: List employment histories
tags: [EmploymentHistory]
responses:
  '200':
    description: Employment histories
```

### `POST /employment-histories`

```yaml
summary: Create employment history
tags: [EmploymentHistory]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [companyName, employmentType, startDate]
        properties:
          companyName:
            type: string
          employmentType:
            type: string
          startDate:
            type: string
            format: date
          endDate:
            type: string
            format: date
          note:
            type: string
          sortOrder:
            type: integer
responses:
  '201':
    description: Created employment history
```

### `GET /employment-histories/{employmentHistoryId}`

```yaml
summary: Get employment history
tags: [EmploymentHistory]
parameters:
  - in: path
    name: employmentHistoryId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Employment history detail
```

### `PUT /employment-histories/{employmentHistoryId}`

```yaml
summary: Update employment history
tags: [EmploymentHistory]
parameters:
  - in: path
    name: employmentHistoryId
    required: true
    schema:
      type: string
requestBody:
  required: true
responses:
  '200':
    description: Updated employment history
```

### `DELETE /employment-histories/{employmentHistoryId}`

```yaml
summary: Delete employment history
tags: [EmploymentHistory]
parameters:
  - in: path
    name: employmentHistoryId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Deleted employment history
```

### `GET /projects`

```yaml
summary: List projects
tags: [Project]
parameters:
  - in: query
    name: employmentHistoryId
    schema:
      type: string
  - in: query
    name: role
    schema:
      type: string
  - in: query
    name: skill
    schema:
      type: string
  - in: query
    name: q
    schema:
      type: string
  - in: query
    name: status
    schema:
      type: string
  - in: query
    name: sort
    schema:
      type: string
responses:
  '200':
    description: Projects
```

### `POST /projects`

```yaml
summary: Create project
tags: [Project]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [employmentHistoryId, name, startDate]
        properties:
          employmentHistoryId:
            type: string
          name:
            type: string
          startDate:
            type: string
            format: date
          endDate:
            type: string
            format: date
          overview:
            type: string
          role:
            type: string
          teamSize:
            type: integer
          status:
            type: string
          sortOrder:
            type: integer
responses:
  '201':
    description: Created project
```

### `GET /projects/{projectId}`

```yaml
summary: Get project
tags: [Project]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Project detail
```

### `PUT /projects/{projectId}`

```yaml
summary: Update project
tags: [Project]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
requestBody:
  required: true
responses:
  '200':
    description: Updated project
```

### `DELETE /projects/{projectId}`

```yaml
summary: Delete project
tags: [Project]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Deleted project
```

### `PUT /projects/{projectId}/order`

```yaml
summary: Update project order
tags: [Project]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [sortOrder]
        properties:
          sortOrder:
            type: integer
responses:
  '200':
    description: Updated project order
```

### `GET /projects/{projectId}/contributions`

```yaml
summary: List project contributions
tags: [ProjectContribution]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Project contributions
```

### `POST /projects/{projectId}/contributions`

```yaml
summary: Create project contribution
tags: [ProjectContribution]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        properties:
          phase:
            type: string
          achievement:
            type: string
          improvement:
            type: string
          note:
            type: string
          sortOrder:
            type: integer
responses:
  '201':
    description: Created contribution
```

### `PUT /projects/{projectId}/contributions/{contributionId}`

```yaml
summary: Update project contribution
tags: [ProjectContribution]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
  - in: path
    name: contributionId
    required: true
    schema:
      type: string
requestBody:
  required: true
responses:
  '200':
    description: Updated contribution
```

### `DELETE /projects/{projectId}/contributions/{contributionId}`

```yaml
summary: Delete project contribution
tags: [ProjectContribution]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
  - in: path
    name: contributionId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Deleted contribution
```

### `GET /skills`

```yaml
summary: List skills
tags: [Skill]
parameters:
  - in: query
    name: category
    schema:
      type: string
  - in: query
    name: q
    schema:
      type: string
  - in: query
    name: sourceType
    schema:
      type: string
responses:
  '200':
    description: Skills
```

### `POST /skills`

```yaml
summary: Create skill
tags: [Skill]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [name, category]
        properties:
          name:
            type: string
          category:
            type: string
          proficiencyLevel:
            type: string
          yearsOfExperience:
            type: number
          sourceType:
            type: string
responses:
  '201':
    description: Created skill
```

### `GET /skills/{skillId}`

```yaml
summary: Get skill
tags: [Skill]
parameters:
  - in: path
    name: skillId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Skill detail
```

### `PUT /skills/{skillId}`

```yaml
summary: Update skill
tags: [Skill]
parameters:
  - in: path
    name: skillId
    required: true
    schema:
      type: string
requestBody:
  required: true
responses:
  '200':
    description: Updated skill
```

### `DELETE /skills/{skillId}`

```yaml
summary: Delete skill
tags: [Skill]
parameters:
  - in: path
    name: skillId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Deleted skill
```

### `PUT /projects/{projectId}/skills`

```yaml
summary: Replace project skills
tags: [Skill]
parameters:
  - in: path
    name: projectId
    required: true
    schema:
      type: string
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [skillIds]
        properties:
          skillIds:
            type: array
            items:
              type: string
responses:
  '200':
    description: Updated project skills
```

### `GET /resume-variants`

```yaml
summary: List resume variants
tags: [ResumeVariant]
responses:
  '200':
    description: Resume variants
```

### `POST /resume-variants`

```yaml
summary: Create resume variant
tags: [ResumeVariant]
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [name]
        properties:
          name:
            type: string
          headline:
            type: string
          summary:
            type: string
          visibilitySettings:
            type: object
responses:
  '201':
    description: Created resume variant
```

### `GET /resume-variants/{resumeVariantId}`

```yaml
summary: Get resume variant
tags: [ResumeVariant]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Resume variant detail
```

### `PUT /resume-variants/{resumeVariantId}`

```yaml
summary: Update resume variant
tags: [ResumeVariant]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
requestBody:
  required: true
responses:
  '200':
    description: Updated resume variant
```

### `DELETE /resume-variants/{resumeVariantId}`

```yaml
summary: Delete resume variant
tags: [ResumeVariant]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Deleted resume variant
```

### `POST /resume-variants/{resumeVariantId}/duplicate`

```yaml
summary: Duplicate resume variant
tags: [ResumeVariant]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [name]
        properties:
          name:
            type: string
responses:
  '201':
    description: Duplicated resume variant
```

### `PUT /resume-variants/{resumeVariantId}/projects`

```yaml
summary: Replace projects in resume variant
tags: [ResumeVariant]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: object
        required: [projects]
        properties:
          projects:
            type: array
            items:
              type: object
              properties:
                projectId:
                  type: string
                sortOrder:
                  type: integer
responses:
  '200':
    description: Updated resume variant projects
```

### `GET /resume-variants/{resumeVariantId}/preview`

```yaml
summary: Get preview data
tags: [Export]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Preview data
```

### `POST /resume-variants/{resumeVariantId}/export/markdown`

```yaml
summary: Export markdown
tags: [Export]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
responses:
  '200':
    description: Markdown exported
```

### `POST /resume-variants/{resumeVariantId}/export/pdf`

```yaml
summary: Export PDF
tags: [Export]
parameters:
  - in: path
    name: resumeVariantId
    required: true
    schema:
      type: string
responses:
  '200':
    description: PDF exported
    content:
      application/pdf:
        schema:
          type: string
          format: binary
```

### `GET /dashboard`

```yaml
summary: Get dashboard summary
tags: [Dashboard]
responses:
  '200':
    description: Dashboard summary
```

### `GET /master/skill-categories`

```yaml
summary: List skill categories
tags: [Master]
responses:
  '200':
    description: Skill categories
```

### `GET /master/project-phases`

```yaml
summary: List project phases
tags: [Master]
responses:
  '200':
    description: Project phases
```

## Notes

- 一覧取得は MVP ではオフセットページネーションなしでもよい
- `visibilitySettings` は JSON カラム相当で柔軟に持つ想定にする
- `export/pdf` は初期段階ではサーバーサイドHTMLをPDF化する方式でもよい
- 公開URL機能はMVP対象外のため、外部共有専用APIは作らない
- 実装時に OpenAPI YAML へ正式化する場合は、この文書をベースに `components/schemas` と `responses` を共通化する

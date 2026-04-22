# .github

Organization-wide GitHub defaults and shared automation.

## Organization Issue/PR Bot rollout

This repository now provides:

- A centralized reusable workflow: `.github/workflows/org-bot.yml`
- A workflow template for other repositories: `.github/workflow-templates/org-bot-caller.yml`

### 1) Configure organization secrets

In **Organization Settings → Secrets and variables → Actions**, create:

- `EMAIL_USER`
- `EMAIL_PASS`

Grant both secrets to all repositories that should use the bot.

### 2) Enable in each repository

In each target repository, add the caller workflow from template:

- `.github/workflows/org-bot-caller.yml`

Or copy this minimum caller workflow:

```yml
name: Organization Issue/PR Bot Caller

on:
  issues:
    types: [opened]
  pull_request:
    types: [opened]

permissions:
  contents: read
  issues: write
  pull-requests: write

jobs:
  org-bot:
    uses: th30d4y/.github/.github/workflows/org-bot.yml@1ee789d935075c8a7dcef4a72f0df37e839879ee
    with:
      event_type: ${{ github.event_name == 'issues' && 'Issue' || 'Pull Request' }}
      number: ${{ github.event.issue.number || github.event.pull_request.number }}
      title: ${{ github.event.issue.title || github.event.pull_request.title }}
      url: ${{ github.event.issue.html_url || github.event.pull_request.html_url }}
    secrets: inherit
```

### 3) Validate and roll out

1. Enable on 1–2 repositories first.
2. Open a test issue and a test PR to confirm:
   - welcome comment is posted
   - owner email is sent
3. Roll out to remaining repositories.

### 4) Optional enforcement

If your GitHub plan supports required workflows/rulesets, enforce the caller workflow org-wide. Otherwise, each repository must include the caller workflow file.

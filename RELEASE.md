# hernco-dashboard — Release Workflow

## Source of Truth
- **Live repo:** `https://github.com/decarbX/hernco-dashboard`
- **Deployed:** `dash.decarbx.ai` (GitHub Pages)
- **Read-only clone:** `~/Desktop/decarbx-ai/projects/hernco-dashboard/` (locked, no write)
- **Working copy:** `~/Desktop/decarbx-ai/projects/hernco-dashboard-working/`
- **Current baseline:** v1.1 (June 25, 2026 — HRC REV POC1 Operations Brief with derate alert)

## Branch Strategy
- `main` — stable production branch. Never commit directly.
- Feature branches → pull requests → `main` after approval.
- Branch naming: `feature/<description>` or `fix/<description>`

## Release Versioning
- v1.1 → current production baseline (deployed)
- v1.2 → next release candidate
- Version labels: semantic (MAJOR.MINOR)
  - MAJOR: new dashboard sections, data sources, structural changes
  - MINOR: metric updates, styling, copy changes, bug fixes

## Workflow

### 1. Stage Locally
Make all changes in the working copy first:
```
~/Desktop/decarbx-ai/projects/hernco-dashboard-working/
```

### 2. Review Against Locked Baseline
Compare against the locked v1.1 baseline:
```
diff ~/Desktop/decarbx-ai/projects/hernco-dashboard/index.html \
     ~/Desktop/decarbx-ai/projects/hernco-dashboard-working/index.html
```

### 3. Commit Checklist
Before any commit, verify:
- [ ] No credentials, API tokens, or .env files
- [ ] No raw telemetry data (CSV, JSON, etc.)
- [ ] No customer data
- [ ] No generated reports or logs
- [ ] No deployment passwords or keys
- [ ] Changes reviewed against v1.1 baseline
- [ ] Version label included in commit message

### 4. Commit Format
```
v1.2 — <brief description of change>
```

### 5. Approval Gate
- All changes require explicit approval before push.
- 007 will not push to `main` without confirmation.
- GitHub authentication is handled by Sean separately.

### 6. Deploy
- GitHub Pages deploys from `main` automatically.
- CNAME `dash.decarbx.ai` must remain intact.

## Security Rules (LOCKED)
- NEVER commit: credentials, API tokens, .env, raw telemetry, customer data, reports, logs, deployment passwords
- This is a private production/customer repository
- Treat every commit as public-facing

## Quick Reference
```bash
# View current baseline
open ~/Desktop/decarbx-ai/projects/hernco-dashboard/index.html

# Diff working copy vs baseline
diff ~/Desktop/decarbx-ai/projects/hernco-dashboard/index.html \
     ~/Desktop/decarbx-ai/projects/hernco-dashboard-working/index.html

# Check what would be committed
cd ~/Desktop/decarbx-ai/projects/hernco-dashboard-working && git diff
```

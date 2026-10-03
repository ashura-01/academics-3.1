# Team Work Split and Git Commands (A, B, C, D)

Person **A** is the project lead. Each person has one branch. Replace the names and emails in the `--author` lines.

## Summary

| Person | Main areas |
|---|---|
| **A** | SIEM ingestion pipeline, SIEM agent/event pages, uptime, report and AI-patch jobs, Finding and Report models |
| **B** | SIEM detection and alerts, SIEM alert/rule/dashboard pages, chat and AI explain, main dashboard, admin |
| **C** | Scanner engine (10 tools), targets, scan runs, scanning frontend |
| **D** | Auth, config, build setup, layouts, billing |

Merge order: **D, then C, then A and B**.

---

## Step 0: do once (anyone, on main)

```bash
git checkout main
git pull
printf "backend/.php/\nbackend/vendor/\nnode_modules/\nbackend/.env\nbackend/public/build/\nbackend/public/hot\nbackend/storage/logs/*\nbackend/storage/framework/cache/*\nbackend/storage/framework/sessions/*\nbackend/storage/framework/views/*\n" >> .gitignore
git add .gitignore
git commit -m "Add gitignore rules for build and cache files"
git push origin main
```

Check `backend/.env.docker` for real keys or passwords before D commits it.

---

## Person A: SIEM ingestion, SIEM agent/event UI, uptime, reports and AI patch

**Backend**
- `backend/app/Siem/` (parsers, normalizers, ingest subfolders)
- `backend/app/Http/Controllers/Api/` (agent ingest endpoints)
- `SiemAgentController`, `SiemInstallController`, `SiemEventController`, `SiemAgentMiddleware`
- `ProcessSiemBatchJob`, `CheckSiemAgents`, `PruneSiemEvents`
- Models: `SiemAgent`, `SiemEvent`, `UptimeLog`, `Finding`, `Report`
- `UptimeController`, `CheckUptimeJob`, `UptimeService`
- `GenerateReportJob`, `GenerateAIPatchJob`
- `backend/resources/siem/`, `routes/api.php`, `routes/console.php`
- Tests: `SiemAgentTest`, `SiemParserTest`
- Docs: `AEGIS_SIEM_BUILD_INSTRUCTIONS.md`

**Frontend**
- SIEM pages for agents, events and install in `frontend/js/Pages/Siem/`

```bash
git checkout main
git checkout -b feature/siem-ingestion-reports

git add backend/app/Siem/ \
  backend/app/Http/Controllers/Api/ \
  backend/app/Http/Controllers/SiemAgentController.php \
  backend/app/Http/Controllers/SiemInstallController.php \
  backend/app/Http/Controllers/SiemEventController.php \
  backend/app/Http/Middleware/SiemAgentMiddleware.php \
  backend/app/Jobs/ProcessSiemBatchJob.php \
  backend/app/Console/Commands/CheckSiemAgents.php \
  backend/app/Console/Commands/PruneSiemEvents.php \
  backend/app/Models/SiemAgent.php \
  backend/app/Models/SiemEvent.php \
  backend/database/migrations/*_create_siem_agents_table.php \
  backend/database/migrations/*_create_siem_events_table.php \
  backend/resources/siem/ \
  backend/routes/api.php backend/routes/console.php \
  backend/tests/Feature/SiemAgentTest.php \
  backend/tests/Feature/SiemParserTest.php \
  AEGIS_SIEM_BUILD_INSTRUCTIONS.md \
  backend/app/Http/Controllers/UptimeController.php \
  backend/app/Jobs/CheckUptimeJob.php \
  backend/app/Services/UptimeService.php \
  backend/app/Models/UptimeLog.php \
  backend/database/migrations/*_create_uptime_logs_table.php \
  backend/app/Jobs/GenerateReportJob.php \
  backend/app/Jobs/GenerateAIPatchJob.php \
  backend/app/Models/Finding.php \
  backend/app/Models/Report.php \
  backend/database/migrations/*_create_findings_table.php \
  backend/database/migrations/*_create_reports_table.php \
  backend/database/migrations/*_unify_findings_retire_vulnerability_logs.php
# A's SIEM pages. Check names with: ls frontend/js/Pages/Siem
# git add frontend/js/Pages/Siem/Agents* frontend/js/Pages/Siem/Events* frontend/js/Pages/Siem/Install*

git status
git commit --author="A Name <a@email.com>" -m "Add SIEM ingestion, agent UI, uptime, reports and AI patch"
git push -u origin feature/siem-ingestion-reports
```

---

## Person B: SIEM detection, SIEM alert UI, chat, dashboard, admin

**Backend**
- `SiemAlertController`, `SiemRuleController`, `SiemDashboardController`
- `RunSiemDetectionJob`, `GenerateSiemAIExplainJob`, `SiemDiagnoseCommand`
- Models: `SiemAlert`, `SiemAllowlist`, `SiemRuleSetting`
- `backend/app/Siem/` rules, detection and enrichment subfolders
- `config/siem.php`
- `ChatController`, `ChatService`, `Services/Contracts/`
- `DashboardController`
- Tests: `SiemRuleTest`, `SiemHealthTest`
- Docs: `RECOVER_SIEM.md`

**Frontend**
- SIEM dashboard, alerts and rules pages in `frontend/js/Pages/Siem/`
- `ChatSidebar`, `Pages/Dashboard.jsx`, `Pages/Admin/`

```bash
git checkout main
git checkout -b feature/siem-detection-chat-dashboard

git add backend/app/Http/Controllers/SiemAlertController.php \
  backend/app/Http/Controllers/SiemRuleController.php \
  backend/app/Http/Controllers/SiemDashboardController.php \
  backend/app/Jobs/RunSiemDetectionJob.php \
  backend/app/Jobs/GenerateSiemAIExplainJob.php \
  backend/app/Console/Commands/SiemDiagnoseCommand.php \
  backend/app/Models/SiemAlert.php \
  backend/app/Models/SiemAllowlist.php \
  backend/app/Models/SiemRuleSetting.php \
  backend/database/migrations/*_create_siem_alerts_table.php \
  backend/database/migrations/*_create_siem_allowlists_table.php \
  backend/database/migrations/*_create_siem_rule_settings_table.php \
  backend/database/migrations/*_add_evidence_event_ids_to_siem_alerts_table.php \
  backend/config/siem.php \
  backend/tests/Feature/SiemRuleTest.php \
  backend/tests/Feature/SiemHealthTest.php \
  RECOVER_SIEM.md \
  backend/app/Http/Controllers/ChatController.php \
  backend/app/Services/ChatService.php \
  backend/app/Services/Contracts/ \
  frontend/js/Components/ChatSidebar.jsx \
  backend/app/Http/Controllers/DashboardController.php \
  frontend/js/Pages/Dashboard.jsx \
  frontend/js/Pages/Admin/
# B's SIEM backend subfolders and pages. Check names with: ls backend/app/Siem frontend/js/Pages/Siem
# git add backend/app/Siem/Rules/ backend/app/Siem/Detection/
# git add frontend/js/Pages/Siem/Dashboard* frontend/js/Pages/Siem/Alerts* frontend/js/Pages/Siem/Rules*

git status
git commit --author="B Name <b@email.com>" -m "Add SIEM detection, alert UI, chat, dashboard and admin"
git push -u origin feature/siem-detection-chat-dashboard
```

---

## Person C: scanner engine, targets and scanning frontend

**Backend**
- `backend/app/Scanning/` (10 tool wrappers, `Contracts/`, `NormalizedFinding`, `ToolRunResult`)
- `config/scanning.php`, `backend/wordlist/`, `AegisScanCommand`
- `ScanRunController`, `UpdateTargetRequest`
- Models: `ScanRun`, `ScanToolOutput`, `Target`
- Tests: `Feature/Integration/`, `Unit/`
- Docs: `AGENTS.md`, `agent.md`, `skills.json`

**Frontend**
- `Pages/Targets/`, `QuickScan/Index`, `Vulnerabilities/Index`, `Pages/ScanRuns/`, `Pages/Uptime/`

```bash
git checkout main
git checkout -b feature/scanning-engine

git add backend/app/Scanning/ \
  backend/config/scanning.php \
  backend/wordlist/ \
  backend/app/Console/Commands/AegisScanCommand.php \
  backend/app/Http/Controllers/ScanRunController.php \
  backend/app/Http/Requests/UpdateTargetRequest.php \
  backend/app/Models/ScanRun.php \
  backend/app/Models/ScanToolOutput.php \
  backend/app/Models/Target.php \
  backend/database/migrations/*_create_targets_table.php \
  backend/database/migrations/*_create_vulnerability_logs_table.php \
  backend/database/migrations/*_add_is_authorized_to_targets_table.php \
  backend/database/migrations/*_create_scan_runs_table.php \
  backend/database/migrations/*_create_scan_tool_outputs_table.php \
  backend/database/migrations/*_add_generate_report_to_scan_runs.php \
  backend/database/migrations/*_add_status_to_scan_tool_outputs_table.php \
  backend/tests/Feature/Integration/ \
  backend/tests/Unit/ \
  AGENTS.md agent.md skills.json \
  frontend/js/Pages/Targets/ \
  frontend/js/Pages/QuickScan/Index.jsx \
  frontend/js/Pages/Vulnerabilities/Index.jsx \
  frontend/js/Pages/ScanRuns/ \
  frontend/js/Pages/Uptime/

git status
git commit --author="C Name <c@email.com>" -m "Add scanning engine, targets and scanning frontend"
git push -u origin feature/scanning-engine
```

---

## Person D: foundation, config, build, layouts and billing

**Backend**
- All `Auth/` controllers, `Controller.php`, `BillingController`
- `Requests/Auth/`, `Requests/Admin/`, `ProfileUpdateRequest`
- `EnsureUserIsAdmin`, `HandleInertiaRequests`, `Policies/`, `Providers/`, `User.php`
- `routes/auth.php`, `routes/admin.php`, `routes/web.php`
- Config: app, auth, cache, filesystems, inertia, logging, mail, queue, session
- User, cache and jobs migrations, plus `subscription_tier`, `is_admin`, `llm_api_key`, `llm_provider_settings`
- Factories, seeders, `artisan`, `bootstrap/`, `public/`, `storage/`, `phpunit.xml`
- Tests: `Feature/Admin/`, `Feature/Auth/`, `ProfileTest`, `ExampleTest`, `TestCase`
- Build and env: `package.json`, `package-lock.json`, `vite.config.js`, `tailwind.config.js`, `postcss.config.js`, `.env.example`, `.env.docker`, `start.sh`, `app.blade.php`

**Frontend**
- `Pages/Auth/`, `Pages/Profile/`, `Pages/Billing/`, `Layouts/`
- `Checkbox`, `InputError`, `InputLabel`, `Modal`, `frontend/css/app.css`

```bash
git checkout main
git checkout -b feature/foundation-billing

git add backend/app/Http/Controllers/Auth/ \
  backend/app/Http/Controllers/Controller.php \
  backend/app/Http/Controllers/BillingController.php \
  backend/app/Http/Requests/Auth/ \
  backend/app/Http/Requests/Admin/ \
  backend/app/Http/Requests/ProfileUpdateRequest.php \
  backend/app/Http/Middleware/EnsureUserIsAdmin.php \
  backend/app/Http/Middleware/HandleInertiaRequests.php \
  backend/app/Policies/ backend/app/Providers/ \
  backend/app/Models/User.php \
  backend/routes/auth.php backend/routes/admin.php backend/routes/web.php \
  backend/config/app.php backend/config/auth.php backend/config/cache.php \
  backend/config/filesystems.php backend/config/inertia.php backend/config/logging.php \
  backend/config/mail.php backend/config/queue.php backend/config/session.php \
  backend/database/migrations/0001_01_01_00000*.php \
  backend/database/migrations/*_add_subscription_tier_to_users_table.php \
  backend/database/migrations/*_add_is_admin_to_users_table.php \
  backend/database/migrations/*_add_llm_api_key_to_users_table.php \
  backend/database/migrations/*_add_llm_provider_settings_to_users_table.php \
  backend/database/.gitignore backend/database/factories/ backend/database/seeders/ \
  backend/artisan backend/bootstrap/ backend/public/ backend/storage/ backend/phpunit.xml \
  backend/tests/Feature/Admin/ backend/tests/Feature/Auth/ \
  backend/tests/Feature/ProfileTest.php backend/tests/Feature/ExampleTest.php backend/tests/TestCase.php \
  backend/package.json backend/package-lock.json backend/vite.config.js \
  backend/tailwind.config.js backend/postcss.config.js \
  backend/.env.example backend/.env.docker backend/resources/views/app.blade.php start.sh \
  frontend/css/app.css \
  frontend/js/Pages/Auth/ frontend/js/Pages/Profile/ frontend/js/Pages/Billing/ \
  frontend/js/Layouts/ \
  frontend/js/Components/Checkbox.jsx frontend/js/Components/InputError.jsx \
  frontend/js/Components/InputLabel.jsx frontend/js/Components/Modal.jsx

git status
git commit --author="D Name <d@email.com>" -m "Add auth foundation, config, build setup, layouts and billing"
git push -u origin feature/foundation-billing
```

---

## After all four are pushed

1. Check for leftover files:
   ```bash
   git checkout main
   git status
   ```
   Anything still listed belongs to nobody. Add it to someone's branch.
2. Open four pull requests into `main`.
3. Merge in this order: **D, then C, then A and B**. If a PR shows a conflict, run `git checkout <branch> && git pull origin main` and resolve it.

## Notes

- If `git add` says a path "did not match any files", that file has a different name than guessed. Run `ls` on that folder and fix the path.
- Run `ls backend/app/Siem frontend/js/Pages/Siem` and split those folders between A and B using the commented `git add` lines.
- Do not commit `backend/.env.docker` if it contains real keys.
- If B feels too heavy, move `DashboardController` and `Pages/Dashboard.jsx` to C or D.

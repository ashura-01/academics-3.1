# Team Work Split and Git Commands (A, B, C, D)

Person **A** is the project lead. Each person has one branch. Replace the names and emails in the `--author` lines.

## Summary

| Person | Main areas |
|---|---|
| **A** | SIEM ingestion, SIEM agent/event pages, findings and reports models, auth backend, `User`, user migrations, dashboard, billing backend |
| **B** | SIEM detection and alerts, SIEM alert/rule/dashboard pages, chat, admin, report and AI patch jobs, middleware, policies, providers, routes, core config |
| **C** | Scanner engine (10 tools), targets, scan runs, scanning frontend |
| **D** | Frontend and build setup, plus light backend: uptime monitoring, admin requests, factories, seeders, supporting config, base tests |

Merge order: **A and B (foundation), then C, then D**.

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

## Person A: SIEM ingestion, findings and reports models, auth backend

**Backend**
- `backend/app/Siem/` (parsers, normalizers, ingest subfolders)
- `backend/app/Http/Controllers/Api/` (agent ingest endpoints)
- `SiemAgentController`, `SiemInstallController`, `SiemEventController`, `SiemAgentMiddleware`
- `ProcessSiemBatchJob`, `CheckSiemAgents`, `PruneSiemEvents`
- Models: `SiemAgent`, `SiemEvent`, `Finding`, `Report`, `User`
- `backend/resources/siem/`, `routes/api.php`, `routes/console.php`, `routes/auth.php`
- Auth: all `Auth/` controllers, `Controller.php`, `Requests/Auth/`
- `DashboardController`, `BillingController`, `ProfileUpdateRequest`
- Config: `auth`, `session`
- Migrations: users, cache, jobs, `subscription_tier`, `is_admin`, `llm_api_key`, `llm_provider_settings`
- Tests: `SiemAgentTest`, `SiemParserTest`, `Feature/Auth/`, `ProfileTest`
- Docs: `AEGIS_SIEM_BUILD_INSTRUCTIONS.md`

**Frontend**
- SIEM pages for agents, events and install in `frontend/js/Pages/Siem/`
- `Pages/Dashboard.jsx`

```bash
git checkout main
git checkout -b feature/siem-ingestion-auth

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
  backend/routes/api.php backend/routes/console.php backend/routes/auth.php \
  backend/tests/Feature/SiemAgentTest.php \
  backend/tests/Feature/SiemParserTest.php \
  AEGIS_SIEM_BUILD_INSTRUCTIONS.md \
  backend/app/Models/Finding.php \
  backend/app/Models/Report.php \
  backend/database/migrations/*_create_findings_table.php \
  backend/database/migrations/*_create_reports_table.php \
  backend/database/migrations/*_unify_findings_retire_vulnerability_logs.php \
  backend/app/Http/Controllers/Auth/ \
  backend/app/Http/Controllers/Controller.php \
  backend/app/Http/Requests/Auth/ \
  backend/app/Http/Controllers/DashboardController.php \
  backend/app/Http/Controllers/BillingController.php \
  backend/app/Http/Requests/ProfileUpdateRequest.php \
  backend/app/Models/User.php \
  backend/config/auth.php backend/config/session.php \
  backend/database/migrations/0001_01_01_00000*.php \
  backend/database/migrations/*_add_subscription_tier_to_users_table.php \
  backend/database/migrations/*_add_is_admin_to_users_table.php \
  backend/database/migrations/*_add_llm_api_key_to_users_table.php \
  backend/database/migrations/*_add_llm_provider_settings_to_users_table.php \
  backend/tests/Feature/Auth/ \
  backend/tests/Feature/ProfileTest.php \
  frontend/js/Pages/Dashboard.jsx
# A's SIEM pages. Check names with: ls frontend/js/Pages/Siem
# git add frontend/js/Pages/Siem/Agents* frontend/js/Pages/Siem/Events* frontend/js/Pages/Siem/Install*

git status
git commit --author="A Name <a@email.com>" -m "Add SIEM ingestion, findings and reports models, auth, dashboard and billing backend"
git push -u origin feature/siem-ingestion-auth
```

---

## Person B: SIEM detection, chat, admin, report and AI patch jobs, app core

**Backend**
- `SiemAlertController`, `SiemRuleController`, `SiemDashboardController`
- `RunSiemDetectionJob`, `GenerateSiemAIExplainJob`, `SiemDiagnoseCommand`
- Models: `SiemAlert`, `SiemAllowlist`, `SiemRuleSetting`
- `backend/app/Siem/` rules, detection and enrichment subfolders
- `config/siem.php`
- `ChatController`, `ChatService`, `Services/Contracts/`
- **Report and AI patch jobs:** `GenerateReportJob`, `GenerateAIPatchJob`
- App core: `EnsureUserIsAdmin`, `HandleInertiaRequests`, `Policies/`, `Providers/`
- Routes: `routes/admin.php`, `routes/web.php`
- Config: `app`
- Scaffolding: `artisan`, `bootstrap/`, `public/`, `storage/`
- Tests: `SiemRuleTest`, `SiemHealthTest`, `Feature/Admin/`
- Docs: `RECOVER_SIEM.md`

**Frontend**
- SIEM dashboard, alerts and rules pages in `frontend/js/Pages/Siem/`
- `ChatSidebar`, `Pages/Admin/`

```bash
git checkout main
git checkout -b feature/siem-detection-core

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
  backend/app/Jobs/GenerateReportJob.php \
  backend/app/Jobs/GenerateAIPatchJob.php \
  frontend/js/Components/ChatSidebar.jsx \
  frontend/js/Pages/Admin/ \
  backend/app/Http/Middleware/EnsureUserIsAdmin.php \
  backend/app/Http/Middleware/HandleInertiaRequests.php \
  backend/app/Policies/ backend/app/Providers/ \
  backend/routes/admin.php backend/routes/web.php \
  backend/config/app.php \
  backend/artisan backend/bootstrap/ backend/public/ backend/storage/ \
  backend/tests/Feature/Admin/
# B's SIEM backend subfolders and pages. Check names with: ls backend/app/Siem frontend/js/Pages/Siem
# git add backend/app/Siem/Rules/ backend/app/Siem/Detection/
# git add frontend/js/Pages/Siem/Dashboard* frontend/js/Pages/Siem/Alerts* frontend/js/Pages/Siem/Rules*

git status
git commit --author="B Name <b@email.com>" -m "Add SIEM detection, chat, admin, report and AI patch jobs and app core"
git push -u origin feature/siem-detection-core
```

---

## Person C: scanner engine, targets and scanning frontend (unchanged)

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

## Person D: frontend, build setup and light backend

**Light backend**
- **Uptime monitoring:** `UptimeController`, `CheckUptimeJob`, `UptimeService`, `UptimeLog` model, `uptime_logs` migration
- `Requests/Admin/`
- Config: `cache`, `filesystems`, `inertia`, `logging`, `mail`, `queue`
- `database/factories/`, `database/seeders/`, `database/.gitignore`
- `phpunit.xml`
- Tests: `ExampleTest`, `TestCase`

**Frontend**
- `Pages/Auth/`, `Pages/Profile/`, `Pages/Billing/`
- `Layouts/` (`AuthenticatedLayout`, `GuestLayout`)
- Shared components: `Checkbox`, `InputError`, `InputLabel`, `Modal`
- `frontend/css/app.css`

**Build and environment**
- `package.json`, `package-lock.json`, `vite.config.js`, `tailwind.config.js`, `postcss.config.js`
- `.env.example`, `.env.docker`, `resources/views/app.blade.php`, `start.sh`

```bash
git checkout main
git checkout -b feature/frontend-build

git add backend/app/Http/Controllers/UptimeController.php \
  backend/app/Jobs/CheckUptimeJob.php \
  backend/app/Services/UptimeService.php \
  backend/app/Models/UptimeLog.php \
  backend/database/migrations/*_create_uptime_logs_table.php \
  backend/app/Http/Requests/Admin/ \
  backend/config/cache.php backend/config/filesystems.php backend/config/inertia.php \
  backend/config/logging.php backend/config/mail.php backend/config/queue.php \
  backend/database/factories/ backend/database/seeders/ backend/database/.gitignore \
  backend/phpunit.xml \
  backend/tests/Feature/ExampleTest.php backend/tests/TestCase.php \
  frontend/js/Pages/Auth/ frontend/js/Pages/Profile/ frontend/js/Pages/Billing/ \
  frontend/js/Layouts/ \
  frontend/js/Components/Checkbox.jsx frontend/js/Components/InputError.jsx \
  frontend/js/Components/InputLabel.jsx frontend/js/Components/Modal.jsx \
  frontend/css/app.css \
  backend/package.json backend/package-lock.json backend/vite.config.js \
  backend/tailwind.config.js backend/postcss.config.js \
  backend/.env.example backend/.env.docker backend/resources/views/app.blade.php start.sh

git status
git commit --author="D Name <d@email.com>" -m "Add frontend, build setup, uptime and supporting backend"
git push -u origin feature/frontend-build
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
3. Merge in this order: **A and B, then C, then D**. If a PR shows a conflict, run `git checkout <branch> && git pull origin main` and resolve it.

## Notes

- Moved to A in this version: `DashboardController` and `Pages/Dashboard.jsx` (from B), `BillingController`, `ProfileUpdateRequest` and `ProfileTest` (from D). `Pages/Billing/` stays with D.
- Moved from A to B in the previous version: `GenerateReportJob` and `GenerateAIPatchJob` (2 files).
- Moved from A to D in the previous version: `UptimeController`, `CheckUptimeJob`, `UptimeService`, `UptimeLog` and the `uptime_logs` migration (5 files).
- Moved from A to D: `ProfileUpdateRequest`, `mail` config, factories, seeders, `ProfileTest`.
- Moved from B to D: `BillingController`, `Requests/Admin/`, the `cache`, `filesystems`, `inertia`, `logging` and `queue` configs, `phpunit.xml`, `database/.gitignore`, `ExampleTest`, `TestCase`.
- If `git add` says a path "did not match any files", that file has a different name than guessed. Run `ls` on that folder and fix the path.
- Run `ls backend/app/Siem frontend/js/Pages/Siem` and split those folders between A and B using the commented `git add` lines.
- Do not commit `backend/.env.docker` if it contains real keys.
- If you already ran the old commands, delete the old local branches first: `git checkout main && git branch -D <old-branch>`.

## Credit split (estimate)

Credit is based on difficulty and amount of work, not only file count.

| Person | Credit | Why |
|---|---|---|
| **A** | **26%** | SIEM ingestion pipeline (the hardest SIEM half), findings and reports models, auth, dashboard and billing backend and the project lead role |
| **B** | **26%** | SIEM detection, alerts and AI explain, chat, report and AI patch jobs, admin and app core |
| **C** | **26%** | Scanner engine with 10 tool wrappers, targets, scan runs and the scanning frontend |
| **D** | **22%** | Most frontend pages and layouts, build setup, uptime monitoring, billing and light backend |

Notes on credit:
- These are rough estimates. Adjust them if someone ends up doing noticeably more or less than planned.
- The `--author` lines in each commit give every person their own credit in `git log`, so your teacher can see who did what.
- Real credit also depends on tests, bug fixes and the final presentation. If someone helps others or fixes bugs, that should count too.

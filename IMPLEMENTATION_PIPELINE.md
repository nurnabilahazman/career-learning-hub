# Career & Learning Hub — Complete Implementation Pipeline

Owner: Nabilah. Last updated: 16 November 2026.

## 0. Scope and delivery truth

This repository currently contains a public static frontend gateway. It intentionally includes no private career evidence text, application records, passwords, employment documents or API secrets. The Google Sheets job tracker and Google Docs evidence bank remain authoritative and private.

**Stage 1 delivered:** HTTPS site, desktop/mobile navigation, ATS term scan for pasted job ads, copyable prompts for ChatGPT/Claude, checklist, links to the live Google resources.

**Not yet delivered:** private login, synchronized cloud database, Google Sheet read/write from site, automatic AI-powered matching, automated resume/PDF generation, secure attachment library, full online Learning Atlas integration. Those should never be represented as live without passing the test plan below.

### Grounded permanent assets
- Existing job tracker: https://docs.google.com/spreadsheets/d/13AzHerJsNeVkLoNNlolljButKMDNS-WcrKO5Dhg8IFI/edit
- Career Evidence Database: https://docs.google.com/document/d/1NegzHbs26tu6FH81Z61_im1c344aH3s_83smQ58kPVE/edit
- Master Guide: https://docs.google.com/document/d/1HAg74gvbQYX8l7IyF7mKuKb_IF8icAsLSRKldOt6AaU/edit
- Interactive Learning Atlas HTML: ChatGPT Library /Career and Learning Hub/Learning_Atlas_Interactive_Planner.html
- Code repository: https://github.com/nurnabilahazman/career-learning-hub
- Public portal: https://nurnabilahazman.github.io/career-learning-hub/

## 1. Detailed user stories and data ownership

**Career Evidence Bank** — Each achievement record: unique ID; employer/project; dates; title; business context; problem; scope; user's specific contribution; technologies; baseline metric and unit; result and unit; estimate/tested/produced/approved/deployed states; detailed challenge and fix; external/internal confidentiality; review date; approved resume-ready wording; interview story; ATS keyword tags; evidence/reference path; superseded evidence and correction history. Keep independent results separated from roles involving collaborators. Source: user-supplied 16 Nov 2026 GAS database with ~23 files/17k lines and previous historical snapshots.

**Job Tracker** — Unique role ID, employer, job URL, work location, official remote/hybrid designation, official posted salary separate from salary expectations, requirements, ATS extracted terms, match categories, priority, date captured, application submission truth, follow-ups, recruiter response, interviews, final disposition and document versions. Existing Google Sheet stays source of truth pending migration and explicit acceptance.

**Documents** — PDF and DOCX variants with role ID, created timestamp, content hash, template version, source evidence IDs, ATS keyword coverage, number of pages, font sizes, layout visual QA status, submission association, confidentiality class and original file paths.

**Learning Atlas** — Credential, official provider, exam window, costs and currencies, priority, study time, current completion, exam booking, version, courses, portfolio projects. Connect to ATS gaps by a many-to-many mapping.

**User Settings** — Black-and-white reference style; one-page A4; ATS first; justified body bullets; dense but legible; no invented ownership or qualifications; use remaining space for other verified achievements; plain English and employer-appropriate tone; default privacy private; no unauthorized submissions.

## 2. Security and data architecture BEFORE importing private records

- Adopt a private authenticated backend (e.g. Supabase Auth/Postgres with RLS, or equivalent established provider). Require unique login, allow MFA, and keep authorization checks server side.
- Use database tables: profiles; employers; roles; achievements; skills; achievement_skills; job_ads; job_requirements; requirement_evidence; applications; application_events; document_versions; interviews; interview_questions; learning_items; learning_progress; source_provenance; audit_events; sync_jobs.
- Every personal table keyed to owner_user_id; enforce RLS: authenticated user can read/write own records, anonymous role zero access. Verify this with unauthorized-client tests.
- Encrypt network connections and protected storage. Keep API keys in server-side environment vars, never frontend bundles, GitHub repo, or debug logs.
- Prepare backup/restore with regular exports, storage retention, explicit restore test and rollback plan. Track edits via append-only application events / audit records.
- Do not import employer documents, real finance data, sensitive spreadsheet IDs or proprietary source code into the public-facing app. Preserve generalized evidence only.
- Use Google OAuth only with required scopes and explicit user action. Do not put a service account key or Gmail access tokens in frontend code.
- Public site must display no personal career facts unless user explicitly approves publication. Private hub can show detailed facts after login.
- Add cross-device synchronization only once secure authentication and database write semantics are stable; offline drafts must not overwrite newer cloud data.

## 3. Data intake and reconciliation

1. Read current native job tracker, enumerate all tabs, header names, and validation. Export a readonly baseline and checksum.
2. Read the full Master Guide and Career Evidence Database, keeping dated source evidence and historical counts. Separately ingest 16 Nov Warner GAS 23-file/~17k-line update.
3. Define canonical achievement schema; mark test-only, shipped, feature-gated and planned. Flag unsupported ownership assertions. Keep latest and historical snapshots.
4. Migrate to private database with idempotent upserts keyed by stable IDs and checksums. Show a 1:1 row reconciliation report. Preserve original native Google Sheet as backup.
5. Select SINGLE source of truth for each domain (initially Google Sheet for job status and Google Doc for evidence); only switch after user verifies identical records and asks to migrate.
6. The application site should never fabricate salary, work arrangement or recruiter response from uncertain public listings.

## 4. ATS-first Resume Studio pipeline

1. Import pasted advertisement or verified job URL. For URLs, server fetch only allowed/public content and confirm official role, not arbitrary user-provided page code. Store original text and retrieval date.
2. Extract **all high-value ATS terms**: job title and synonyms, tasks, accounting standards, tools, platforms, qualifications, skills, deliverables, communication expectations, years of experience, country/work authorization, schedule.
3. Group/rank by mandatory vs preferred and importance. Compare each term against the user's verified achievement bank. Categories: strong verified direct experience; adjacent/partial transferable; unconfirmed/missing. Store source achievement IDs and confidence labels. No automatic invention.
4. Prioritize resume bullets with direct job matches first. Use exact terminology naturally without stuffing. Add metrics with honest unit and scope. Distinguish tested vs shipped vs deployed, supported tasks vs independent ownership.
5. **Mandatory fullness pass:** If meaningful space remains, add other distinct high-quality achievements from other roles, leadership, projects, certifications and relevant learning. Do not duplicate bullets or shrink font to unusable sizes. Preserve normal page margins.
6. Render one-page A4 PDF from editable DOCX template. Reference style: centered name and contact, BLACK section headings/horizontal rules, justified bullet bodies with normal final lines, aligned job titles/dates and hanging indents. Compare against the user's Infosys PDF.
7. Automated QA: A4 page count=1; not blank; no clipping; text extract retains all headings/contact and numbers; accurate factual statuses; font sized around 9.3–10 pt or readable equivalent; no huge unused page footer; links valid where allowed.
8. Visual QA: Render PDF to image and inspect visually; repair mismatched line wraps, excessive whitespace, font scaling or misalignment before finalizing.
9. Versioned outputs: PDF and DOCX linked to specific job ID, skills mapping and generated cover letter or short answers. User must be able to recover which exact files were submitted.
10. Explain remaining gaps, don't manufacture content. No ATS tool can guarantee an employer's screening score.

## 5. Application, status and interview pipeline

- A new ad creates a draft role; duplicate detection via normalized employer+title+official req ID or URL.
- Application stage can be updated to **Applied** only after user confirms submission. Failed portal is **Not applied** plus detailed reason.
- Follow-ups change only explicitly named fields and preserve other constraints. Track salary from employer separately from expected salary.
- When job is drafted, append 5–8 specific interview questions, each pointing to actual evidence or a missing-skill preparation note.
- When a status is updated, verify the existing native Google Sheet row and dashboard counts by reading back. Avoid silent sheet duplicates.
- Optionally create user-approved reminders for deadlines or anticipated recruiter follow-up, with explicit recurrence and cancel controls.

## 6. Learning Atlas pipeline

- Preserve original interactive HTML and its editable course/exam data. Audit for security and external data links before integration.
- Import study items with provider links, costs, currency, time, prerequisite, start/end and completion. Treat price and availability as dated estimates until official verification.
- Show skills repeatedly requested by employers and direct learning links to relevant exam/project; include evidence-building exercises, not just certificates.
- Support desktop/mobile, autosave, backup/export and optionally offline access. Cross-device syncing must use authenticated private storage.
- Do not claim an exam scheduled or passed unless user confirms it.

## 7. AI integration, ChatGPT and Claude Pro

- The current frontend only creates COPYABLE prompts. It does not call AI models.
- ChatGPT Plus and Claude Pro subscriptions are separate from developer API usage, which may incur charges. Do not assume either subscription enables a website backend.
- Future backend must support explicit choice of model/provider, opt-in upload of job text and private evidence, cost limits, no confidential employer data, output provenance, retry and error handling.
- AI suggestions require a human verification step before modifying any official record or generating a final submitted application.

## 8. Deployment and testing acceptance

- Source managed in git; private credentials never committed.
- GitHub Pages serves the current public shell via HTTPS. Validate 200 response, on mobile data, and that navigation/links work.
- For private phase, deploy a backend to Vercel/other secure host with environment secrets, MFA login and protected routes. Do not expose private database records on unauthenticated endpoints.
- Test matrix: Safari macOS; Chrome desktop; Safari iPhone/iPad; Android Chrome; at least 360px viewport; screen reader keyboard-only; no horizontal overflow; network interruption/retry; public vs private authorization; invalid data; empty job ad; hundreds of job rows; CSS/JS caching; linked Docs still require login.
- End-to-end test: paste an ad → extract ATS requirements → match verified evidence → generate reviewed one-page resume PDF/DOCX → save version → mark draft → user confirms applied → update existing tracker without duplicate → verify counts → retrieve in a fresh session on another device.
- Security test: anonymously query every private endpoint; attempt cross-user access, direct file URL access and forged application status; all must fail.
- Backup test: restore an isolated recent snapshot and compare counts/hashes.
- Only after all critical tests pass describe the complete private automated system as **deployed**. Until then, label capabilities accurately.

## 9. Current implementation state (16 Nov 2026)

- ✅ Public static hub with mobile navigation and links to current private resources.
- ✅ Browser-only ATS pattern scan from pasted ads, with explicit warning that it is not verified matching.
- ✅ Copyable prompts for AI assistants, checklist stored on this device.
- ✅ Career Evidence Database updated with full detailed 16 Nov Warner GAS evidence.
- ✅ Existing job tracker preserved.
- ⏳ Personal authentication, private synced storage, automatic job-tracker and AI integration, automatic resume generation and PDF layout QA, full Learning Atlas in-browser integration.
- The user's original full Learning Atlas remains in private ChatGPT Library; its contents have **not** been republished on GitHub Pages.

═══════════════════════════════════════════════════════════════════════════════
     📋 TEAM PROJECT GUIDE — 12 DAYS / 6 HOURS PER DAY / 6 MEMBERS
     ឯកសារណែនាំក្រុម — ១២ ថ្ងៃ / ៦ ម៉ោង/ថ្ងៃ / ៦ នាក់
═══════════════════════════════════════════════════════════════════════════════

Project: Student-Mentor Platform
Duration: 12 Days (6 hours/day)
Team: 6 Members (HUN RIN PHY HEING NANG RATHANAK)
Prepared by: PHE SOPHY Team EGENZ
Date: May 24, 2026

═══════════════════════════════════════════════════════════════════════════════
👥 រចនាសម្ព័ន្ធក្រុម / TEAM STRUCTURE
═══════════════════════════════════════════════════════════════════════════════

┌────────┬────────────────────────────┬─────────────────────────────────────────┐
│ Member │ Role                       │ Main Responsibility                     │
├────────┼────────────────────────────┼─────────────────────────────────────────┤
│ BE1    │ Backend Lead + Database    │ Schema design, tables, indexes,         │
│        │                            │ DB optimization                         │
├────────┼────────────────────────────┼─────────────────────────────────────────┤
│ BE2    │ Backend API + Auth         │ Auth routes, JWT, bcrypt, role check,   │
│        │                            │ API endpoints                           │
├────────┼────────────────────────────┼─────────────────────────────────────────┤
│ BE3    │ Backend QA + DevOps        │ Testing, CI/CD, deployment, docs,        │
│        │                            │ integration tests                       │
├────────┼────────────────────────────┼─────────────────────────────────────────┤
│ FE1    │ Frontend UI + Pages        │ React pages, components, layouts,       │
│        │                            │ styling, animations                     │
├────────┼────────────────────────────┼─────────────────────────────────────────┤
│ FE2    │ Frontend API + Integration │ Axios, API connection, state management,  │
│        │                            │ form handling                           │
├────────┼────────────────────────────┼─────────────────────────────────────────┤
│ FE3    │ Frontend QA + Polish       │ Responsive, error handling, responsive,   │
│        │                            │ UI polish, mobile check                 │
└────────┴────────────────────────────┴─────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════════════════
📅 ផែនការសង្ខេបតាមថ្ងៃ / DAY-BY-DAY SUMMARY
═══════════════════════════════════════════════════════════════════════════════

DAY 1 — AUTH FOUNDATION (ថ្ងៃទី ១ — គ្រឹះ Auth)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Plan Auth First
   → PRIORITY: 🔴 HIGH | Auth is priority for client

⏰ 08:30 - 09:00 | SETUP | PARALLEL (ធ្វើជាមួយគ្នាបាន)
   → BE1: Setup PostgreSQL + Create database → Port 5432
   → BE2: Setup Express server (server.js) → Port 5000
   → BE3: Setup GitHub repo + Git flow
   → FE1: Setup React + Vite + Tailwind
   → FE2: Setup Axios + folder structure
   → FE3: Setup React Router + layout

⏰ 09:00 - 10:30 | AUTH CORE | PARALLEL
   → BE1: Schema — Create users table (id, email, password_hash, role)
   → BE2: middleware/auth.js — Hash password (bcrypt, salt rounds 10)
   → BE3: middleware/validate.js — Email + password validation
   → FE1: Login page UI (form, button, styling)
   → FE2: Register page UI (form, button, styling)
   → FE3: Auth layout (centered card, background)

⏰ 10:30 - 12:00 | AUTH ADVANCED | PARALLEL
   → BE1: Schema — Create user_type table
   → BE2: middleware/auth.js — Compare password (bcrypt)
   → BE3: middleware/auth.js — Generate token (JWT, expires 24h)
   → FE1: Connect Login UI to mock API
   → FE2: Connect Register UI to mock API
   → FE3: Error handling UI (error messages, loading)

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 14:30 | AUTH LINK | DEPENDENT (ត្រូវរង់ចាំ)
   → BE1: Schema — Create student table (link to users)
   → BE2: Auth Routes — POST /api/auth/register
   → BE3: middleware/auth.js — Verify token (JWT)
   → FE1: Dashboard layout (after login)
   → FE2: Add Token — Axios interceptor (save to localStorage)
   → FE3: Handle CORS — Prepare config

⏰ 14:30 - 16:00 | AUTH REAL API | DEPENDENT
   → BE1: Schema — Create mentor table (link to users)
   → BE2: Auth Routes — POST /api/auth/login
   → BE3: middleware/auth.js — Check role (admin, student, mentor)
   → FE1: Connect Login to REAL API (test with BE2) ← DEPENDS ON BE2
   → FE2: Connect Register to REAL API (test with BE2) ← DEPENDS ON BE2
   → FE3: Protected routes (need token to access) ← DEPENDS ON BE3

⏰ 16:00 - 17:30 | AUTH FINAL | PARALLEL
   → BE1: Schema — Add indexes, constraints
   → BE2: User Routes — GET /api/users/me (current user)
   → BE3: Unit Test — Auth functions (hash, compare, token)
   → FE1: Connect /api/users/me to show user info ← DEPENDS ON BE2
   → FE2: Error Handling — Global error handler (401, 403, 500)
   → FE3: Logout functionality (clear token, redirect)

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Review Day 1
   → TARGET: Auth 80% complete

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 2 — AUTH COMPLETE + CLIENT CHECK (ថ្ងៃទី ២ — Auth ចប់ + ពិនិត្យ Client)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Auth Complete Today
   → PRIORITY: 🔴 HIGH | Client check at 1 PM

⏰ 08:30 - 10:00 | AUTH POLISH | PARALLEL
   → BE1: Schema — Create skill, province, community_type tables
   → BE2: Auth Routes — Add password reset (optional)
   → BE3: Integration Test — Auth flow (register → login → dashboard)
   → FE1: Polish Login page (animations, validation feedback)
   → FE2: Polish Register page (password strength, email validation)
   → FE3: Polish Dashboard (sidebar, navigation, user menu)

⏰ 10:00 - 12:00 | AUTH ROLE + API TEST | PARALLEL
   → BE1: Schema — Create community_post, mentor_post tables
   → BE2: User Routes — PUT /api/users/:id (update profile)
   → BE3: API Test — All auth endpoints (register, login, me)
   → FE1: Role-based dashboard (admin/student/mentor views)
   → FE2: Connect Update profile to API
   → FE3: Responsive check (mobile login/register)

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 14:00 | CLIENT CHECK | ALL
   → CLIENT CHECK — Demo Auth to client
   → SHOW: Register → Login → Dashboard
   → PRIORITY: 🔴 HIGH | Client feedback received

⏰ 14:00 - 15:00 | FIX FEEDBACK | ALL
   → Fix Client feedback (if any)
   → PRIORITY: 🔴 HIGH | Feedback must be fixed immediately

⏰ 15:00 - 16:00 | CORE START | PARALLEL
   → BE1: Schema — Create subscription_plan, subscription tables
   → BE2: Task Routes — GET/POST /api/tasks
   → BE3: Unit Test — Validation functions
   → FE1: Task List page (mock data)
   → FE2: Task Form page (create task)
   → FE3: Bug List page (mock data)

⏰ 16:00 - 17:30 | CORE API | PARALLEL
   → BE1: Schema — Create transaction_detail, banking_payment tables
   → BE2: Bug Routes — GET/POST /api/bugs
   → BE3: Integration Test — Task CRUD flow
   → FE1: Connect Task List to real API ← DEPENDS ON BE2
   → FE2: Connect Task Create to real API ← DEPENDS ON BE2
   → FE3: Connect Bug List to real API ← DEPENDS ON BE2

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Auth 100% Complete! Client checked!
   → ✅ AUTH IS DONE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 3 — CORE FEATURES (ថ្ងៃទី ៣ — លក្ខណៈសំខាន់)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Core Features Day

⏰ 08:30 - 12:00 | MENTOR/STUDENT/COMMUNITY | PARALLEL
   → BE1: Schema — mentor_portfolio, mentor_skill, community_history
   → BE2: Mentor Routes — GET/POST /api/mentors
   → BE3: Student Routes — GET/POST /api/students
   → FE1: Mentor List page + Mentor Profile page
   → FE2: Student Profile page
   → FE3: Community Feed page + Create Post page

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 17:30 | COMMUNITY/SUBSCRIPTION | PARALLEL
   → BE1: Schema — account_history, account_history_log
   → BE2: Community Routes — GET/POST /api/community-posts
   → BE3: Subscription Routes — GET/POST /api/subscriptions
   → FE1: Connect Mentor to real API ← DEPENDS ON BE2
   → FE2: Connect Student to real API ← DEPENDS ON BE3
   → FE3: Connect Community to real API ← DEPENDS ON BE2

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Core Features Progress

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 4 — COMPLETE CORE (ថ្ងៃទី ៤ — ចប់ Core)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Complete Core

⏰ 08:30 - 12:00 | TRANSACTION/BANKING | PARALLEL
   → BE1: Add foreign keys, indexes, constraints
   → BE2: Transaction Routes — GET/POST /api/transactions
   → BE3: Banking Routes — GET/POST /api/banking-payments
   → FE1: Subscription Plans page
   → FE2: Transaction History page
   → FE3: Admin Dashboard page

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 17:30 | OPTIMIZATION + CONNECT | PARALLEL
   → BE1: Database optimization (indexes, query optimization)
   → BE2: Add pagination, search, filter to all routes
   → BE3: API Test — All endpoints
   → FE1: Connect Subscription to real API ← DEPENDS ON BE3
   → FE2: Connect Transaction to real API ← DEPENDS ON BE2
   → FE3: Connect Admin to real API ← DEPENDS ON BE2

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Core 100% Complete
   → ✅ CORE IS DONE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 5 — POLISH + DEPLOY (ថ្ងៃទី ៥ — ធ្វើឲស្អាត + ដាក់ឡើង)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Polish + Deploy

⏰ 08:30 - 12:00 | BACKEND POLISH | PARALLEL
   → BE1: Handle CORS — Final config
   → BE2: Error handling standardization
   → BE3: Auto Test (CI/CD) — GitHub Actions
   → FE1: UI animations + transitions
   → FE2: Responsive design (mobile, tablet)
   → FE3: Dark mode + theme switcher

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 17:30 | DEPLOY | ALL
   → BE1: Security audit (SQL injection, XSS)
   → BE2: Performance optimization
   → BE3: Deploy backend to Render
   → FE1: Deploy frontend to Vercel
   → FE2: Production test (smoke test)
   → FE3: Final responsive check

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Deployed! Production is LIVE!
   → 🚀 PRODUCTION LIVE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 6 — FINAL POLISH (ថ្ងៃទី ៦ — ធ្វើឲចប់សព្វគ្រប់)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Final Polish

⏰ 08:30 - 12:00 | DOCS + UX | PARALLEL
   → BE1: Database backup strategy
   → BE2: API documentation (Postman collection)
   → BE3: Integration Test — Full flows
   → FE1: Loading states + skeleton screens
   → FE2: Toast notifications (success/error)
   → FE3: Form validation feedback

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 17:30 | SECURITY + E2E | PARALLEL
   → BE1: Rate limiting + security headers
   → BE2: Review all API docs
   → BE3: E2E Test setup (Playwright)
   → FE1: Error boundaries + fallback UI
   → FE2: Optimize images + lazy loading
   → FE3: Final UI polish

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Polish Complete
   → ✅ READY FOR TEST

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 7 — BUG HUNT DAY (ថ្ងៃទី ៧ — រក Bug)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Bug Hunt Day

⏰ 08:30 - 12:00 | MORNING BUG HUNT | PAIRS (ធ្វើជាគូ)
   → BE1 + FE1: Bug Hunt — Test Auth flow end-to-end
   → BE2 + FE2: Bug Hunt — Test Task CRUD end-to-end
   → BE3 + FE3: Bug Hunt — Test Bug CRUD end-to-end

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 17:30 | AFTERNOON BUG HUNT | PAIRS
   → BE1 + FE1: Bug Hunt — Test Mentor/Student end-to-end
   → BE2 + FE2: Bug Hunt — Test Community end-to-end
   → BE3 + FE3: Bug Hunt — Test Subscription end-to-end

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Bug List Created
   → 🐛 BUG LIST READY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 8 — FIX BUGS (ថ្ងៃទី ៨ — ជួសជុល Bug)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Fix Bugs

⏰ 08:30 - 12:00 | FIX MORNING | PAIRS
   → BE1 + FE1: Fix Code — Backend + Frontend bugs
   → BE2 + FE2: Fix Code — Backend + Frontend bugs
   → BE3 + FE3: Fix Code — Backend + Frontend bugs

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 15:00 | TEST FIX | ALL
   → Test Fix — Verify all fixes

⏰ 15:00 - 16:00 | DEPLOY | BE3
   → Deploy — Update production

⏰ 16:00 - 17:30 | FINAL INTEGRATION | ALL
   → Final integration test

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Bugs Fixed
   → ✅ BUGS FIXED

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 9 — FULL TEST (ថ្ងៃទី ៩ — ធ្វើតេស្តពេញលេញ)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Full Test

⏰ 08:30 - 12:00 | MORNING TESTS | BE3 LEADS
   → BE3: Unit Test — Run all unit tests
   → BE3: Integration Test — Run all integration tests
   → BE3: API Test — Run all API tests

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 17:30 | AFTERNOON TESTS | ALL
   → BE3: E2E Test — Run all E2E tests
   → BE3: Load Test — Test 100 concurrent users
   → ALL: Security Test — SQL injection, XSS, CSRF

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Test Report Ready
   → 📊 TEST REPORT READY

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 10 — HOT FIX (ថ្ងៃទី ១០ — ជួសជុលបន្ទាន់)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Hot Fix

⏰ 08:30 - 09:00 | DISCOVER | ALL
   → Discover Bug — Review test report
   → Bug list finalized

⏰ 09:00 - 10:00 | BRANCH | BE3
   → Create Branch — hotfix/critical-bugs
   → Branches created

⏰ 10:00 - 12:00 | FIX | PAIRS
   → BE1 + BE2: Fix Code — Backend critical bugs
   → FE1 + FE2 + FE3: Fix Code — Frontend critical bugs

⏰ 12:00 - 13:00 | LUNCH BREAK | ALL

⏰ 13:00 - 15:00 | VERIFY | BE3 + FE2
   → Test Fix — Verify all critical fixes

⏰ 15:00 - 16:00 | DEPLOY | BE3
   → Deploy — Update production with fixes

⏰ 16:00 - 17:30 | SMOKE | ALL
   → Final smoke test

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Hot Fix Complete
   → 🔥 HOT FIX DONE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 11 — DEMO PREP (ថ្ងៃទី ១១ — ត្រៀម Demo)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | SCRUM | ALL
   → Daily Standup — Demo Prep

⏰ 08:30 - 09:30 | PREP | BE3 + BE1 + BE2
   → BE3: Prepare Script — Write demo script (5 min)
   → BE1 + BE2: Setup Demo Data — Seed realistic data

⏰ 09:30 - 10:30 | REHEARSAL 1 | ALL
   → Rehearsal 1 — First run-through
   → Issues found

⏰ 10:30 - 11:30 | FIX 1 | ALL
   → Fix issues from Rehearsal 1

⏰ 11:30 - 12:30 | REHEARSAL 2 | ALL
   → Rehearsal 2 — Second run-through
   → Smooth

⏰ 12:30 - 13:30 | LUNCH BREAK | ALL

⏰ 13:30 - 14:30 | FIX 2 | ALL
   → Fix issues from Rehearsal 2

⏰ 14:30 - 15:30 | REHEARSAL 3 | ALL
   → Rehearsal 3 — Final with timer
   → Confident

⏰ 15:30 - 17:30 | BACKUP | ALL
   → Backup plan — Screenshots + Video

⏰ 17:30 - 18:00 | SCRUM | ALL
   → Daily Retro — Demo Prep Complete
   → 🎬 READY FOR DEMO

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DAY 12 — DEMO DAY (ថ្ងៃទី ១២ — ថ្ងៃ Demo)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏰ 08:00 - 08:30 | FINAL CHECK | ALL
   → Final check production + demo data

⏰ 08:30 - 09:00 | SMOKE | ALL
   → Final smoke test

⏰ 09:00 - 10:00 | TEAM MEETING | ALL
   → Team meeting — Final instructions

⏰ 10:00 - 12:00 | REST | ALL
   → Rest + Prepare

⏰ 12:00 - 13:00 | LUNCH | ALL

⏰ 13:00 - 14:00 | 🎉 LIVE DEMO | ALL
   → LIVE DEMO (5 minutes)
   → Demo delivered!

⏰ 14:00 - 15:00 | Q&A | ALL
   → Q&A — Answer questions
   → Questions answered

⏰ 15:00 - 17:00 | 🎊 CELEBRATION | ALL
   → Celebrate + Document lessons learned
   → SUCCESS!

═══════════════════════════════════════════════════════════════════════════════
🔗 ការងារធ្វើជាមួយគ្នា និង ត្រូវរង់ចាំ / PARALLEL VS DEPENDENT
═══════════════════════════════════════════════════════════════════════════════

✅ ធ្វើជាមួយគ្នាបាន (Parallel — មិនត្រូវរង់ចាំ):
   • Day 1 morning: BE setup DB + FE setup React (ធ្វើជាមួយគ្នា)
   • Day 1 morning: BE schema + FE UI pages (ធ្វើជាមួយគ្នា)
   • Day 1 morning: BE middleware + FE layout (ធ្វើជាមួយគ្នា)
   • Day 2 morning: BE tables + FE polish (ធ្វើជាមួយគ្នា)
   • Day 3-4: BE routes + FE pages (ធ្វើជាមួយគ្នា — តែ FE ប្រើ mock data)

⛔ ត្រូវរង់ចាំ (Dependent — ធ្វើមិនបានទេ):
   • FE connect REAL API → ត្រូវរង់ចាំ BE API រួចសិន
   • Protected routes → ត្រូវរង់ចាំ Auth middleware រួចសិន
   • Role-based dashboard → ត្រូវរង់ចាំ Role check រួចសិន
   • Integration test → ត្រូវរង់ចាំ BE + FE រួចសិន
   • Deploy → ត្រូវរង់ចាំ All features working រួចសិន

💡 STRATEGY: ធ្វើការងារដែលធ្វើជាមួយគ្នាបានជាមុន (parallel) ហើយរក្សាទុកការងារដែលត្រូវរង់ចាំសម្រាប់ពេលក្រោយ។
   ប្រើ mock data សម្រាប់ FE ពេល BE មិនទាន់រួច។

═══════════════════════════════════════════════════════════════════════════════
📋 បញ្ជីត្រួតពិនិត្យចុងក្រោយ / FINAL CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

BEFORE CLIENT DEMO (មុន Demo):
  ☐ Auth flow: Register → Login → Dashboard (smooth)
  ☐ Role-based views working (admin/student/mentor)
  ☐ Task CRUD working
  ☐ Bug CRUD working
  ☐ Mentor/Student profiles working
  ☐ Community feed working
  ☐ Subscription plans visible
  ☐ Transaction history visible
  ☐ Admin dashboard visible
  ☐ Mobile responsive working
  ☐ Dark mode working (if time)
  ☐ No console errors
  ☐ No API 500 errors
  ☐ Production stable
  ☐ Demo data realistic
  ☐ Demo script ready (5 min)
  ☐ Rehearsal 3 done
  ☐ Backup screenshots ready

═══════════════════════════════════════════════════════════════════════════════
🚨 ច្បាប់មាស / GOLDEN RULES
═══════════════════════════════════════════════════════════════════════════════

  1. ធ្វើតាម timeline — កុំយឺត
  2. Commit រៀងរាល់ ២-៣ ម៉ោង
  3. សួរសំណួរពេល block — កុំអង្គុយរង់ចាំ
  4. Code review មុន merge
  5. ប្រើ branch feature/xxx — កុំ push ទៅ main
  6. .env មិន commit ទៅ Git
  7. Standup + Retro រៀងរាល់ថ្ងៃ
  8. ជួយគ្នា — ក្រុមជោគជ័យ = គម្រោងជោគជ័យ
  9. ប្រើ mock data ពេល BE មិនទាន់រួច
  10. ធ្វើ test មុនថារួច — កុំទុកឲ QA រក

═══════════════════════════════════════════════════════════════════════════════
🎯 គោលដៅចុងក្រោយ / FINAL GOAL
═══════════════════════════════════════════════════════════════════════════════

បង្ហាញ demo ដ៏អស្ចារ្យឲ client នៅថ្ងៃទី ១២ ម៉ោង ១៣:០០។
គម្រោងជោគជ័យ = ក្រុមជោគជ័យ 

═══════════════════════════════════════════════════════════════════════════════
Document prepared by PHE SOPHY Team EGENZ
Last updated: May 24, 2026
═══════════════════════════════════════════════════════════════════════════════

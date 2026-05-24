# TEAM-ROLE-HANDLE
This Repo make For Aleart Each Member For Role and Position Read it Work Fast
# 🎓 Student Mentor Platform

&gt; A full-stack web application connecting students with mentors for personalized learning.

## 📋 Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Team](#team)
- [Getting Started](#getting-started)
- [API Documentation](#api-documentation)
- [Deployment](#deployment)

## ✨ Features

- 🔐 Authentication (JWT-based)
- 👨‍🏫 Mentor profiles & booking
- 🎓 Student progress tracking
- 💬 Community feed
- 📊 Admin dashboard
- 💳 Subscription plans

## 🛠 Tech Stack

### Frontend
| Technology | Purpose |
|------------|---------|
| React 18 | UI Framework |
| Vite | Build Tool |
| Tailwind CSS | Styling |
| Axios | HTTP Client |
| React Router | Navigation |

### Backend
| Technology | Purpose |
|------------|---------|
| Express.js | API Server |
| PostgreSQL | Database |
| JWT | Authentication |
| bcryptjs | Password Hashing |

## 👥 Team

| Name | Role | Tasks |
|------|------|-------|
| **HUN** | Frontend Lead | React setup, Login UI, Dashboard |
| **RIN** | API Integration | Axios, Register, Forms |
| **PHY** | Router & Layout | React Router, Auth layout, Protected routes |
| **Rathanak** | Database Lead | PostgreSQL, Schema, Tables |
| **Heing** | API Routes | Express, Auth routes, Task/Bug/Mentor APIs |
| **Nang** | QA & DevOps | Git, Validation, JWT, Testing, CI/CD |

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- PostgreSQL 15+
- Git

### Installation

1. Clone the repository
```bash
git clone https://github.com/your-org/project.git
cd project
Setup Frontend
cd backend
npm install
cp .env.example .env
# Edit .env with your database credentials
npm run dev
Setup Backend
cd frontend
npm install
cp .env.example .env
npm run dev
Setup Database
cd backend
psql -U postgres -f database/schema.sql
API DOCUMENTATION
Authentication
| Method | Endpoint             | Description      |
| ------ | -------------------- | ---------------- |
| POST   | `/api/auth/register` | Create account   |
| POST   | `/api/auth/login`    | Login            |
| GET    | `/api/users/me`      | Get current user |
Tasks
| Method | Endpoint     | Description |
| ------ | ------------ | ----------- |
| GET    | `/api/tasks` | List tasks  |
| POST   | `/api/tasks` | Create task |
🌐 Deployment
Backend (Render)
Push code to GitHub
Connect repo to Render
Add environment variables
Deploy
Frontend (Vercel)
Push code to GitHub
Connect repo to Vercel
Add VITE_API_BASE_URL environment variable
Deploy
📝 License
MIT License - see LICENSE file
🙏 Acknowledgments
Team EGENZ

═══════════════════════════════════════════════════════════════════════════════
🎨 STEP 4: ADVANCED GITHUB MARKDOWN FEATURES
═══════════════════════════════════════════════════════════════════════════════

1. EMOJIS (អ៊ីមូជី)
────────────────────
:rocket: → 🚀
:fire: → 🔥
:warning: → ⚠️
:white_check_mark: → ✅
:x: → ❌

Full list: https://github.com/ikatyang/emoji-cheat-sheet

2. COLLAPSIBLE SECTIONS (ផ្នែកដែលអាចបង្ហាញ/លាក់)
───────────────────────────────────────────────────
<details>
<summary>Click to expand</summary>

Hidden content here...
More hidden content...

</details>

3. TASK LISTS WITH ISSUE LINKS
──────────────────────────────
- [x] #1 Setup project
- [x] #2 Create database
- [ ] #3 Deploy to production

4. MENTION PEOPLE
─────────────────
@username → Mention someone
@organization/team → Mention team

5. REFERENCE ISSUES/PRs
───────────────────────
#123 → Issue/PR #123
GH-123 → Same as above

═══════════════════════════════════════════════════════════════════════════════
🔧 STEP 5: GITHUB SPECIAL FILES
═══════════════════════════════════════════════════════════════════════════════

Create these files in your repo root:

1. README.md          ← Main project documentation
2. LICENSE            ← License file (MIT, Apache, etc.)
3. CONTRIBUTING.md    ← How to contribute
4. CODE_OF_CONDUCT.md ← Community guidelines
5. .github/
   ├── ISSUE_TEMPLATE/
   │   ├── bug_report.md
   │   └── feature_request.md
   └── PULL_REQUEST_TEMPLATE.md

═══════════════════════════════════════════════════════════════════════════════
📤 STEP 6: PUSH TO GITHUB AND VERIFY
═══════════════════════════════════════════════════════════════════════════════

1. Save your README.md file
2. Add to git:
   git add README.md
   git commit -m "docs: Add comprehensive README"
   git push origin main

3. Go to GitHub repository page
4. You will see README.md rendered beautifully!

═══════════════════════════════════════════════════════════════════════════════
💡 QUICK TIPS
═══════════════════════════════════════════════════════════════════════════════

✅ DO:
  • Use clear headings (# ## ###)
  • Add table of contents for long docs
  • Use code blocks with language tag
  • Add screenshots/images
  • Use tables for structured data
  • Add emojis for visual appeal

❌ DON'T:
  • Make README too long (keep under 100 lines if possible)
  • Forget to update when project changes
  • Use too many colors (GitHub ignores HTML colors)
  • Forget to add .md extension

═══════════════════════════════════════════════════════════════════════════════
🎯 PRACTICE EXERCISE FOR YOUR TEAM
═══════════════════════════════════════════════════════════════════════════════

Create these files in your project:

1. /README.md (main project doc)
2. /backend/README.md (backend specific)
3. /frontend/README.md (frontend specific)
4. /docs/API.md (API documentation)
5. /docs/DEPLOYMENT.md (deployment guide)
6. /docs/TEAM.md (team member tasks)

Example: docs/TEAM.md
```markdown
# 👥 Team Members & Tasks

## Frontend Team

### HUN
**Day 1-3 Tasks:**
- [x] Setup React + Vite + Tailwind
- [x] Login page UI
- [x] Dashboard layout
- [ ] Connect to real API

**Day 4-6 Tasks:**
- [ ] DB optimization
- [ ] CORS configuration
- [ ] Security audit

### RIN
**Day 1-3 Tasks:**
- [x] Axios setup
- [x] Register page UI
- [ ] API integration

### PHY
**Day 1-3 Tasks:**
- [x] React Router setup
- [x] Auth layout
- [ ] Protected routes

## Backend Team

### Rathanak
**Day 1-3 Tasks:**
- [x] PostgreSQL setup
- [x] All 18 tables created
- [ ] Indexes added

### Heing
**Day 1-3 Tasks:**
- [x] Express server
- [x] Auth routes
- [ ] Task/Bug routes

### Nang
**Day 1-3 Tasks:**
- [x] Git setup
- [x] Validation middleware
- [ ] Unit tests

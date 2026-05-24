═══════════════════════════════════════════════════════════════════════════════
     📄 NANG — BACKEND DEVELOPER (Git + Validation + JWT + Testing + QA)
═══════════════════════════════════════════════════════════════════════════════

Role: Backend Developer — QA + DevOps Lead
Team: BACKEND
Tasks: Git setup, Validation middleware, JWT functions, Role check, Unit tests, Integration tests, E2E setup

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 1 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Setup GitHub repo + Git flow (08:30-09:00)
───────────────────────────────────────────────────
# Create repo on GitHub
# Clone locally
git init
git remote add origin https://github.com/your-org/project.git

# Create branches
git checkout -b main
git checkout -b develop

# Git flow rules:
# Feature branches: feature/auth, feature/tasks
# Hotfix branches: hotfix/critical
# Never push directly to main

# .gitignore
node_modules/
.env
*.log
dist/
coverage/

TASK 2: middleware/validate.js — Email + password validation (09:00-10:30)
─────────────────────────────────────────────────────────────────────────
// middleware/validate.js
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
const passwordRegex = /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d@$!%*#?&]{6,}$/;

const validateRegister = (req, res, next) => {
    const { email, password, full_name, user_type_id } = req.body;
    
    if (!email || !password) {
        return res.status(400).json({ success: false, message: 'Email and password required' });
    }
    
    if (!emailRegex.test(email)) {
        return res.status(400).json({ success: false, message: 'Invalid email format' });
    }
    
    if (!passwordRegex.test(password)) {
        return res.status(400).json({ 
            success: false, 
            message: 'Password must be 6+ chars with letter and number' 
        });
    }
    
    if (user_type_id && ![1, 2, 3].includes(parseInt(user_type_id))) {
        return res.status(400).json({ success: false, message: 'Invalid user type' });
    }
    
    next();
};

const validateLogin = (req, res, next) => {
    const { email, password } = req.body;
    
    if (!email || !password) {
        return res.status(400).json({ success: false, message: 'Email and password required' });
    }
    
    next();
};

module.exports = { validateRegister, validateLogin };

TASK 3: middleware/auth.js — Generate token (JWT) (10:30-12:00)
──────────────────────────────────────────────────────────────
const jwt = require('jsonwebtoken');

const generateToken = (userId, email, userTypeId) => {
    return jwt.sign(
        { id: userId, email, user_type_id: userTypeId },
        process.env.JWT_SECRET,
        { expiresIn: process.env.JWT_EXPIRES_IN || '7d' }
    );
};

TASK 4: middleware/auth.js — Verify token (JWT) (13:00-14:30)
─────────────────────────────────────────────────────────────
const verifyToken = (req, res, next) => {
    try {
        const authHeader = req.headers.authorization;
        
        if (!authHeader || !authHeader.startsWith('Bearer ')) {
            return res.status(401).json({ success: false, message: 'No token provided' });
        }

        const token = authHeader.split(' ')[1];
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        
        req.user = decoded;
        next();
    } catch (error) {
        if (error.name === 'TokenExpiredError') {
            return res.status(401).json({ success: false, message: 'Token expired' });
        }
        return res.status(401).json({ success: false, message: 'Invalid token' });
    }
};

TASK 5: middleware/auth.js — Check role (admin, student, mentor) (14:30-16:00)
──────────────────────────────────────────────────────────────────────────────
const checkRole = (allowedRoles) => {
    return (req, res, next) => {
        if (!req.user) {
            return res.status(401).json({ success: false, message: 'Unauthorized' });
        }

        const roleMap = { admin: 1, student: 2, mentor: 3 };
        const allowedIds = allowedRoles.map(r => roleMap[r] || r);
        
        if (!allowedIds.includes(req.user.user_type_id)) {
            return res.status(403).json({ success: false, message: 'Forbidden: insufficient role' });
        }
        
        next();
    };
};

// Export all
module.exports = {
    hashPassword: require('./auth').hashPassword,
    comparePassword: require('./auth').comparePassword,
    generateToken,
    verifyToken,
    checkRole
};

TASK 6: Unit Test — Auth functions (16:00-17:30)
────────────────────────────────────────────────
// tests/unit/auth.test.js
const { hashPassword, comparePassword, generateToken, verifyToken } = require('../../middleware/auth');

describe('Auth Functions', () => {
    test('hashPassword returns hashed string', async () => {
        const hashed = await hashPassword('password123');
        expect(hashed).toBeDefined();
        expect(hashed).not.toBe('password123');
    });

    test('comparePassword true for correct password', async () => {
        const hashed = await hashPassword('password123');
        const isMatch = await comparePassword('password123', hashed);
        expect(isMatch).toBe(true);
    });

    test('comparePassword false for wrong password', async () => {
        const hashed = await hashPassword('password123');
        const isMatch = await comparePassword('wrong', hashed);
        expect(isMatch).toBe(false);
    });

    test('generateToken returns valid JWT', () => {
        const token = generateToken(1, 'test@test.com', 2);
        expect(typeof token).toBe('string');
    });
});

// package.json scripts
{
    "test": "jest",
    "test:watch": "jest --watch"
}

// jest.config.js
module.exports = {
    testEnvironment: 'node',
    setupFilesAfterEnv: ['<<rootDir>/tests/setup.js']
};

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 2 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Integration Test — Auth flow (08:30-10:00)
──────────────────────────────────────────────────
// tests/integration/auth.flow.test.js
const request = require('supertest');
const app = require('../../server');

describe('Auth Flow', () => {
    const testEmail = `test_${Date.now()}@example.com`;
    let token;

    test('Register → Login → Get Profile', async () => {
        // Register
        const registerRes = await request(app)
            .post('/api/auth/register')
            .send({
                email: testEmail,
                password: 'password123',
                full_name: 'Test User',
                user_type_id: 2
            });
        
        expect(registerRes.status).toBe(201);
        expect(registerRes.body.token).toBeDefined();
        token = registerRes.body.token;

        // Login
        const loginRes = await request(app)
            .post('/api/auth/login')
            .send({ email: testEmail, password: 'password123' });
        
        expect(loginRes.status).toBe(200);
        expect(loginRes.body.token).toBeDefined();

        // Get Profile
        const profileRes = await request(app)
            .get('/api/users/me')
            .set('Authorization', `Bearer ${token}`);
        
        expect(profileRes.status).toBe(200);
        expect(profileRes.body.user.email).toBe(testEmail);
    });
});

TASK 2: API Test — All auth endpoints (10:00-12:00)
───────────────────────────────────────────────────
// tests/api/auth.api.test.js
describe('Auth Endpoints', () => {
    test('POST /api/auth/register - validation error', async () => {
        const res = await request(app)
            .post('/api/auth/register')
            .send({ email: 'invalid', password: '123' });
        
        expect(res.status).toBe(400);
    });

    test('POST /api/auth/login - wrong password', async () => {
        const res = await request(app)
            .post('/api/auth/login')
            .send({ email: 'test@test.com', password: 'wrong' });
        
        expect(res.status).toBe(401);
    });

    test('GET /api/users/me - no token', async () => {
        const res = await request(app).get('/api/users/me');
        expect(res.status).toBe(401);
    });
});

TASK 3: Unit Test — Validation functions (13:00-14:30)
──────────────────────────────────────────────────────
// tests/unit/validate.test.js
const { validateRegister, validateLogin } = require('../../middleware/validate');

describe('Validation', () => {
    let req, res, next;

    beforeEach(() => {
        req = { body: {} };
        res = { status: jest.fn().mockReturnThis(), json: jest.fn() };
        next = jest.fn();
    });

    test('validateRegister rejects invalid email', () => {
        req.body = { email: 'invalid', password: 'password123' };
        validateRegister(req, res, next);
        expect(res.status).toHaveBeenCalledWith(400);
    });

    test('validateRegister rejects weak password', () => {
        req.body = { email: 'test@test.com', password: '123' };
        validateRegister(req, res, next);
        expect(res.status).toHaveBeenCalledWith(400);
    });

    test('validateRegister passes valid data', () => {
        req.body = { email: 'test@test.com', password: 'password123' };
        validateRegister(req, res, next);
        expect(next).toHaveBeenCalled();
    });

    test('validateLogin requires email and password', () => {
        req.body = {};
        validateLogin(req, res, next);
        expect(res.status).toHaveBeenCalledWith(400);
    });
});

TASK 4: Integration Test — Task CRUD flow (14:30-17:30)
───────────────────────────────────────────────────────
// tests/integration/task.flow.test.js
describe('Task CRUD Flow', () => {
    let token;
    let taskId;

    beforeAll(async () => {
        // Login to get token
        const loginRes = await request(app)
            .post('/api/auth/login')
            .send({ email: 'test@test.com', password: 'password123' });
        token = loginRes.body.token;
    });

    test('Create Task', async () => {
        const res = await request(app)
            .post('/api/tasks')
            .set('Authorization', `Bearer ${token}`)
            .send({
                title: 'Test Task',
                description: 'Test Description',
                priority: 'high',
                due_date: '2024-12-31'
            });
        
        expect(res.status).toBe(201);
        taskId = res.body.id;
    });

    test('Get Tasks', async () => {
        const res = await request(app)
            .get('/api/tasks')
            .set('Authorization', `Bearer ${token}`);
        
        expect(res.status).toBe(200);
        expect(Array.isArray(res.body)).toBe(true);
    });
});

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 3 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Student Routes — GET/POST /api/students (08:30-12:00)
──────────────────────────────────────────────────────────────
// routes/studentRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken, checkRole } = require('../middleware/auth');

// GET /api/students (Admin only)
router.get('/', verifyToken, checkRole(['admin']), async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT s.*, u.full_name, u.email, u.phone
             FROM students s
             JOIN users u ON s.user_id = u.id`
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

// GET /api/students/:id
router.get('/:id', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT s.*, u.full_name, u.email, u.phone, u.avatar_url
             FROM students s
             JOIN users u ON s.user_id = u.id
             WHERE s.user_id = $1`,
            [req.params.id]
        );
        
        if (result.rows.length === 0) {
            return res.status(404).json({ success: false, message: 'Student not found' });
        }
        
        res.json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

// POST /api/students
router.post('/', verifyToken, async (req, res, next) => {
    try {
        const { school_name, grade_level, parent_phone } = req.body;
        
        const result = await pool.query(
            `INSERT INTO students (user_id, school_name, grade_level, parent_phone)
             VALUES ($1, $2, $3, $4) RETURNING *`,
            [req.user.id, school_name, grade_level, parent_phone]
        );
        
        res.status(201).json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

TASK 2: Subscription Routes — GET/POST /api/subscriptions (13:00-17:30)
──────────────────────────────────────────────────────────────────────
// routes/subscriptionRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken } = require('../middleware/auth');

// GET /api/subscriptions/plans
router.get('/plans', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            'SELECT * FROM subscription_plan WHERE is_active = true'
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

// GET /api/subscriptions/my
router.get('/my', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT s.*, sp.name as plan_name, sp.price
             FROM subscription s
             JOIN subscription_plan sp ON s.plan_id = sp.id
             WHERE s.user_id = $1`,
            [req.user.id]
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

// POST /api/subscriptions
router.post('/', verifyToken, async (req, res, next) => {
    try {
        const { plan_id } = req.body;
        
        // Get plan details
        const planResult = await pool.query(
            'SELECT * FROM subscription_plan WHERE id = $1',
            [plan_id]
        );
        
        if (planResult.rows.length === 0) {
            return res.status(404).json({ success: false, message: 'Plan not found' });
        }
        
        const plan = planResult.rows[0];
        const expiryDate = new Date();
        expiryDate.setMonth(expiryDate.getMonth() + plan.duration_months);
        
        const result = await pool.query(
            `INSERT INTO subscription (user_id, plan_id, expiry_date)
             VALUES ($1, $2, $3) RETURNING *`,
            [req.user.id, plan_id, expiryDate]
        );
        
        res.status(201).json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 4 TASKS — NANG switches to FRONTEND (Admin Dashboard + Connect)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Admin Dashboard page (08:30-12:00)
───────────────────────────────────────────
// src/pages/admin/AdminPanel.jsx
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';
import { useAuth } from '../../hooks/useAuth';

const AdminPanel = () => {
  const [stats, setStats] = useState(null);
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const { isAdmin } = useAuth();

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    try {
      // Fetch stats
      const statsRes = await api.get('/admin/stats');
      setStats(statsRes.data);

      // Fetch users
      const usersRes = await api.get('/admin/users');
      setUsers(usersRes.data);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <Loading />;

  return (
    <MainLayout>
      <div className="space-y-6">
        <h1 className="text-3xl font-bold">Admin Dashboard</h1>
        
        {/* Stats Cards */}
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
          <StatCard title="Total Users" value={stats?.total_users || 0} color="blue" />
          <StatCard title="Students" value={stats?.total_students || 0} color="green" />
          <StatCard title="Mentors" value={stats?.total_mentors || 0} color="purple" />
          <StatCard title="Revenue" value={`$${stats?.total_revenue || 0}`} color="orange" />
        </div>

        {/* Users Table */}
        <div className="bg-white rounded-xl shadow-md overflow-hidden">
          <h2 className="text-xl font-bold p-6 border-b">All Users</h2>
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead className="bg-gray-50">
                <tr>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">ID</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Name</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Email</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Role</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Status</th>
                </tr>
              </thead>
              <tbody className="divide-y divide-gray-200">
                {users.map(user => (
                  <tr key={user.id} className="hover:bg-gray-50">
                    <td className="px-6 py-4 text-sm text-gray-900">{user.id}</td>
                    <td className="px-6 py-4 text-sm text-gray-900">{user.full_name}</td>
                    <td className="px-6 py-4 text-sm text-gray-500">{user.email}</td>
                    <td className="px-6 py-4">
                      <span className={`px-2 py-1 rounded-full text-xs font-medium ${
                        user.user_type === 'admin' ? 'bg-red-100 text-red-800' :
                        user.user_type === 'mentor' ? 'bg-purple-100 text-purple-800' :
                        'bg-blue-100 text-blue-800'
                      }`}>
                        {user.user_type}
                      </span>
                    </td>
                    <td className="px-6 py-4">
                      <span className={`px-2 py-1 rounded-full text-xs font-medium ${
                        user.is_active ? 'bg-green-100 text-green-800' : 'bg-gray-100 text-gray-800'
                      }`}>
                        {user.is_active ? 'Active' : 'Inactive'}
                      </span>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </MainLayout>
  );
};

const StatCard = ({ title, value, color }) => {
  const colors = {
    blue: 'border-blue-500 text-blue-600',
    green: 'border-green-500 text-green-600',
    purple: 'border-purple-500 text-purple-600',
    orange: 'border-orange-500 text-orange-600'
  };

  return (
    <div className={`bg-white p-6 rounded-xl shadow-md border-l-4 ${colors[color]}`}>
      <h3 className="text-gray-500 text-sm font-medium">{title}</h3>
      <p className="text-3xl font-bold mt-2">{value}</p>
    </div>
  );
};

export default AdminPanel;

// Add admin routes to backend:
// routes/adminRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken, checkRole } = require('../middleware/auth');

router.get('/stats', verifyToken, checkRole(['admin']), async (req, res, next) => {
    try {
        const usersCount = await pool.query('SELECT COUNT(*) FROM users');
        const studentsCount = await pool.query('SELECT COUNT(*) FROM students');
        const mentorsCount = await pool.query('SELECT COUNT(*) FROM mentors');
        const revenue = await pool.query('SELECT COALESCE(SUM(amount), 0) FROM transaction_detail WHERE status = $1', ['completed']);
        
        res.json({
            total_users: parseInt(usersCount.rows[0].count),
            total_students: parseInt(studentsCount.rows[0].count),
            total_mentors: parseInt(mentorsCount.rows[0].count),
            total_revenue: parseFloat(revenue.rows[0].coalesce)
        });
    } catch (error) {
        next(error);
    }
});

router.get('/users', verifyToken, checkRole(['admin']), async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT u.id, u.email, u.full_name, u.is_active, u.created_at, ut.name as user_type
             FROM users u
             LEFT JOIN user_type ut ON u.user_type_id = ut.id
             ORDER BY u.created_at DESC`
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

TASK 2: Connect Admin to real API (13:00-17:30)
────────────────────────────────────────────────
// Already connected in AdminPanel.jsx above

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 5 TASKS — NANG stays on FRONTEND (Dark Mode + Final Responsive)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Dark mode + theme switcher (08:30-12:00)
─────────────────────────────────────────────────
// src/context/ThemeContext.jsx
import { createContext, useState, useEffect, useContext } from 'react';

const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [darkMode, setDarkMode] = useState(() => {
    return localStorage.getItem('darkMode') === 'true';
  });

  useEffect(() => {
    localStorage.setItem('darkMode', darkMode);
    if (darkMode) {
      document.documentElement.classList.add('dark');
    } else {
      document.documentElement.classList.remove('dark');
    }
  }, [darkMode]);

  const toggleDarkMode = () => setDarkMode(!darkMode);

  return (
    <ThemeContext.Provider value={{ darkMode, toggleDarkMode }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => useContext(ThemeContext);

// Update tailwind.config.js:
// darkMode: 'class',

// Add theme toggle button to MainLayout:
// <button onClick={toggleDarkMode}>
//   {darkMode ? '☀️' : '🌙'}
// </button>

// Dark mode styles (add to index.css):
.dark body { @apply bg-gray-900 text-white; }
.dark .bg-white { @apply bg-gray-800; }
.dark .text-gray-900 { @apply text-white; }
.dark .text-gray-600 { @apply text-gray-300; }
.dark .text-gray-500 { @apply text-gray-400; }
.dark .bg-gray-50 { @apply bg-gray-900; }
.dark .border-gray-200 { @apply border-gray-700; }

TASK 2: Final responsive check (13:00-17:30)
───────────────────────────────────────────
// Test all pages on:
// - iPhone SE (375px)
// - iPad (768px)
// - Desktop (1024px+)
// Fix any layout issues found

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 6 TASKS — NANG stays on FRONTEND (Form Validation + Final Polish)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Form validation feedback (08:30-12:00)
──────────────────────────────────────────────
// Enhance Input component with real-time validation
// src/components/Input.jsx (enhanced)

import { useState } from 'react';

const Input = ({ label, type = 'text', name, value, onChange, validate, required = false }) => {
  const [touched, setTouched] = useState(false);
  const [error, setError] = useState('');

  const handleBlur = () => {
    setTouched(true);
    if (validate) {
      const validationError = validate(value);
      setError(validationError);
    }
  };

  const handleChange = (e) => {
    onChange(e);
    if (touched && validate) {
      const validationError = validate(e.target.value);
      setError(validationError);
    }
  };

  return (
    <div className="mb-4">
      <label className="block text-sm font-medium text-gray-700 mb-1">
        {label} {required && <span className="text-red-500">*</span>}
      </label>
      <input
        type={type}
        name={name}
        value={value}
        onChange={handleChange}
        onBlur={handleBlur}
        className={`w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 transition-all ${
          error ? 'border-red-500 focus:ring-red-500' : 'border-gray-300 focus:ring-blue-500'
        }`}
      />
      {error && <p className="mt-1 text-sm text-red-500">{error}</p>}
    </div>
  );
};

export default Input;

TASK 2: Final UI polish (13:00-17:30)
─────────────────────────────────────
// Review all pages
// Fix any visual inconsistencies
// Ensure consistent spacing, colors, fonts
// Add final touches

═══════════════════════════════════════════════════════════════════════════════
✅ NANG CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

DAY 1 (Backend):
  ☐ GitHub repo created
  ☐ Git flow established
  ☐ .gitignore configured
  ☐ validate.js created (email + password validation)
  ☐ generateToken working
  ☐ verifyToken working
  ☐ checkRole working (admin/student/mentor)
  ☐ Unit tests for auth functions passing

DAY 2 (Backend):
  ☐ Integration test for auth flow passing
  ☐ API tests for all auth endpoints passing
  ☐ Unit tests for validation functions passing
  ☐ Integration test for Task CRUD passing

DAY 3 (Backend):
  ☐ Student Routes created
  ☐ Subscription Routes created
  ☐ All routes tested

DAY 4 (Frontend switch):
  ☐ Admin Dashboard page created
  ☐ Connected to real API
  ☐ Admin stats showing
  ☐ Users table showing

DAY 5 (Frontend):
  ☐ Dark mode toggle working
  ☐ All pages responsive on mobile/tablet/desktop

DAY 6 (Frontend):
  ☐ Form validation feedback working
  ☐ Final UI polish complete

═══════════════════════════════════════════════════════════════════════════════
END OF NANG DOCUMENT
═══════════════════════════════════════════════════════════════════════════════

═══════════════════════════════════════════════════════════════════════════════
     📄 HEING — BACKEND DEVELOPER (Express + Auth Routes + API Routes)
═══════════════════════════════════════════════════════════════════════════════

Role: Backend Developer — API Lead
Team: BACKEND
Tasks: Express server, Auth middleware functions, Auth routes, User routes, Task/Bug/Mentor/Community routes

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 1 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Setup Express server (server.js) (08:30-09:00)
─────────────────────────────────────────────────────
// backend/server.js
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();

// Middleware
app.use(cors({
    origin: process.env.CORS_ORIGIN || 'http://localhost:5173',
    credentials: true
}));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Health check
app.get('/health', (req, res) => res.json({ 
    status: 'OK', 
    timestamp: new Date().toISOString() 
}));

// Routes (add more on Day 2-3)
app.use('/api/auth', require('./routes/authRoutes'));
app.use('/api/users', require('./routes/userRoutes'));

// 404 handler
app.use((req, res) => {
    res.status(404).json({ message: 'Route not found' });
});

// Global error handler
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(err.status || 500).json({ 
        success: false,
        message: err.message || 'Internal Server Error'
    });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`🚀 Server running on port ${PORT}`));

// .env
PORT=5000
DATABASE_URL=postgresql://project_user:your_password@localhost:5432/project_db
JWT_SECRET=your_super_secret_key
JWT_EXPIRES_IN=7d
CORS_ORIGIN=http://localhost:5173

TASK 2: middleware/auth.js — Hash password (bcrypt) (09:00-10:30)
─────────────────────────────────────────────────────────────────
// middleware/auth.js
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

const hashPassword = async (password) => {
    const salt = await bcrypt.genSalt(10);
    return await bcrypt.hash(password, salt);
};

TASK 3: middleware/auth.js — Compare password (bcrypt) (10:30-12:00)
───────────────────────────────────────────────────────────────────
const comparePassword = async (plainPassword, hashedPassword) => {
    return await bcrypt.compare(plainPassword, hashedPassword);
};

TASK 4: Auth Routes — POST /api/auth/register (13:00-14:30)
───────────────────────────────────────────────────────────
// controllers/authController.js
const pool = require('../database/db');
const { hashPassword, comparePassword, generateToken } = require('../middleware/auth');

const register = async (req, res, next) => {
    try {
        const { email, password, full_name, user_type_id = 2 } = req.body;
        
        const existing = await pool.query('SELECT id FROM users WHERE email = $1', [email]);
        if (existing.rows.length > 0) {
            return res.status(409).json({ success: false, message: 'User exists' });
        }
        
        const hashed = await hashPassword(password);
        const result = await pool.query(
            `INSERT INTO users (email, password_hash, full_name, user_type_id)
             VALUES ($1, $2, $3, $4) RETURNING id, email, full_name, user_type_id`,
            [email, hashed, full_name, user_type_id]
        );
        
        const user = result.rows[0];
        const token = generateToken(user.id, user.email, user.user_type_id);
        
        res.status(201).json({
            success: true,
            token,
            user: { ...user, created_at: new Date() }
        });
    } catch (error) {
        next(error);
    }
};

TASK 5: Auth Routes — POST /api/auth/login (14:30-16:00)
──────────────────────────────────────────────────────────
const login = async (req, res, next) => {
    try {
        const { email, password } = req.body;
        
        const result = await pool.query(
            'SELECT id, email, password_hash, full_name, user_type_id FROM users WHERE email = $1',
            [email]
        );
        
        if (result.rows.length === 0) {
            return res.status(401).json({ success: false, message: 'Invalid credentials' });
        }
        
        const user = result.rows[0];
        const isMatch = await comparePassword(password, user.password_hash);
        
        if (!isMatch) {
            return res.status(401).json({ success: false, message: 'Invalid credentials' });
        }
        
        const token = generateToken(user.id, user.email, user.user_type_id);
        
        res.json({
            success: true,
            token,
            user: { id: user.id, email: user.email, full_name: user.full_name, user_type_id: user.user_type_id }
        });
    } catch (error) {
        next(error);
    }
};

TASK 6: User Routes — GET /api/users/me (16:00-17:30)
──────────────────────────────────────────────────────
// controllers/userController.js
const getCurrentUser = async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT u.id, u.email, u.full_name, u.phone, u.avatar_url, u.is_active, 
                    u.created_at, ut.name as user_type
             FROM users u
             LEFT JOIN user_type ut ON u.user_type_id = ut.id
             WHERE u.id = $1`,
            [req.user.id]
        );
        
        if (result.rows.length === 0) {
            return res.status(404).json({ success: false, message: 'User not found' });
        }
        
        res.json({ success: true, user: result.rows[0] });
    } catch (error) {
        next(error);
    }
};

// routes/userRoutes.js
const express = require('express');
const router = express.Router();
const { verifyToken } = require('../middleware/auth');
const { getCurrentUser } = require('../controllers/userController');

router.get('/me', verifyToken, getCurrentUser);

module.exports = router;

// routes/authRoutes.js
const express = require('express');
const router = express.Router();
const { register, login } = require('../controllers/authController');
const { validateRegister, validateLogin } = require('../middleware/validate');

router.post('/register', validateRegister, register);
router.post('/login', validateLogin, login);

module.exports = router;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 2 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Auth Routes — Add password reset (optional) (08:30-09:30)
─────────────────────────────────────────────────────────────────
// controllers/authController.js
const resetPassword = async (req, res, next) => {
    try {
        const { email } = req.body;
        // In production: send email with reset link
        // For demo: just return success
        res.json({ success: true, message: 'Reset link sent if email exists' });
    } catch (error) {
        next(error);
    }
};

// Add to authRoutes.js
router.post('/reset-password', resetPassword);

TASK 2: User Routes — PUT /api/users/:id (update profile) (09:30-10:30)
─────────────────────────────────────────────────────────────────────────
const updateUser = async (req, res, next) => {
    try {
        const { full_name, phone, avatar_url } = req.body;
        const userId = parseInt(req.params.id);
        
        if (userId !== req.user.id) {
            return res.status(403).json({ success: false, message: 'Can only update own profile' });
        }
        
        const result = await pool.query(
            `UPDATE users SET full_name = COALESCE($1, full_name),
             phone = COALESCE($2, phone),
             avatar_url = COALESCE($3, avatar_url),
             updated_at = CURRENT_TIMESTAMP
             WHERE id = $4 RETURNING *`,
            [full_name, phone, avatar_url, userId]
        );
        
        res.json({ success: true, user: result.rows[0] });
    } catch (error) {
        next(error);
    }
};

// Add to userRoutes.js
router.put('/:id', verifyToken, updateUser);

TASK 3: Task Routes — GET/POST /api/tasks (10:30-12:00)
────────────────────────────────────────────────────────
// routes/taskRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken } = require('../middleware/auth');

// GET /api/tasks
router.get('/', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            'SELECT * FROM tasks WHERE user_id = $1 ORDER BY created_at DESC',
            [req.user.id]
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

// POST /api/tasks
router.post('/', verifyToken, async (req, res, next) => {
    try {
        const { title, description, priority, due_date } = req.body;
        
        const result = await pool.query(
            `INSERT INTO tasks (user_id, title, description, priority, due_date)
             VALUES ($1, $2, $3, $4, $5) RETURNING *`,
            [req.user.id, title, description, priority, due_date]
        );
        
        res.status(201).json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

TASK 4: Bug Routes — GET/POST /api/bugs (13:00-14:30)
─────────────────────────────────────────────────────
// routes/bugRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken } = require('../middleware/auth');

router.get('/', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            'SELECT * FROM bugs ORDER BY created_at DESC'
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

router.post('/', verifyToken, async (req, res, next) => {
    try {
        const { title, description, priority } = req.body;
        
        const result = await pool.query(
            `INSERT INTO bugs (reporter_id, title, description, priority, status)
             VALUES ($1, $2, $3, $4, 'open') RETURNING *`,
            [req.user.id, title, description, priority]
        );
        
        res.status(201).json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

// Note: Add tasks and bugs tables to schema if not exists:
/*
CREATE TABLE tasks (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    priority VARCHAR(20) DEFAULT 'medium',
    status VARCHAR(20) DEFAULT 'pending',
    due_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE bugs (
    id SERIAL PRIMARY KEY,
    reporter_id INTEGER REFERENCES users(id) ON DELETE SET NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    priority VARCHAR(20) DEFAULT 'medium',
    status VARCHAR(20) DEFAULT 'open',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
*/

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 3 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Mentor Routes — GET/POST /api/mentors (08:30-12:00)
────────────────────────────────────────────────────────────
// routes/mentorRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken } = require('../middleware/auth');

// GET /api/mentors
router.get('/', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT m.*, u.full_name, u.email, u.avatar_url
             FROM mentors m
             JOIN users u ON m.user_id = u.id
             WHERE m.is_verified = true`
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

// GET /api/mentors/:id
router.get('/:id', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT m.*, u.full_name, u.email, u.avatar_url
             FROM mentors m
             JOIN users u ON m.user_id = u.id
             WHERE m.id = $1`,
            [req.params.id]
        );
        
        if (result.rows.length === 0) {
            return res.status(404).json({ success: false, message: 'Mentor not found' });
        }
        
        res.json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

// POST /api/mentors
router.post('/', verifyToken, async (req, res, next) => {
    try {
        const { expertise, years_experience, company, hourly_rate } = req.body;
        
        const result = await pool.query(
            `INSERT INTO mentors (user_id, expertise, years_experience, company, hourly_rate)
             VALUES ($1, $2, $3, $4, $5) RETURNING *`,
            [req.user.id, expertise, years_experience, company, hourly_rate]
        );
        
        res.status(201).json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

TASK 2: Community Routes — GET/POST /api/community-posts (13:00-17:30)
───────────────────────────────────────────────────────────────────────
// routes/communityRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken } = require('../middleware/auth');

router.get('/', verifyToken, async (req, res, next) => {
    try {
        const result = await pool.query(
            `SELECT cp.*, u.full_name as author, u.avatar_url
             FROM community_post cp
             JOIN users u ON cp.user_id = u.id
             WHERE cp.is_published = true
             ORDER BY cp.created_at DESC`
        );
        res.json(result.rows);
    } catch (error) {
        next(error);
    }
});

router.post('/', verifyToken, async (req, res, next) => {
    try {
        const { community_type_id, title, content } = req.body;
        
        const result = await pool.query(
            `INSERT INTO community_post (user_id, community_type_id, title, content)
             VALUES ($1, $2, $3, $4) RETURNING *`,
            [req.user.id, community_type_id, title, content]
        );
        
        res.status(201).json(result.rows[0]);
    } catch (error) {
        next(error);
    }
});

module.exports = router;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 4 TASKS — HEING switches to FRONTEND (Transaction History + Connect)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Transaction History page (08:30-12:00)
────────────────────────────────────────────────
// src/pages/transaction/TransactionHistory.jsx
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const TransactionHistory = () => {
  const [transactions, setTransactions] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchTransactions();
  }, []);

  const fetchTransactions = async () => {
    try {
      const response = await api.get('/transactions');
      setTransactions(response.data.data || response.data);
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
        <h1 className="text-2xl font-bold">Transaction History</h1>
        
        <div className="bg-white rounded-xl shadow-md overflow-hidden">
          <table className="w-full">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Date</th>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Type</th>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Amount</th>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Status</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-gray-200">
              {transactions.map(tx => (
                <tr key={tx.id} className="hover:bg-gray-50">
                  <td className="px-6 py-4 text-sm text-gray-900">
                    {new Date(tx.created_at).toLocaleDateString()}
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-900 capitalize">{tx.type}</td>
                  <td className="px-6 py-4 text-sm font-medium text-gray-900">
                    ${tx.amount}
                  </td>
                  <td className="px-6 py-4">
                    <span className={`px-2 py-1 rounded-full text-xs font-medium ${
                      tx.status === 'completed' ? 'bg-green-100 text-green-800' :
                      tx.status === 'pending' ? 'bg-yellow-100 text-yellow-800' :
                      'bg-red-100 text-red-800'
                    }`}>
                      {tx.status}
                    </span>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>
    </MainLayout>
  );
};

export default TransactionHistory;

TASK 2: Connect Transaction to real API (13:00-17:30)
────────────────────────────────────────────────────
// Already connected in TransactionHistory.jsx above

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 5 TASKS — HEING stays on FRONTEND (Responsive + Production Test)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Responsive design (mobile, tablet) (08:30-12:00)
────────────────────────────────────────────────────────
// Ensure all pages work on mobile
// Test on Chrome DevTools mobile view
// Fix any layout issues

TASK 2: Production test (smoke test) (13:00-17:30)
──────────────────────────────────────────────────
// Test deployed app on Vercel
// Check all routes work
// Check API connections work
// Check auth flow works end-to-end

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 6 TASKS — HEING stays on FRONTEND (Toast + Image Optimization)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Toast notifications (08:30-12:00)
──────────────────────────────────────────
// Work with NANG on toast system
// src/components/Toast.jsx (see NANG's document)

TASK 2: Optimize images + lazy loading (13:00-17:30)
────────────────────────────────────────────────────
// Use lazy loading for images
// Compress images before upload
// Use WebP format when possible

═══════════════════════════════════════════════════════════════════════════════
✅ HEING CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

DAY 1 (Backend):
  ☐ Express server running on port 5000
  ☐ hashPassword function working
  ☐ comparePassword function working
  ☐ POST /api/auth/register working
  ☐ POST /api/auth/login working
  ☐ GET /api/users/me working

DAY 2 (Backend):
  ☐ Password reset route (optional)
  ☐ PUT /api/users/:id working
  ☐ GET/POST /api/tasks working
  ☐ GET/POST /api/bugs working

DAY 3 (Backend):
  ☐ GET/POST /api/mentors working
  ☐ GET/POST /api/community-posts working

DAY 4 (Frontend switch):
  ☐ Transaction History page created
  ☐ Connected to real API

DAY 5 (Frontend):
  ☐ All pages responsive
  ☐ Production smoke test passed

DAY 6 (Frontend):
  ☐ Toast notifications working
  ☐ Images optimized

═══════════════════════════════════════════════════════════════════════════════
END OF HEING DOCUMENT
═══════════════════════════════════════════════════════════════════════════════

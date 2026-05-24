═══════════════════════════════════════════════════════════════════════════════
     📄 RIN — FRONTEND DEVELOPER (Axios + API + Forms + Integration)
═══════════════════════════════════════════════════════════════════════════════

Role: Frontend Developer — API Integration Lead
Team: FRONTEND
Tasks: Axios setup, Register UI, API connection, Token management, Forms

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 1 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Setup Axios + folder structure (08:30-09:00)
─────────────────────────────────────────────────
// Create folder structure
mkdir -p src/services src/hooks src/context src/utils

// src/services/api.js — Axios instance with interceptors
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:5000/api';

const api = axios.create({
    baseURL: API_BASE_URL,
    headers: { 'Content-Type': 'application/json' },
    timeout: 10000
});

// Request interceptor — Add token
api.interceptors.request.use(
    (config) => {
        const token = localStorage.getItem('token');
        if (token) config.headers.Authorization = `Bearer ${token}`;
        return config;
    },
    (error) => Promise.reject(error)
);

// Response interceptor — Handle errors
api.interceptors.response.use(
    (response) => response,
    (error) => {
        if (error.response?.status === 401) {
            localStorage.removeItem('token');
            localStorage.removeItem('user');
            window.location.href = '/login';
        }
        return Promise.reject(error);
    }
);

export default api;

TASK 2: Register page UI (09:00-10:30)
──────────────────────────────────────
// src/pages/auth/Register.jsx
import { useState } from 'react';
import { Link } from 'react-router-dom';

const Register = () => {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    confirmPassword: '',
    full_name: '',
    user_type_id: 2
  });
  const [errors, setErrors] = useState({});
  const [loading, setLoading] = useState(false);

  const validate = () => {
    const newErrors = {};
    if (!formData.email) newErrors.email = 'Email required';
    else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.email)) {
      newErrors.email = 'Invalid email format';
    }
    
    if (!formData.password) newErrors.password = 'Password required';
    else if (formData.password.length < 6) newErrors.password = 'Min 6 characters';
    
    if (formData.password !== formData.confirmPassword) {
      newErrors.confirmPassword = 'Passwords do not match';
    }
    
    if (!formData.full_name) newErrors.full_name = 'Name required';
    return newErrors;
  };

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
    if (errors[e.target.name]) setErrors({ ...errors, [e.target.name]: '' });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    const validationErrors = validate();
    if (Object.keys(validationErrors).length > 0) {
      setErrors(validationErrors);
      return;
    }
    // Will connect to API later
    console.log('Register:', formData);
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gradient-to-br from-green-50 to-teal-100">
      <div className="bg-white p-8 rounded-2xl shadow-xl w-full max-w-md">
        <h2 className="text-2xl font-bold text-center text-gray-800 mb-6">Create Account</h2>
        
        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Full Name</label>
            <input
              type="text"
              name="full_name"
              value={formData.full_name}
              onChange={handleChange}
              className={`w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 focus:ring-green-500 ${
                errors.full_name ? 'border-red-500' : 'border-gray-300'
              }`}
              placeholder="Your full name"
            />
            {errors.full_name && <p className="text-red-500 text-sm mt-1">{errors.full_name}</p>}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Email</label>
            <input
              type="email"
              name="email"
              value={formData.email}
              onChange={handleChange}
              className={`w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 focus:ring-green-500 ${
                errors.email ? 'border-red-500' : 'border-gray-300'
              }`}
              placeholder="your@email.com"
            />
            {errors.email && <p className="text-red-500 text-sm mt-1">{errors.email}</p>}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Account Type</label>
            <select
              name="user_type_id"
              value={formData.user_type_id}
              onChange={handleChange}
              className="w-full px-4 py-2 border border-gray-300 rounded-lg outline-none focus:ring-2 focus:ring-green-500"
            >
              <option value={2}>Student</option>
              <option value={3}>Mentor</option>
            </select>
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Password</label>
            <input
              type="password"
              name="password"
              value={formData.password}
              onChange={handleChange}
              className={`w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 focus:ring-green-500 ${
                errors.password ? 'border-red-500' : 'border-gray-300'
              }`}
              placeholder="Min 6 characters"
            />
            {errors.password && <p className="text-red-500 text-sm mt-1">{errors.password}</p>}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Confirm Password</label>
            <input
              type="password"
              name="confirmPassword"
              value={formData.confirmPassword}
              onChange={handleChange}
              className={`w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 focus:ring-green-500 ${
                errors.confirmPassword ? 'border-red-500' : 'border-gray-300'
              }`}
              placeholder="Repeat password"
            />
            {errors.confirmPassword && <p className="text-red-500 text-sm mt-1">{errors.confirmPassword}</p>}
          </div>

          <button
            type="submit"
            disabled={loading}
            className="w-full bg-green-600 hover:bg-green-700 text-white font-medium py-2.5 rounded-lg transition-colors disabled:opacity-50"
          >
            {loading ? 'Creating...' : 'Create Account'}
          </button>
        </form>

        <p className="text-center text-gray-600 mt-4 text-sm">
          Already have an account?{' '}
          <Link to="/login" className="text-green-600 hover:underline">Login</Link>
        </p>
      </div>
    </div>
  );
};

export default Register;

TASK 3: Connect Register UI to mock API (10:30-12:00)
───────────────────────────────────────────────────
// src/services/mockApi.js
export const mockRegister = async (userData) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userData.email && userData.password.length >= 6) {
        resolve({
          token: 'mock_jwt_token_' + Date.now(),
          user: {
            id: Date.now(),
            email: userData.email,
            full_name: userData.full_name,
            user_type_id: userData.user_type_id
          }
        });
      } else {
        reject(new Error('Invalid registration data'));
      }
    }, 500);
  });
};

// Update Register.jsx
import { mockRegister } from '../../services/mockApi';

const handleSubmit = async (e) => {
  e.preventDefault();
  const validationErrors = validate();
  if (Object.keys(validationErrors).length > 0) {
    setErrors(validationErrors);
    return;
  }

  setLoading(true);
  try {
    const response = await mockRegister({
      email: formData.email,
      password: formData.password,
      full_name: formData.full_name,
      user_type_id: parseInt(formData.user_type_id)
    });
    localStorage.setItem('token', response.token);
    localStorage.setItem('user', JSON.stringify(response.user));
    window.location.href = '/dashboard';
  } catch (err) {
    setErrors({ general: err.message });
  } finally {
    setLoading(false);
  }
};

TASK 4: Add Token — Axios interceptor (save to localStorage) (13:00-14:30)
─────────────────────────────────────────────────────────────────────────
// Already done in api.js above
// The interceptor automatically:
// 1. Gets token from localStorage on every request
// 2. Adds Authorization header
// 3. On 401, clears token and redirects to login

TASK 5: Connect Register to REAL API (14:30-16:00)
─────────────────────────────────────────────────
// Update Register.jsx to use real API
import api from '../../services/api';

const handleSubmit = async (e) => {
  e.preventDefault();
  const validationErrors = validate();
  if (Object.keys(validationErrors).length > 0) {
    setErrors(validationErrors);
    return;
  }

  setLoading(true);
  try {
    const response = await api.post('/auth/register', {
      email: formData.email,
      password: formData.password,
      full_name: formData.full_name,
      user_type_id: parseInt(formData.user_type_id)
    });
    
    const { token, user } = response.data;
    localStorage.setItem('token', token);
    localStorage.setItem('user', JSON.stringify(user));
    window.location.href = '/dashboard';
  } catch (err) {
    setErrors({ general: err.response?.data?.message || 'Registration failed' });
  } finally {
    setLoading(false);
  }
};

TASK 6: Error Handling — Global error handler (401, 403, 500) (16:00-17:30)
────────────────────────────────────────────────────────────────────────────
// Update api.js response interceptor
api.interceptors.response.use(
    (response) => response,
    (error) => {
        if (error.response) {
            const { status, data } = error.response;
            
            switch (status) {
                case 401:
                    localStorage.removeItem('token');
                    localStorage.removeItem('user');
                    alert('Session expired. Please login again.');
                    window.location.href = '/login';
                    break;
                    
                case 403:
                    alert('You do not have permission to access this resource.');
                    break;
                    
                case 404:
                    console.error('Resource not found:', data.message);
                    break;
                    
                case 422:
                    console.error('Validation error:', data.message);
                    break;
                    
                case 500:
                    console.error('Server error:', data.message);
                    alert('Server error. Please try again later.');
                    break;
                    
                default:
                    console.error(`Error ${status}:`, data);
            }
        } else if (error.request) {
            alert('Network error. Please check your connection.');
        }
        
        return Promise.reject(error);
    }
);

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 2 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Polish Register page (password strength, email validation) (08:30-10:00)
───────────────────────────────────────────────────────────────────────────────
// Add password strength indicator
// Add to Register.jsx

const PasswordStrength = ({ password }) => {
  const getStrength = () => {
    let score = 0;
    if (password.length >= 6) score++;
    if (password.length >= 10) score++;
    if (/[A-Z]/.test(password)) score++;
    if (/[0-9]/.test(password)) score++;
    if (/[^A-Za-z0-9]/.test(password)) score++;
    return score;
  };

  const strength = getStrength();
  const colors = ['bg-red-500', 'bg-orange-500', 'bg-yellow-500', 'bg-blue-500', 'bg-green-500'];
  const labels = ['Very Weak', 'Weak', 'Fair', 'Good', 'Strong'];

  return (
    <div className="mt-2">
      <div className="flex gap-1 h-2">
        {[0, 1, 2, 3, 4].map(i => (
          <div key={i} className={`flex-1 rounded ${i < strength ? colors[strength - 1] : 'bg-gray-200'}`} />
        ))}
      </div>
      <p className="text-xs text-gray-500 mt-1">{labels[strength - 1] || 'Enter password'}</p>
    </div>
  );
};

// Add email validation with debounce
const [emailChecking, setEmailChecking] = useState(false);

const checkEmail = async (email) => {
  if (!email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) return;
  
  setEmailChecking(true);
  try {
    const response = await api.get(`/auth/check-email?email=${email}`);
    if (response.data.exists) {
      setErrors(prev => ({ ...prev, email: 'Email already registered' }));
    }
  } catch (err) {
    // Ignore error
  } finally {
    setEmailChecking(false);
  }
};

TASK 2: Connect Update profile to API (10:00-12:00)
──────────────────────────────────────────────────
// src/pages/profile/EditProfile.jsx
import { useState, useEffect } from 'react';
import api from '../../services/api';

const EditProfile = () => {
  const [formData, setFormData] = useState({
    full_name: '',
    phone: '',
    avatar_url: ''
  });
  const [loading, setLoading] = useState(false);
  const [success, setSuccess] = useState(false);

  useEffect(() => {
    fetchProfile();
  }, []);

  const fetchProfile = async () => {
    try {
      const response = await api.get('/users/me');
      setFormData({
        full_name: response.data.user.full_name || '',
        phone: response.data.user.phone || '',
        avatar_url: response.data.user.avatar_url || ''
      });
    } catch (err) {
      console.error(err);
    }
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    setSuccess(false);
    
    try {
      const user = JSON.parse(localStorage.getItem('user') || '{}');
      await api.put(`/users/${user.id}`, formData);
      setSuccess(true);
      // Refresh user data
      const response = await api.get('/users/me');
      localStorage.setItem('user', JSON.stringify(response.data.user));
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="max-w-2xl mx-auto bg-white p-8 rounded-xl shadow-md">
      <h2 className="text-2xl font-bold mb-6">Edit Profile</h2>
      
      {success && (
        <div className="bg-green-50 text-green-600 p-3 rounded-lg mb-4">
          Profile updated successfully!
        </div>
      )}
      
      <form onSubmit={handleSubmit} className="space-y-4">
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Full Name</label>
          <input
            type="text"
            value={formData.full_name}
            onChange={(e) => setFormData({ ...formData, full_name: e.target.value })}
            className="w-full px-4 py-2 border border-gray-300 rounded-lg"
          />
        </div>
        
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Phone</label>
          <input
            type="tel"
            value={formData.phone}
            onChange={(e) => setFormData({ ...formData, phone: e.target.value })}
            className="w-full px-4 py-2 border border-gray-300 rounded-lg"
          />
        </div>
        
        <button
          type="submit"
          disabled={loading}
          className="w-full bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 disabled:opacity-50"
        >
          {loading ? 'Saving...' : 'Save Changes'}
        </button>
      </form>
    </div>
  );
};

export default EditProfile;

TASK 3: Task Form page (create task) (13:00-14:30)
─────────────────────────────────────────────────
// src/pages/tasks/TaskForm.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import api from '../../services/api';

const TaskForm = () => {
  const [formData, setFormData] = useState({
    title: '',
    description: '',
    priority: 'medium',
    due_date: ''
  });
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    
    try {
      await api.post('/tasks', formData);
      navigate('/tasks');
    } catch (err) {
      alert(err.response?.data?.message || 'Failed to create task');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="max-w-2xl mx-auto bg-white p-8 rounded-xl shadow-md">
      <h2 className="text-2xl font-bold mb-6">Create New Task</h2>
      
      <form onSubmit={handleSubmit} className="space-y-4">
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Title</label>
          <input
            type="text"
            value={formData.title}
            onChange={(e) => setFormData({ ...formData, title: e.target.value })}
            className="w-full px-4 py-2 border border-gray-300 rounded-lg"
            required
          />
        </div>
        
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Description</label>
          <textarea
            value={formData.description}
            onChange={(e) => setFormData({ ...formData, description: e.target.value })}
            className="w-full px-4 py-2 border border-gray-300 rounded-lg h-32"
          />
        </div>
        
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Priority</label>
          <select
            value={formData.priority}
            onChange={(e) => setFormData({ ...formData, priority: e.target.value })}
            className="w-full px-4 py-2 border border-gray-300 rounded-lg"
          >
            <option value="low">Low</option>
            <option value="medium">Medium</option>
            <option value="high">High</option>
          </select>
        </div>
        
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Due Date</label>
          <input
            type="date"
            value={formData.due_date}
            onChange={(e) => setFormData({ ...formData, due_date: e.target.value })}
            className="w-full px-4 py-2 border border-gray-300 rounded-lg"
          />
        </div>
        
        <button
          type="submit"
          disabled={loading}
          className="w-full bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 disabled:opacity-50"
        >
          {loading ? 'Creating...' : 'Create Task'}
        </button>
      </form>
    </div>
  );
};

export default TaskForm;

TASK 4: Connect Task Create to real API (14:30-17:30)
────────────────────────────────────────────────────
// Already connected in TaskForm.jsx above
// Uses api.post('/tasks', formData)

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 3 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Student Profile page (08:30-12:00)
─────────────────────────────────────────
// src/pages/students/StudentProfile.jsx
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const StudentProfile = () => {
  const [student, setStudent] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchStudentProfile();
  }, []);

  const fetchStudentProfile = async () => {
    try {
      const user = JSON.parse(localStorage.getItem('user') || '{}');
      const response = await api.get(`/students/${user.id}`);
      setStudent(response.data);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <Loading />;
  if (!student) return <div>Student profile not found</div>;

  return (
    <MainLayout>
      <div className="max-w-4xl mx-auto">
        <div className="bg-white rounded-xl shadow-md p-8">
          <div className="flex items-center gap-6 mb-6">
            <div className="w-24 h-24 bg-purple-100 rounded-full flex items-center justify-center text-4xl">
              🎓
            </div>
            <div>
              <h1 className="text-2xl font-bold">{student.full_name}</h1>
              <p className="text-gray-500">{student.school_name}</p>
              <p className="text-gray-500">Grade {student.grade_level}</p>
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div className="bg-gray-50 p-4 rounded-lg">
              <h3 className="font-semibold mb-2">Contact</h3>
              <p className="text-gray-600">Email: {student.email}</p>
              <p className="text-gray-600">Phone: {student.phone || 'Not set'}</p>
              <p className="text-gray-600">Parent: {student.parent_phone || 'Not set'}</p>
            </div>
            
            <div className="bg-gray-50 p-4 rounded-lg">
              <h3 className="font-semibold mb-2">Progress</h3>
              <p className="text-gray-600">Tasks completed: {student.tasks_completed || 0}</p>
              <p className="text-gray-600">Mentors: {student.mentor_count || 0}</p>
            </div>
          </div>
        </div>
      </div>
    </MainLayout>
  );
};

export default StudentProfile;

TASK 2: Connect Student to real API (13:00-17:30)
────────────────────────────────────────────────
// Already connected above
// Uses api.get(`/students/${user.id}`)

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 4 TASKS — RIN switches to BACKEND (Banking Routes + API Test)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Banking Routes — GET/POST /api/banking-payments (08:30-12:00)
────────────────────────────────────────────────────────────────────
// routes/bankingRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken, checkRole } = require('../middleware/auth');

// GET /api/banking-payments (Admin only)
router.get('/', verifyToken, checkRole(['admin']), async (req, res, next) => {
  try {
    const result = await pool.query(
      `SELECT bp.*, td.amount, td.status as transaction_status
       FROM banking_payment bp
       JOIN transaction_detail td ON bp.transaction_id = td.id
       ORDER BY bp.created_at DESC`
    );
    res.json(result.rows);
  } catch (error) {
    next(error);
  }
});

// POST /api/banking-payments
router.post('/', verifyToken, async (req, res, next) => {
  try {
    const { transaction_id, bank_name, account_number, account_holder } = req.body;
    
    const result = await pool.query(
      `INSERT INTO banking_payment (transaction_id, bank_name, account_number, account_holder)
       VALUES ($1, $2, $3, $4) RETURNING *`,
      [transaction_id, bank_name, account_number, account_holder]
    );
    
    res.status(201).json(result.rows[0]);
  } catch (error) {
    next(error);
  }
});

module.exports = router;

TASK 2: API Test — All endpoints (13:00-17:30)
─────────────────────────────────────────────
// tests/api/allEndpoints.test.js
const request = require('supertest');
const app = require('../../server');

describe('API Endpoints', () => {
  let token;
  let userId;

  beforeAll(async () => {
    // Register and login to get token
    const registerRes = await request(app)
      .post('/api/auth/register')
      .send({
        email: `test_${Date.now()}@example.com`,
        password: 'password123',
        full_name: 'Test User'
      });
    
    token = registerRes.body.token;
    userId = registerRes.body.user.id;
  });

  test('GET /api/users/me', async () => {
    const res = await request(app)
      .get('/api/users/me')
      .set('Authorization', `Bearer ${token}`);
    
    expect(res.status).toBe(200);
    expect(res.body.user).toBeDefined();
  });

  test('GET /api/tasks', async () => {
    const res = await request(app)
      .get('/api/tasks')
      .set('Authorization', `Bearer ${token}`);
    
    expect(res.status).toBe(200);
    expect(Array.isArray(res.body)).toBe(true);
  });

  test('GET /api/mentors', async () => {
    const res = await request(app)
      .get('/api/mentors')
      .set('Authorization', `Bearer ${token}`);
    
    expect(res.status).toBe(200);
  });

  // Add more endpoint tests...
});

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 5 TASKS — RIN stays on BACKEND (CI/CD + Deploy)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Auto Test (CI/CD) — GitHub Actions (08:30-12:00)
─────────────────────────────────────────────────────────
// .github/workflows/ci.yml
name: CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: test_db
          POSTGRES_USER: test_user
          POSTGRES_PASSWORD: test_pass
        ports:
          - 5432:5432
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: |
          cd backend
          npm ci
      
      - name: Run tests
        env:
          DATABASE_URL: postgresql://test_user:test_pass@localhost:5432/test_db
          JWT_SECRET: test_secret
        run: |
          cd backend
          npm test

TASK 2: Deploy backend to Render (13:00-17:30)
──────────────────────────────────────────────
// Steps:
// 1. Push code to GitHub
// 2. Go to render.com → New Web Service
// 3. Connect GitHub repo
// 4. Settings:
//    - Build Command: cd backend && npm install
//    - Start Command: cd backend && npm start
//    - Environment Variables:
//      DATABASE_URL=postgresql://...
//      JWT_SECRET=your_secret
//      CORS_ORIGIN=https://your-frontend.vercel.app

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 6 TASKS — RIN stays on BACKEND (Integration + E2E)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Integration Test — Full flows (08:30-12:00)
───────────────────────────────────────────────────
// tests/integration/fullFlows.test.js
// Test complete user journeys

TASK 2: E2E Test setup (Playwright) (13:00-17:30)
─────────────────────────────────────────────────
// Install: npm install -D @playwright/test
// Create playwright.config.js
// Write E2E tests for critical paths

═══════════════════════════════════════════════════════════════════════════════
✅ RIN CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

DAY 1:
  ☐ Axios setup with interceptors
  ☐ Register page UI complete
  ☐ Register connected to mock API
  ☐ Token interceptor working
  ☐ Register connected to REAL API
  ☐ Global error handler (401, 403, 500)

DAY 2:
  ☐ Register polished (password strength, email validation)
  ☐ Update profile connected to API
  ☐ Task Form page created
  ☐ Task Create connected to real API

DAY 3:
  ☐ Student Profile page created
  ☐ Student connected to real API

DAY 4 (Backend switch):
  ☐ Banking Routes created
  ☐ API tests for all endpoints

DAY 5 (Backend):
  ☐ GitHub Actions CI/CD working
  ☐ Backend deployed to Render

DAY 6 (Backend):
  ☐ Integration tests for full flows
  ☐ Playwright E2E setup

═══════════════════════════════════════════════════════════════════════════════
END OF RIN DOCUMENT
═══════════════════════════════════════════════════════════════════════════════

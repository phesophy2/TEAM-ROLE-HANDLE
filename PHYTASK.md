═══════════════════════════════════════════════════════════════════════════════
     📄 PHY — FRONTEND DEVELOPER (Router + Layout + Error + Protected + Logout)
═══════════════════════════════════════════════════════════════════════════════

Role: Frontend Developer — Router + Layout + QA
Team: FRONTEND
Tasks: React Router, Auth layout, Error UI, CORS config, Protected routes, Logout

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 1 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Setup React Router + layout (08:30-09:00)
─────────────────────────────────────────────────
// src/App.jsx — Router setup
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import { AuthProvider } from './context/AuthContext';
import { useAuth } from './hooks/useAuth';

// Pages
import Login from './pages/auth/Login';
import Register from './pages/auth/Register';
import Dashboard from './pages/dashboard/Dashboard';
import TaskList from './pages/tasks/TaskList';
import TaskForm from './pages/tasks/TaskForm';
import BugList from './pages/bugs/BugList';
import MentorList from './pages/mentors/MentorList';
import MentorProfile from './pages/mentors/MentorProfile';
import StudentProfile from './pages/students/StudentProfile';
import CommunityFeed from './pages/community/CommunityFeed';
import CreatePost from './pages/community/CreatePost';
import AdminPanel from './pages/admin/AdminPanel';

// Protected Route component
const ProtectedRoute = ({ children, allowedRoles = [] }) => {
  const { user, loading } = useAuth();

  if (loading) return <div>Loading...</div>;
  if (!user) return <Navigate to="/login" />;
  if (allowedRoles.length > 0 && !allowedRoles.includes(user.user_type_id)) {
    return <Navigate to="/dashboard" />;
  }

  return children;
};

function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Routes>
          {/* Public routes */}
          <Route path="/login" element={<Login />} />
          <Route path="/register" element={<Register />} />

          {/* Protected routes */}
          <Route path="/dashboard" element={
            <ProtectedRoute><Dashboard /></ProtectedRoute>
          } />
          <Route path="/tasks" element={
            <ProtectedRoute><TaskList /></ProtectedRoute>
          } />
          <Route path="/tasks/new" element={
            <ProtectedRoute><TaskForm /></ProtectedRoute>
          } />
          <Route path="/bugs" element={
            <ProtectedRoute><BugList /></ProtectedRoute>
          } />
          <Route path="/mentors" element={
            <ProtectedRoute><MentorList /></ProtectedRoute>
          } />
          <Route path="/mentors/:id" element={
            <ProtectedRoute><MentorProfile /></ProtectedRoute>
          } />
          <Route path="/profile" element={
            <ProtectedRoute><StudentProfile /></ProtectedRoute>
          } />
          <Route path="/community" element={
            <ProtectedRoute><CommunityFeed /></ProtectedRoute>
          } />
          <Route path="/community/new" element={
            <ProtectedRoute><CreatePost /></ProtectedRoute>
          } />

          {/* Admin only */}
          <Route path="/admin" element={
            <ProtectedRoute allowedRoles={[1]}>
              <AdminPanel />
            </ProtectedRoute>
          } />

          {/* Default redirect */}
          <Route path="/" element={<Navigate to="/dashboard" />} />
          <Route path="*" element={<div>404 - Page Not Found</div>} />
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  );
}

export default App;

TASK 2: Auth layout (centered card, background) (09:00-10:30)
────────────────────────────────────────────────────────────
// src/layouts/AuthLayout.jsx
const AuthLayout = ({ children }) => {
  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 via-indigo-50 to-purple-50 
                    flex items-center justify-center p-4 relative overflow-hidden">
      {/* Decorative elements */}
      <div className="absolute top-10 left-10 w-32 h-32 bg-blue-200 rounded-full opacity-20 blur-xl"></div>
      <div className="absolute bottom-10 right-10 w-48 h-48 bg-purple-200 rounded-full opacity-20 blur-xl"></div>
      
      <div className="w-full max-w-md relative z-10">
        <div className="bg-white/80 backdrop-blur-sm rounded-2xl shadow-xl p-8 border border-white/50">
          <div className="text-center mb-8">
            <div className="w-16 h-16 bg-blue-600 rounded-xl mx-auto mb            flex items-center justify-center mb-4">
              <span className="text-3xl">🎓</span>
            </div>
            <h1 className="text-2xl font-bold text-gray-900">Student Mentor</h1>
            <p className="text-gray-500 mt-1">Connect. Learn. Grow.</p>
          </div>
          {children}
        </div>
      </div>
    </div>
  );
};

export default AuthLayout;

// Update Login.jsx and Register.jsx to use AuthLayout
// Wrap their JSX with <AuthLayout>...</AuthLayout>

TASK 3: Error handling UI (error messages, loading) (10:30-12:00)
──────────────────────────────────────────────────────────────────
// src/components/ErrorMessage.jsx
const ErrorMessage = ({ message, onRetry }) => (
  <div className="bg-red-50 border-l-4 border-red-500 p-4 rounded-r-lg mb-4 animate-fade-in">
    <div className="flex items-start">
      <span className="text-red-500 text-xl mr-3">⚠️</span>
      <div className="flex-1">
        <p className="text-red-700 font-medium">{message || 'Something went wrong'}</p>
        {onRetry && (
          <button 
            onClick={onRetry}
            className="mt-2 text-sm text-red-600 hover:text-red-800 underline font-medium"
          >
            Try again
          </button>
        )}
      </div>
    </div>
  </div>
);

export default ErrorMessage;

// src/components/Loading.jsx
const Loading = ({ size = 'md' }) => {
  const sizes = { sm: 'h-6 w-6', md: 'h-12 w-12', lg: 'h-16 w-16' };
  
  return (
    <div className="flex items-center justify-center h-64">
      <div className={`${sizes[size]} animate-spin rounded-full border-b-2 border-blue-600`}></div>
    </div>
  );
};

export default Loading;

// src/components/Skeleton.jsx
const Skeleton = ({ className = '' }) => (
  <div className={`animate-pulse bg-gray-200 rounded ${className}`}></div>
);

export default Skeleton;

// Add to index.css
@keyframes fade-in {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-fade-in { animation: fade-in 0.3s ease-out; }

TASK 4: Handle CORS — Prepare config (13:00-14:30)
──────────────────────────────────────────────────
// vite.config.js — Add proxy for development
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true,
      }
    }
  }
});

// .env (frontend)
VITE_API_BASE_URL=http://localhost:5000/api

// For production, CORS is handled by backend (HUN on Day 5)

TASK 5: Protected routes (need token to access) (14:30-16:00)
────────────────────────────────────────────────────────────
// Already done in App.jsx above with ProtectedRoute component
// This checks:
// 1. If user is loading → show loading
// 2. If no user → redirect to login
// 3. If role doesn't match → redirect to dashboard

TASK 6: Logout functionality (clear token, redirect) (16:00-17:30)
─────────────────────────────────────────────────────────────────
// Add to AuthContext.jsx (work with RIN)
const logout = () => {
  localStorage.removeItem('token');
  localStorage.removeItem('user');
  setUser(null);
  window.location.href = '/login';
};

// Add logout button to MainLayout.jsx (work with HUN)
// Already included in HUN's MainLayout

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 2 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Polish Dashboard (sidebar, navigation, user menu) (08:30-10:00)
──────────────────────────────────────────────────────────────────────
// Enhance MainLayout with better sidebar and user menu
// src/layouts/MainLayout.jsx (enhanced version)

import { useState } from 'react';
import { Link, useNavigate, useLocation } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';

const MainLayout = ({ children }) => {
  const [sidebarOpen, setSidebarOpen] = useState(false);
  const [userMenuOpen, setUserMenuOpen] = useState(false);
  const navigate = useNavigate();
  const location = useLocation();
  const { user, logout } = useAuth();

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  const navItems = [
    { path: '/dashboard', label: 'Dashboard', icon: '📊' },
    { path: '/tasks', label: 'Tasks', icon: '📋' },
    { path: '/bugs', label: 'Bugs', icon: '🐛' },
    { path: '/mentors', label: 'Mentors', icon: '👨‍🏫' },
    { path: '/community', label: 'Community', icon: '💬' },
  ];

  if (user?.user_type_id === 1) {
    navItems.push({ path: '/admin', label: 'Admin', icon: '⚙️' });
  }

  return (
    <div className="min-h-screen bg-gray-50 flex">
      {/* Mobile sidebar overlay */}
      {sidebarOpen && (
        <div 
          className="fixed inset-0 bg-black/50 z-40 md:hidden"
          onClick={() => setSidebarOpen(false)}
        />
      )}

      {/* Sidebar */}
      <aside className={`
        fixed md:static inset-y-0 left-0 z-50 w-64 bg-white shadow-lg transform transition-transform
        ${sidebarOpen ? 'translate-x-0' : '-translate-x-full md:translate-x-0'}
      `}>
        <div className="p-6 border-b flex justify-between items-center">
          <h2 className="text-xl font-bold text-blue-600">Student Mentor</h2>
          <button onClick={() => setSidebarOpen(false)} className="md:hidden">✕</button>
        </div>
        
        <nav className="p-4 space-y-1">
          {navItems.map(item => (
            <Link
              key={item.path}
              to={item.path}
              onClick={() => setSidebarOpen(false)}
              className={`flex items-center px-4 py-3 rounded-lg transition-colors ${
                location.pathname === item.path
                  ? 'bg-blue-50 text-blue-600 font-medium'
                  : 'text-gray-700 hover:bg-gray-50'
              }`}
            >
              <span className="mr-3 text-lg">{item.icon}</span>
              {item.label}
            </Link>
          ))}
        </nav>
      </aside>

      {/* Main Content */}
      <div className="flex-1 flex flex-col min-w-0">
        {/* Top Navbar */}
        <header className="bg-white shadow-sm px-4 md:px-6 py-4 flex justify-between items-center">
          <button 
            onClick={() => setSidebarOpen(true)}
            className="md:hidden p-2 text-gray-600"
          >
            ☰
          </button>
          
          <h2 className="text-lg font-semibold hidden md:block">
            {navItems.find(n => n.path === location.pathname)?.label || 'Dashboard'}
          </h2>

          {/* User Menu */}
          <div className="relative">
            <button 
              onClick={() => setUserMenuOpen(!userMenuOpen)}
              className="flex items-center gap-2 text-gray-700 hover:text-gray-900"
            >
              <div className="w-8 h-8 bg-blue-100 rounded-full flex items-center justify-center">
                {user?.full_name?.[0]?.toUpperCase() || 'U'}
              </div>
              <span className="hidden md:block">{user?.full_name}</span>
              <span>▼</span>
            </button>

            {userMenuOpen && (
              <div className="absolute right-0 mt-2 w-48 bg-white rounded-lg shadow-lg border py-1 z-50">
                <Link 
                  to="/profile" 
                  className="block px-4 py-2 text-gray-700 hover:bg-gray-50"
                  onClick={() => setUserMenuOpen(false)}
                >
                  Profile
                </Link>
                <button 
                  onClick={() => {
                    setUserMenuOpen(false);
                    handleLogout();
                  }}
                  className="block w-full text-left px-4 py-2 text-red-600 hover:bg-red-50"
                >
                  Logout
                </button>
              </div>
            )}
          </div>
        </header>

        <main className="flex-1 p-4 md:p-6 overflow-auto">
          {children}
        </main>
      </div>
    </div>
  );
};

export default MainLayout;

TASK 2: Responsive check (mobile login/register) (10:00-12:00)
──────────────────────────────────────────────────────────────
// All auth pages already responsive with Tailwind
// Test on mobile:
// - Login page: w-full max-w-md (responsive width)
// - Register page: same
// - Dashboard: sidebar hidden on mobile, hamburger menu

// Add mobile-specific improvements
// src/components/MobileBottomNav.jsx
import { Link, useLocation } from 'react-router-dom';

const MobileBottomNav = () => {
  const location = useLocation();
  
  const items = [
    { path: '/dashboard', label: 'Home', icon: '🏠' },
    { path: '/tasks', label: 'Tasks', icon: '📋' },
    { path: '/mentors', label: 'Mentors', icon: '👨‍🏫' },
    { path: '/community', label: 'Community', icon: '💬' },
  ];

  return (
    <nav className="md:hidden fixed bottom-0 left-0 right-0 bg-white border-t z-50">
      <div className="flex justify-around py-2">
        {items.map(item => (
          <Link
            key={item.path}
            to={item.path}
            className={`flex flex-col items-center py-1 px-3 ${
              location.pathname === item.path ? 'text-blue-600' : 'text-gray-500'
            }`}
          >
            <span className="text-xl">{item.icon}</span>
            <span className="text-xs mt-1">{item.label}</span>
          </Link>
        ))}
      </div>
    </nav>
  );
};

export default MobileBottomNav;

TASK 3: Bug List page (mock data) (13:00-14:30)
────────────────────────────────────────────────
// src/pages/bugs/BugList.jsx
import { useState, useEffect } from 'react';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const BugList = () => {
  const [bugs, setBugs] = useState([]);
  const [loading, setLoading] = useState(true);

  const mockBugs = [
    { id: 1, title: 'Login button not working', status: 'open', priority: 'high', reporter: 'John' },
    { id: 2, title: 'Page crashes on mobile', status: 'in_progress', priority: 'critical', reporter: 'Jane' },
    { id: 3, title: 'Dark mode not saving', status: 'resolved', priority: 'low', reporter: 'Bob' },
  ];

  useEffect(() => {
    setTimeout(() => {
      setBugs(mockBugs);
      setLoading(false);
    }, 500);
  }, []);

  const getStatusColor = (status) => ({
    open: 'bg-red-100 text-red-800',
    in_progress: 'bg-yellow-100 text-yellow-800',
    resolved: 'bg-green-100 text-green-800',
    closed: 'bg-gray-100 text-gray-800'
  }[status] || 'bg-gray-100');

  if (loading) return <Loading />;

  return (
    <MainLayout>
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h1 className="text-2xl font-bold">Bug Reports</h1>
          <button className="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-700">
            + Report Bug
          </button>
        </div>

        <div className="bg-white rounded-xl shadow-md overflow-hidden">
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead className="bg-gray-50">
                <tr>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">ID</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Title</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Status</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Priority</th>
                  <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Reporter</th>
                </tr>
              </thead>
              <tbody className="divide-y divide-gray-200">
                {bugs.map(bug => (
                  <tr key={bug.id} className="hover:bg-gray-50">
                    <td className="px-6 py-4 text-sm text-gray-900">#{bug.id}</td>
                    <td className="px-6 py-4 text-sm text-gray-900">{bug.title}</td>
                    <td className="px-6 py-4">
                      <span className={`px-2 py-1 rounded-full text-xs font-medium ${getStatusColor(bug.status)}`}>
                        {bug.status.replace('_', ' ')}
                      </span>
                    </td>
                    <td className="px-6 py-4">
                      <span className={`px-2 py-1 rounded-full text-xs font-medium ${
                        bug.priority === 'critical' ? 'bg-red-100 text-red-800' :
                        bug.priority === 'high' ? 'bg-orange-100 text-orange-800' :
                        'bg-blue-100 text-blue-800'
                      }`}>
                        {bug.priority}
                      </span>
                    </td>
                    <td className="px-6 py-4 text-sm text-gray-500">{bug.reporter}</td>
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

export default BugList;

TASK 4: Connect Bug List to real API (14:30-17:30)
─────────────────────────────────────────────────
// Replace mock data with API call
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';
import ErrorMessage from '../../components/ErrorMessage';

const BugList = () => {
  const [bugs, setBugs] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState('');

  useEffect(() => {
    fetchBugs();
  }, []);

  const fetchBugs = async () => {
    try {
      setLoading(true);
      setError('');
      const response = await api.get('/bugs');
      setBugs(response.data);
    } catch (err) {
      setError(err.response?.data?.message || 'Failed to load bugs');
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <Loading />;
  
  return (
    <MainLayout>
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h1 className="text-2xl font-bold">Bug Reports</h1>
          <button className="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-700">
            + Report Bug
          </button>
        </div>

        {error && <ErrorMessage message={error} onRetry={fetchBugs} />}

        {/* ... rest same as above */}
      </div>
    </MainLayout>
  );
};

export default BugList;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 3 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Community Feed page + Create Post page (08:30-12:00)
───────────────────────────────────────────────────────────
// src/pages/community/CommunityFeed.jsx
import { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const CommunityFeed = () => {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchPosts();
  }, []);

  const fetchPosts = async () => {
    try {
      const response = await api.get('/community-posts');
      setPosts(response.data);
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
        <div className="flex justify-between items-center">
          <h1 className="text-2xl font-bold">Community Feed</h1>
          <Link 
            to="/community/new"
            className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700"
          >
            + New Post
          </Link>
        </div>

        <div className="space-y-4">
          {posts.map(post => (
            <div key={post.id} className="bg-white p-6 rounded-xl shadow-md">
              <div className="flex items-center gap-3 mb-3">
                <div className="w-10 h-10 bg-gray-200 rounded-full flex items-center justify-center">
                  {post.author?.[0]?.toUpperCase() || 'U'}
                </div>
                <div>
                  <p className="font-medium">{post.author || 'Unknown'}</p>
                  <p className="text-sm text-gray-500">
                    {new Date(post.created_at).toLocaleDateString()}
                  </p>
                </div>
              </div>
              <h3 className="text-lg font-semibold mb-2">{post.title}</h3>
              <p className="text-gray-600">{post.content}</p>
            </div>
          ))}
        </div>
      </div>
    </MainLayout>
  );
};

export default CommunityFeed;

// src/pages/community/CreatePost.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';

const CreatePost = () => {
  const [formData, setFormData] = useState({ title: '', content: '' });
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    
    try {
      await api.post('/community-posts', formData);
      navigate('/community');
    } catch (err) {
      alert(err.response?.data?.message || 'Failed to create post');
    } finally {
      setLoading(false);
    }
  };

  return (
    <MainLayout>
      <div className="max-w-2xl mx-auto">
        <div className="bg-white p-8 rounded-xl shadow-md">
          <h1 className="text-2xl font-bold mb-6">Create New Post</h1>
          
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
              <label className="block text-sm font-medium text-gray-700 mb-1">Content</label>
              <textarea
                value={formData.content}
                onChange={(e) => setFormData({ ...formData, content: e.target.value })}
                className="w-full px-4 py-2 border border-gray-300 rounded-lg h-40"
                required
              />
            </div>
            
            <div className="flex gap-4">
              <button
                type="button"
                onClick={() => navigate('/community')}
                className="flex-1 bg-gray-200 text-gray-800 py-2 rounded-lg hover:bg-gray-300"
              >
                Cancel
              </button>
              <button
                type="submit"
                disabled={loading}
                className="flex-1 bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 disabled:opacity-50"
              >
                {loading ? 'Posting...' : 'Post'}
              </button>
            </div>
          </form>
        </div>
      </div>
    </MainLayout>
  );
};

export default CreatePost;

TASK 2: Connect Community to real API (13:00-17:30)
────────────────────────────────────────────────────
// Already connected in CommunityFeed.jsx and CreatePost.jsx above

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 4 TASKS — PHY switches to BACKEND (Transaction Routes + Pagination)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Transaction Routes — GET/POST /api/transactions (08:30-12:00)
───────────────────────────────────────────────────────────────────────
// routes/transactionRoutes.js
const express = require('express');
const router = express.Router();
const pool = require('../database/db');
const { verifyToken } = require('../middleware/auth');

// GET /api/transactions (with pagination)
router.get('/', verifyToken, async (req, res, next) => {
  try {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const offset = (page - 1) * limit;

    const countResult = await pool.query('SELECT COUNT(*) FROM transaction_detail WHERE user_id = $1', [req.user.id]);
    const total = parseInt(countResult.rows[0].count);

    const result = await pool.query(
      `SELECT * FROM transaction_detail 
       WHERE user_id = $1 
       ORDER BY created_at DESC 
       LIMIT $2 OFFSET $3`,
      [req.user.id, limit, offset]
    );

    res.json({
      data: result.rows,
      pagination: {
        page,
        limit,
        total,
        totalPages: Math.ceil(total / limit)
      }
    });
  } catch (error) {
    next(error);
  }
});

// POST /api/transactions
router.post('/', verifyToken, async (req, res, next) => {
  try {
    const { amount, type, description } = req.body;
    
    const result = await pool.query(
      `INSERT INTO transaction_detail (user_id, amount, type, description)
       VALUES ($1, $2, $3, $4) RETURNING *`,
      [req.user.id, amount, type, description]
    );
    
    res.status(201).json(result.rows[0]);
  } catch (error) {
    next(error);
  }
});

module.exports = router;

TASK 2: Add pagination, search, filter to all routes (13:00-17:30)
───────────────────────────────────────────────────────────────────
// middleware/pagination.js
const paginate = (defaultLimit = 10) => {
  return (req, res, next) => {
    req.pagination = {
      page: Math.max(1, parseInt(req.query.page) || 1),
      limit: Math.min(100, Math.max(1, parseInt(req.query.limit) || defaultLimit)),
      offset: (Math.max(1, parseInt(req.query.page) || 1) - 1) * Math.min(100, Math.max(1, parseInt(req.query.limit) || defaultLimit))
    };
    next();
  };
};

// middleware/search.js
const search = (allowedFields) => {
  return (req, res, next) => {
    req.search = {};
    if (req.query.q) {
      req.search.query = req.query.q;
      req.search.fields = allowedFields;
    }
    next();
  };
};

// Apply to routes:
// router.get('/', verifyToken, paginate(), search(['title', 'description']), controller);

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 5 TASKS — PHY stays on BACKEND (Error Handling + Performance)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Error handling standardization (08:30-12:00)
───────────────────────────────────────────────────
// middleware/errorHandler.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

const globalErrorHandler = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';

  if (process.env.NODE_ENV === 'development') {
    res.status(err.statusCode).json({
      status: err.status,
      message: err.message,
      stack: err.stack,
      error: err
    });
  } else {
    // Production
    if (err.isOperational) {
      res.status(err.statusCode).json({
        status: err.status,
        message: err.message
      });
    } else {
      console.error('ERROR 💥', err);
      res.status(500).json({
        status: 'error',
        message: 'Something went wrong!'
      });
    }
  }
};

module.exports = { AppError, globalErrorHandler };

TASK 2: Performance optimization (13:00-17:30)
─────────────────────────────────────────────
// Add compression
const compression = require('compression');
app.use(compression());

// Add response caching
const cache = require('memory-cache');
const cacheMiddleware = (duration = 300) => {
  return (req, res, next) => {
    const key = req.originalUrl || req.url;
    const cachedBody = cache.get(key);
    
    if (cachedBody) {
      res.send(cachedBody);
      return;
    }
    
    res.sendResponse = res.send;
    res.send = (body) => {
      cache.put(key, body, duration * 1000);
      res.sendResponse(body);
    };
    next();
  };
};

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 6 TASKS — PHY stays on BACKEND (API Docs + Review)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: API documentation (Postman collection) (08:30-12:00)
───────────────────────────────────────────────────────────
// Export Postman collection with all endpoints
// Include: Auth, Users, Tasks, Bugs, Mentors, Students, Community, Subscriptions, Transactions, Banking

TASK 2: Review all API docs (13:00-17:30)
────────────────────────────────────────
// Verify all endpoints are documented
// Verify request/response examples are correct
// Share with team

═══════════════════════════════════════════════════════════════════════════════
✅ PHY CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

DAY 1:
  ☐ React Router setup with all routes
  ☐ AuthLayout created (centered card, gradient background)
  ☐ ErrorMessage component created
  ☐ Loading component created
  ☐ CORS config prepared (vite proxy)
  ☐ ProtectedRoute working (redirects if no token)
  ☐ Logout clears token and redirects

DAY 2:
  ☐ Dashboard polished (sidebar, user menu, mobile responsive)
  ☐ Mobile responsive check passed
  ☐ Bug List page with mock data
  ☐ Bug List connected to real API

DAY 3:
  ☐ Community Feed page created
  ☐ Create Post page created
  ☐ Both connected to real API

DAY 4 (Backend switch):
  ☐ Transaction Routes created with pagination
  ☐ Pagination middleware reusable
  ☐ Search middleware reusable

DAY 5 (Backend):
  ☐ Error handling standardized
  ☐ Performance optimization applied

DAY 6 (Backend):
  ☐ Postman collection complete
  ☐ API docs reviewed

═══════════════════════════════════════════════════════════════════════════════
END OF PHY DOCUMENT
═══════════════════════════════════════════════════════════════════════════════

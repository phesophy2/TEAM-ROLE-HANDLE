═══════════════════════════════════════════════════════════════════════════════
     📄 HUN — FRONTEND DEVELOPER (React + Vite + Tailwind + Pages)
═══════════════════════════════════════════════════════════════════════════════

Role: Frontend Developer — UI Lead + Pages
Team: FRONTEND
Tasks: React setup, Login UI, Dashboard, API integration, Polish

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 1 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Setup React + Vite + Tailwind (08:30-09:00)
─────────────────────────────────────────────────
npm create vite@latest frontend -- --template react
cd frontend
npm install
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

// tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: { extend: {} },
  plugins: [],
}

// src/index.css
@tailwind base;
@tailwind components;
@tailwind utilities;

TASK 2: Login page UI (09:00-10:30)
───────────────────────────────────
// src/pages/auth/Login.jsx
import { useState } from 'react';
import { Link } from 'react-router-dom';

const Login = () => {
  const [formData, setFormData] = useState({ email: '', password: '' });
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
    if (error) setError('');
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    // Will connect to API later
    console.log('Login:', formData);
    setLoading(false);
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gradient-to-br from-blue-50 to-indigo-100">
      <div className="bg-white p-8 rounded-2xl shadow-xl w-full max-w-md">
        <h2 className="text-2xl font-bold text-center text-gray-800 mb-6">Welcome Back</h2>
        
        {error && (
          <div className="bg-red-50 text-red-600 p-3 rounded-lg mb-4 text-sm">
            {error}
          </div>
        )}

        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Email</label>
            <input
              type="email"
              name="email"
              value={formData.email}
              onChange={handleChange}
              className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none"
              placeholder="your@email.com"
              required
            />
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">Password</label>
            <input
              type="password"
              name="password"
              value={formData.password}
              onChange={handleChange}
              className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none"
              placeholder="••••••••"
              required
            />
          </div>

          <button
            type="submit"
            disabled={loading}
            className="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2.5 rounded-lg transition-colors disabled:opacity-50"
          >
            {loading ? 'Signing in...' : 'Sign In'}
          </button>
        </form>

        <p className="text-center text-gray-600 mt-4 text-sm">
          Don't have an account?{' '}
          <Link to="/register" className="text-blue-600 hover:underline">Register</Link>
        </p>
      </div>
    </div>
  );
};

export default Login;

TASK 3: Connect Login UI to mock API (10:30-12:00)
─────────────────────────────────────────────────
// Create mock API for testing before backend ready
// src/services/mockApi.js
export const mockLogin = async (email, password) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (email === 'test@test.com' && password === 'password123') {
        resolve({
          token: 'mock_jwt_token_12345',
          user: { id: 1, email, full_name: 'Test User', user_type_id: 2 }
        });
      } else {
        reject(new Error('Invalid email or password'));
      }
    }, 500);
  });
};

// Update Login.jsx to use mock API
import { mockLogin } from '../../services/mockApi';

const handleSubmit = async (e) => {
  e.preventDefault();
  setLoading(true);
  setError('');
  
  try {
    const response = await mockLogin(formData.email, formData.password);
    localStorage.setItem('token', response.token);
    localStorage.setItem('user', JSON.stringify(response.user));
    window.location.href = '/dashboard';
  } catch (err) {
    setError(err.message);
  } finally {
    setLoading(false);
  }
};

TASK 4: Dashboard layout (13:00-14:30)
────────────────────────────────────
// src/layouts/MainLayout.jsx
import { Link, useNavigate } from 'react-router-dom';

const MainLayout = ({ children }) => {
  const navigate = useNavigate();
  const user = JSON.parse(localStorage.getItem('user') || '{}');

  const handleLogout = () => {
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    navigate('/login');
  };

  const navItems = [
    { path: '/dashboard', label: 'Dashboard', icon: '📊' },
    { path: '/tasks', label: 'Tasks', icon: '📋' },
    { path: '/bugs', label: 'Bugs', icon: '🐛' },
    { path: '/mentors', label: 'Mentors', icon: '👨‍🏫' },
    { path: '/community', label: 'Community', icon: '💬' },
  ];

  return (
    <div className="min-h-screen bg-gray-50 flex">
      {/* Sidebar */}
      <aside className="w-64 bg-white shadow-lg hidden md:block">
        <div className="p-6 border-b">
          <h2 className="text-xl font-bold text-blue-600">Student Mentor</h2>
        </div>
        <nav className="p-4 space-y-1">
          {navItems.map(item => (
            <Link key={item.path} to={item.path}
              className="flex items-center px-4 py-3 text-gray-700 rounded-lg hover:bg-blue-50 hover:text-blue-600 transition-colors">
              <span className="mr-3">{item.icon}</span>
              {item.label}
            </Link>
          ))}
        </nav>
      </aside>

      {/* Main Content */}
      <div className="flex-1 flex flex-col">
        <header className="bg-white shadow-sm px-6 py-4 flex justify-between items-center">
          <h2 className="text-lg font-semibold">Dashboard</h2>
          <div className="flex items-center gap-4">
            <span className="text-gray-600">{user?.full_name || 'User'}</span>
            <button onClick={handleLogout} className="text-red-600 hover:text-red-700 font-medium">
              Logout
            </button>
          </div>
        </header>
        <main className="flex-1 p-6 overflow-auto">{children}</main>
      </div>
    </div>
  );
};

export default MainLayout;

// src/pages/dashboard/Dashboard.jsx
import MainLayout from '../../layouts/MainLayout';

const Dashboard = () => {
  const user = JSON.parse(localStorage.getItem('user') || '{}');

  return (
    <MainLayout>
      <div className="space-y-6">
        <h1 className="text-3xl font-bold">Welcome, {user?.full_name || 'User'}!</h1>
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div className="bg-white p-6 rounded-xl shadow-md">
            <h3 className="text-lg font-semibold mb-2">My Tasks</h3>
            <p className="text-3xl font-bold text-blue-600">0</p>
          </div>
          <div className="bg-white p-6 rounded-xl shadow-md">
            <h3 className="text-lg font-semibold mb-2">My Mentors</h3>
            <p className="text-3xl font-bold text-blue-600">0</p>
          </div>
          <div className="bg-white p-6 rounded-xl shadow-md">
            <h3 className="text-lg font-semibold mb-2">Community Posts</h3>
            <p className="text-3xl font-bold text-blue-600">0</p>
          </div>
        </div>
      </div>
    </MainLayout>
  );
};

export default Dashboard;

TASK 5: Connect Login to REAL API (14:30-16:00)
───────────────────────────────────────────────
// Use RIN's axios setup (src/services/api.js)
// Update Login.jsx to use real API
import api from '../../services/api';

const handleSubmit = async (e) => {
  e.preventDefault();
  setLoading(true);
  setError('');

  try {
    const response = await api.post('/auth/login', {
      email: formData.email,
      password: formData.password
    });
    
    const { token, user } = response.data;
    localStorage.setItem('token', token);
    localStorage.setItem('user', JSON.stringify(user));
    window.location.href = '/dashboard';
  } catch (err) {
    setError(err.response?.data?.message || 'Login failed');
  } finally {
    setLoading(false);
  }
};

TASK 6: Connect /api/users/me to show user info (16:00-17:30)
─────────────────────────────────────────────────────────────
// Update Dashboard to fetch real user data
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const Dashboard = () => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser();
  }, []);

  const fetchUser = async () => {
    try {
      const response = await api.get('/users/me');
      setUser(response.data.user);
      localStorage.setItem('user', JSON.stringify(response.data.user));
    } catch (err) {
      console.error('Failed to fetch user:', err);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <Loading />;

  return (
    <MainLayout>
      <div className="space-y-6">
        <h1 className="text-3xl font-bold">Welcome, {user?.full_name}!</h1>
        <p className="text-gray-600">Email: {user?.email}</p>
        <p className="text-gray-600">Role: {user?.user_type}</p>
      </div>
    </MainLayout>
  );
};

export default Dashboard;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 2 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Polish Login page (animations, validation feedback) (08:30-10:00)
───────────────────────────────────────────────────────────────────────
// Add animations and better validation feedback
// src/pages/auth/Login.jsx (updated)

// Add CSS animations to index.css
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.animate-shake { animation: shake 0.3s ease-in-out; }
.animate-fade-in { animation: fadeIn 0.5s ease-in; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(-10px); } to { opacity: 1; transform: translateY(0); } }

// Add password visibility toggle and better validation
const Login = () => {
  const [showPassword, setShowPassword] = useState(false);
  const [touched, setTouched] = useState({});

  // ... existing state

  const validateField = (name, value) => {
    if (name === 'email') {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? '' : 'Invalid email format';
    }
    if (name === 'password') {
      return value.length >= 6 ? '' : 'Password must be at least 6 characters';
    }
    return '';
  };

  const handleBlur = (e) => {
    setTouched({ ...touched, [e.target.name]: true });
  };

  // In JSX, add to input:
  // className={`... ${touched.email && errors.email ? 'border-red-500 animate-shake' : ''}`}

TASK 2: Role-based dashboard (admin/student/mentor views) (10:00-12:00)
───────────────────────────────────────────────────────────────────────
// src/pages/dashboard/Dashboard.jsx (role-based)

const Dashboard = () => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser();
  }, []);

  const fetchUser = async () => {
    try {
      const response = await api.get('/users/me');
      setUser(response.data.user);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <Loading />;

  // Render different dashboard based on role
  const renderDashboard = () => {
    switch (user?.user_type_id) {
      case 1: return <AdminDashboard user={user} />;
      case 2: return <StudentDashboard user={user} />;
      case 3: return <MentorDashboard user={user} />;
      default: return <div>Unknown role</div>;
    }
  };

  return (
    <MainLayout>
      <div className="animate-fade-in">
        {renderDashboard()}
      </div>
    </MainLayout>
  );
};

// Admin Dashboard
const AdminDashboard = ({ user }) => (
  <div className="space-y-6">
    <h1 className="text-3xl font-bold">Admin Dashboard</h1>
    <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
      <div className="bg-white p-6 rounded-xl shadow-md border-l-4 border-blue-500">
        <h3 className="text-gray-500 text-sm">Total Users</h3>
        <p className="text-3xl font-bold text-gray-800">--</p>
      </div>
      <div className="bg-white p-6 rounded-xl shadow-md border-l-4 border-green-500">
        <h3 className="text-gray-500 text-sm">Students</h3>
        <p className="text-3xl font-bold text-gray-800">--</p>
      </div>
      <div className="bg-white p-6 rounded-xl shadow-md border-l-4 border-purple-500">
        <h3 className="text-gray-500 text-sm">Mentors</h3>
        <p className="text-3xl font-bold text-gray-800">--</p>
      </div>
      <div className="bg-white p-6 rounded-xl shadow-md border-l-4 border-orange-500">
        <h3 className="text-gray-500 text-sm">Revenue</h3>
        <p className="text-3xl font-bold text-gray-800">$--</p>
      </div>
    </div>
  </div>
);

// Student Dashboard
const StudentDashboard = ({ user }) => (
  <div className="space-y-6">
    <h1 className="text-3xl font-bold">Student Dashboard</h1>
    <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
      <div className="bg-white p-6 rounded-xl shadow-md">
        <h3 className="text-lg font-semibold mb-4">My Tasks</h3>
        <p className="text-gray-600">You have no pending tasks</p>
      </div>
      <div className="bg-white p-6 rounded-xl shadow-md">
        <h3 className="text-lg font-semibold mb-4">My Mentors</h3>
        <p className="text-gray-600">Connect with mentors to get started</p>
      </div>
    </div>
  </div>
);

// Mentor Dashboard
const MentorDashboard = ({ user }) => (
  <div className="space-y-6">
    <h1 className="text-3xl font-bold">Mentor Dashboard</h1>
    <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
      <div className="bg-white p-6 rounded-xl shadow-md">
        <h3 className="text-lg font-semibold mb-4">My Students</h3>
        <p className="text-gray-600">No students assigned yet</p>
      </div>
      <div className="bg-white p-6 rounded-xl shadow-md">
        <h3 className="text-lg font-semibold mb-4">Community Posts</h3>
        <p className="text-gray-600">Share your knowledge with the community</p>
      </div>
    </div>
  </div>
);

TASK 3: Task List page (mock data) (13:00-14:30)
───────────────────────────────────────────────
// src/pages/tasks/TaskList.jsx
import { useState, useEffect } from 'react';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const TaskList = () => {
  const [tasks, setTasks] = useState([]);
  const [loading, setLoading] = useState(true);

  // Mock data for testing
  const mockTasks = [
    { id: 1, title: 'Complete homework', status: 'pending', priority: 'high', due_date: '2024-01-15' },
    { id: 2, title: 'Review mentor feedback', status: 'completed', priority: 'medium', due_date: '2024-01-10' },
    { id: 3, title: 'Prepare presentation', status: 'in_progress', priority: 'high', due_date: '2024-01-20' },
  ];

  useEffect(() => {
    // Simulate API call
    setTimeout(() => {
      setTasks(mockTasks);
      setLoading(false);
    }, 500);
  }, []);

  const getStatusColor = (status) => {
    const colors = {
      pending: 'bg-yellow-100 text-yellow-800',
      completed: 'bg-green-100 text-green-800',
      in_progress: 'bg-blue-100 text-blue-800'
    };
    return colors[status] || 'bg-gray-100 text-gray-800';
  };

  if (loading) return <Loading />;

  return (
    <MainLayout>
      <div className="space-y-6">
        <div className="flex justify-between items-center">
          <h1 className="text-2xl font-bold">My Tasks</h1>
          <button className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700">
            + New Task
          </button>
        </div>

        <div className="bg-white rounded-xl shadow-md overflow-hidden">
          <table className="w-full">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Title</th>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Status</th>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Priority</th>
                <th className="px-6 py-3 text-left text-sm font-medium text-gray-500">Due Date</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-gray-200">
              {tasks.map(task => (
                <tr key={task.id} className="hover:bg-gray-50">
                  <td className="px-6 py-4 text-sm text-gray-900">{task.title}</td>
                  <td className="px-6 py-4">
                    <span className={`px-2 py-1 rounded-full text-xs font-medium ${getStatusColor(task.status)}`}>
                      {task.status}
                    </span>
                  </td>
                  <td className="px-6 py-4 text-sm text-gray-500 capitalize">{task.priority}</td>
                  <td className="px-6 py-4 text-sm text-gray-500">{task.due_date}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>
    </MainLayout>
  );
};

export default TaskList;

TASK 4: Connect Task List to real API (14:30-17:30)
─────────────────────────────────────────────────
// Replace mock data with real API call
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const TaskList = () => {
  const [tasks, setTasks] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState('');

  useEffect(() => {
    fetchTasks();
  }, []);

  const fetchTasks = async () => {
    try {
      setLoading(true);
      const response = await api.get('/tasks');
      setTasks(response.data);
    } catch (err) {
      setError(err.response?.data?.message || 'Failed to fetch tasks');
    } finally {
      setLoading(false);
    }
  };

  // ... rest same as above but use real data

  return (
    <MainLayout>
      <div className="space-y-6">
        {error && (
          <div className="bg-red-50 text-red-600 p-4 rounded-lg">
            {error}
            <button onClick={fetchTasks} className="ml-4 underline">Retry</button>
          </div>
        )}
        {/* ... rest of JSX */}
      </div>
    </MainLayout>
  );
};

export default TaskList;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 3 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Mentor List page + Mentor Profile page (08:30-12:00)
───────────────────────────────────────────────────────────
// src/pages/mentors/MentorList.jsx
import { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const MentorList = () => {
  const [mentors, setMentors] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchMentors();
  }, []);

  const fetchMentors = async () => {
    try {
      const response = await api.get('/mentors');
      setMentors(response.data);
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
        <h1 className="text-2xl font-bold">Mentors</h1>
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {mentors.map(mentor => (
            <Link key={mentor.id} to={`/mentors/${mentor.id}`}
              className="bg-white p-6 rounded-xl shadow-md hover:shadow-lg transition-shadow">
              <div className="flex items-center gap-4 mb-4">
                <div className="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center text-xl">
                  👨‍🏫
                </div>
                <div>
                  <h3 className="font-semibold">{mentor.full_name}</h3>
                  <p className="text-sm text-gray-500">{mentor.expertise}</p>
                </div>
              </div>
              <p className="text-gray-600 text-sm">{mentor.years_experience} years experience</p>
              <p className="text-blue-600 font-medium mt-2">${mentor.hourly_rate}/hour</p>
            </Link>
          ))}
        </div>
      </div>
    </MainLayout>
  );
};

export default MentorList;

// src/pages/mentors/MentorProfile.jsx
import { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const MentorProfile = () => {
  const { id } = useParams();
  const [mentor, setMentor] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchMentor();
  }, [id]);

  const fetchMentor = async () => {
    try {
      const response = await api.get(`/mentors/${id}`);
      setMentor(response.data);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <Loading />;
  if (!mentor) return <div>Mentor not found</div>;

  return (
    <MainLayout>
      <div className="max-w-4xl mx-auto">
        <div className="bg-white rounded-xl shadow-md p-8">
          <div className="flex items-center gap-6 mb-6">
            <div className="w-24 h-24 bg-blue-100 rounded-full flex items-center justify-center text-4xl">
              👨‍🏫
            </div>
            <div>
              <h1 className="text-2xl font-bold">{mentor.full_name}</h1>
              <p className="text-gray-500">{mentor.expertise}</p>
              <div className="flex items-center gap-2 mt-2">
                <span className="text-yellow-500">⭐⭐⭐⭐⭐</span>
                <span className="text-gray-500">({mentor.rating || 0} reviews)</span>
              </div>
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
            <div className="bg-gray-50 p-4 rounded-lg text-center">
              <p className="text-2xl font-bold text-blue-600">{mentor.years_experience}</p>
              <p className="text-sm text-gray-500">Years Experience</p>
            </div>
            <div className="bg-gray-50 p-4 rounded-lg text-center">
              <p className="text-2xl font-bold text-blue-600">${mentor.hourly_rate}</p>
              <p className="text-sm text-gray-500">Hourly Rate</p>
            </div>
            <div className="bg-gray-50 p-4 rounded-lg text-center">
              <p className="text-2xl font-bold text-blue-600">{mentor.students_count || 0}</p>
              <p className="text-sm text-gray-500">Students</p>
            </div>
          </div>

          <div className="mb-6">
            <h2 className="text-lg font-semibold mb-2">About</h2>
            <p className="text-gray-600">{mentor.bio || 'No bio available'}</p>
          </div>

          <div className="mb-6">
            <h2 className="text-lg font-semibold mb-2">Skills</h2>
            <div className="flex flex-wrap gap-2">
              {(mentor.skills || []).map(skill => (
                <span key={skill} className="bg-blue-100 text-blue-700 px-3 py-1 rounded-full text-sm">
                  {skill}
                </span>
              ))}
            </div>
          </div>

          <button className="w-full bg-blue-600 text-white py-3 rounded-lg hover:bg-blue-700 font-medium">
            Book Session
          </button>
        </div>
      </div>
    </MainLayout>
  );
};

export default MentorProfile;

TASK 2: Connect Mentor to real API (13:00-17:30)
───────────────────────────────────────────────
// Already connected in MentorList.jsx and MentorProfile.jsx above
// Just make sure API endpoints match Heing's backend routes

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 4 TASKS — HUN switches to BACKEND (Database + Optimization)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Add foreign keys, indexes, constraints (08:30-12:00)
─────────────────────────────────────────────────────────────
// Work with Rathanak's schema — add indexes
// database/optimization.sql

-- Add indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_user_type ON users(user_type_id);
CREATE INDEX idx_students_user_id ON students(user_id);
CREATE INDEX idx_mentors_user_id ON mentors(user_id);
CREATE INDEX idx_community_posts_user ON community_post(user_id);
CREATE INDEX idx_community_posts_type ON community_post(community_type_id);
CREATE INDEX idx_subscriptions_user ON subscription(user_id);
CREATE INDEX idx_transactions_user ON transaction_detail(user_id);
CREATE INDEX idx_mentor_skills_mentor ON mentor_skill(mentor_id);

-- Add constraints
ALTER TABLE users ADD CONSTRAINT chk_email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');
ALTER TABLE mentors ADD CONSTRAINT chk_hourly_rate_positive CHECK (hourly_rate > 0);

TASK 2: Database optimization (13:00-17:30)
───────────────────────────────────────────
// Connection pooling optimization
// Update database/db.js

const { Pool } = require('pg');
require('dotenv').config();

const pool = new Pool({
    connectionString: process.env.DATABASE_URL,
    max: 20,                    // Maximum connections
    idleTimeoutMillis: 30000,   // Close idle after 30s
    connectionTimeoutMillis: 2000, // Timeout after 2s
    ssl: process.env.NODE_ENV === 'production' ? { rejectUnauthorized: false } : false
});

pool.on('connect', () => console.log('✅ New client connected'));
pool.on('error', (err) => console.error('❌ Pool error:', err));

module.exports = pool;

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 5 TASKS — HUN stays on BACKEND (CORS + Security)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Handle CORS - Final config (08:30-12:00)
────────────────────────────────────────────────
// Update server.js CORS configuration

const express = require('express');
const cors = require('cors');

const app = express();

// CORS configuration
const corsOptions = {
    origin: process.env.CORS_ORIGIN || 'http://localhost:5173',
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With']
};

app.use(cors(corsOptions));

TASK 2: Security audit (13:00-17:30)
───────────────────────────────────
// Add security middleware
// middleware/security.js

const helmet = require('helmet');
const rateLimit = require('express-rate-limit');

// Rate limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP'
});

// SQL Injection prevention helper
const sanitizeInput = (input) => {
    if (typeof input !== 'string') return input;
    return input.replace(/['";\\]/g, '');
};

// XSS prevention helper
const escapeHtml = (unsafe) => {
    return unsafe
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;");
};

module.exports = { limiter, sanitizeInput, escapeHtml };

// Add to server.js:
// const { limiter } = require('./middleware/security');
// app.use(limiter);
// app.use(helmet());

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 6 TASKS — HUN stays on BACKEND (Backup + Rate Limiting)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Database backup strategy (08:30-12:00)
────────────────────────────────────────────
// scripts/backup.js
const { exec } = require('child');
const path = require('path');

const backup = () => {
    const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
    const filename = `backup-${timestamp}.sql`;
    const cmd = `pg_dump ${process.env.DATABASE_URL} > ./backups/${filename}`;
    
    exec(cmd, (error) => {
        if (error) console.error('Backup failed:', error);
        else console.log(`✅ Backup saved: ${filename}`);
    });
};

// Run daily with cron or setInterval
setInterval(backup, 24 * 60 * 60 * 1000); // Daily

TASK 2: Rate limiting + security headers (13:00-17:30)
─────────────────────────────────────────────────────
// Already covered in Day 5 — finalize and test

═══════════════════════════════════════════════════════════════════════════════
✅ HUN CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

DAY 1 (Frontend):
  ☐ React + Vite + Tailwind installed
  ☐ Login page UI complete with form, button, styling
  ☐ Login connected to mock API
  ☐ Dashboard layout created (sidebar, navbar, main content)
  ☐ Login connected to REAL API
  ☐ /api/users/me connected and showing user info

DAY 2 (Frontend):
  ☐ Login page polished (animations, validation feedback)
  ☐ Role-based dashboard (admin/student/mentor views)
  ☐ Task List page with mock data
  ☐ Task List connected to real API

DAY 3 (Frontend):
  ☐ Mentor List page created
  ☐ Mentor Profile page created
  ☐ Both connected to real API

DAY 4 (Backend — switch):
  ☐ Indexes added to all tables
  ☐ Constraints added
  ☐ DB optimization applied

DAY 5 (Backend):
  ☐ CORS configured correctly
  ☐ Security audit passed
  ☐ Helmet installed
  ☐ Rate limiting working

DAY 6 (Backend):
  ☐ Backup script created
  ☐ Rate limiting tested
  ☐ Security headers working

═══════════════════════════════════════════════════════════════════════════════
🆘 HUN QUICK FIXES
═══════════════════════════════════════════════════════════════════════════════

Problem: Tailwind not working
Fix: Check tailwind.config.js content paths
Fix: Make sure index.css has @tailwind directives

Problem: Login API returns 401
Fix: Check email/password correct
Fix: Check backend server running
Fix: Check CORS origin matches

Problem: Dashboard not showing user info
Fix: Check /api/users/me endpoint working
Fix: Check token saved in localStorage
Fix: Check axios interceptor adds token

Problem: Database slow
Fix: Add indexes to frequently queried columns
Fix: Use EXPLAIN ANALYZE to find slow queries

═══════════════════════════════════════════════════════════════════════════════
END OF HUN DOCUMENT
═══════════════════════════════════════════════════════════════════════════════

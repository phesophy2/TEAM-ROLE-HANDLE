═══════════════════════════════════════════════════════════════════════════════
     📄 RATHANAK — BACKEND DEVELOPER (Database + Schema + Tables)
═══════════════════════════════════════════════════════════════════════════════

Role: Backend Developer — Database Lead
Team: BACKEND
Tasks: PostgreSQL setup, ALL schema tables, Indexes, Constraints

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 1 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Setup PostgreSQL + Create database (08:30-09:00)
───────────────────────────────────────────────────────
# Install PostgreSQL
# Windows: https://www.postgresql.org/download/windows/
# Mac: brew install postgresql
# Linux: sudo apt install postgresql

sudo service postgresql start
sudo -u postgres psql

# In psql:
CREATE DATABASE project_db;
CREATE USER project_user WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE project_db TO project_user;

TASK 2: Schema — Create users table (09:00-10:30)
─────────────────────────────────────────────────
-- database/schema.sql (Part 1)

-- User Type Table
CREATE TABLE user_type (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert default types
INSERT INTO user_type (name) VALUES ('admin'), ('student'), ('mentor');

-- Users Table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    full_name VARCHAR(255),
    user_type_id INTEGER REFERENCES user_type(id) ON DELETE SET NULL,
    phone VARCHAR(20),
    avatar_url TEXT,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 3: Schema — Create user_type table (10:30-12:00)
────────────────────────────────────────────────────
-- Already created above in Part 1

TASK 4: Schema — Create student table (13:00-14:30)
──────────────────────────────────────────────────
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    user_id INTEGER UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    school_name VARCHAR(255),
    grade_level VARCHAR(50),
    parent_phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 5: Schema — Create mentor table (14:30-16:00)
────────────────────────────────────────────────
CREATE TABLE mentors (
    id SERIAL PRIMARY KEY,
    user_id INTEGER UNIQUE REFERENCES users(id) ON DELETE CASCADE,
    expertise VARCHAR(255),
    years_experience INT,
    company VARCHAR(255),
    hourly_rate DECIMAL(10,2),
    is_verified BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 6: Schema — Add indexes, constraints (16:00-17:30)
───────────────────────────────────────────────────────
-- Indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_user_type ON users(user_type_id);
CREATE INDEX idx_students_user_id ON students(user_id);
CREATE INDEX idx_mentors_user_id ON mentors(user_id);

-- Constraints
ALTER TABLE users ADD CONSTRAINT chk_email_format 
  CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');

ALTER TABLE mentors ADD CONSTRAINT chk_hourly_rate_positive 
  CHECK (hourly_rate > 0);

ALTER TABLE students ADD CONSTRAINT chk_grade_level 
  CHECK (grade_level IN ('1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', 'university'));

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 2 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Schema — Create skill, province, community_type tables (08:30-10:00)
───────────────────────────────────────────────────────────────────────────
CREATE TABLE skill (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    category VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE province (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    code VARCHAR(10) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE community_type (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 2: Schema — Create community_post, mentor_post tables (10:00-12:00)
─────────────────────────────────────────────────────────────────────────
CREATE TABLE community_post (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    community_type_id INTEGER REFERENCES community_type(id),
    title VARCHAR(255) NOT NULL,
    content TEXT,
    is_published BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE mentor_post (
    id SERIAL PRIMARY KEY,
    mentor_id INTEGER REFERENCES mentors(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 3: Schema — Create subscription_plan, subscription tables (13:00-14:30)
───────────────────────────────────────────────────────────────────────────
CREATE TABLE subscription_plan (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    duration_months INT NOT NULL,
    features JSONB,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE subscription (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    plan_id INTEGER REFERENCES subscription_plan(id),
    status VARCHAR(20) DEFAULT 'active',
    start_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expiry_date TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 4: Schema — Create transaction_detail, banking_payment tables (14:30-17:30)
─────────────────────────────────────────────────────────────────────────────────
CREATE TABLE transaction_detail (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE SET NULL,
    amount DECIMAL(10,2) NOT NULL,
    type VARCHAR(50),
    status VARCHAR(20) DEFAULT 'pending',
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE banking_payment (
    id SERIAL PRIMARY KEY,
    transaction_id INTEGER REFERENCES transaction_detail(id),
    bank_name VARCHAR(100),
    account_number VARCHAR(50),
    account_holder VARCHAR(255),
    payment_proof_url TEXT,
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 3 TASKS (6 hours)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Schema — mentor_portfolio, mentor_skill, community_history (08:30-12:00)
─────────────────────────────────────────────────────────────────────────────────
CREATE TABLE mentor_portfolio (
    id SERIAL PRIMARY KEY,
    mentor_id INTEGER REFERENCES mentors(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    url TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE mentor_skill (
    id SERIAL PRIMARY KEY,
    mentor_id INTEGER REFERENCES mentors(id) ON DELETE CASCADE,
    skill_id INTEGER REFERENCES skill(id) ON DELETE CASCADE,
    level VARCHAR(20),
    UNIQUE(mentor_id, skill_id)
);

CREATE TABLE community_history (
    id SERIAL PRIMARY KEY,
    post_id INTEGER REFERENCES community_post(id) ON DELETE CASCADE,
    action VARCHAR(50),
    performed_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

TASK 2: Schema — account_history, account_history_log (13:00-17:30)
───────────────────────────────────────────────────────────────────
CREATE TABLE account_history (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    action VARCHAR(100),
    details JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE account_history_log (
    id SERIAL PRIMARY KEY,
    history_id INTEGER REFERENCES account_history(id) ON DELETE CASCADE,
    old_value JSONB,
    new_value JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 4 TASKS — RATHANAK switches to FRONTEND (Subscription + Admin)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Subscription Plans page (08:30-12:00)
─────────────────────────────────────────────
// src/pages/subscription/SubscriptionPlans.jsx
import { useState, useEffect } from 'react';
import api from '../../services/api';
import MainLayout from '../../layouts/MainLayout';
import Loading from '../../components/Loading';

const SubscriptionPlans = () => {
  const [plans, setPlans] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchPlans();
  }, []);

  const fetchPlans = async () => {
    try {
      const response = await api.get('/subscriptions/plans');
      setPlans(response.data);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  const handleSubscribe = async (planId) => {
    try {
      await api.post('/subscriptions', { plan_id: planId });
      alert('Subscribed successfully!');
    } catch (err) {
      alert(err.response?.data?.message || 'Subscription failed');
    }
  };

  if (loading) return <Loading />;

  return (
    <MainLayout>
      <div className="space-y-6">
        <h1 className="text-2xl font-bold text-center">Choose Your Plan</h1>
        
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
          {plans.map(plan => (
            <div key={plan.id} className={`bg-white rounded-xl shadow-md p-6 border-2 ${
              plan.name === 'Pro' ? 'border-blue-500' : 'border-transparent'
            }`}>
              <h3 className="text-xl font-bold mb-2">{plan.name}</h3>
              <p className="text-3xl font-bold text-blue-600 mb-4">
                ${plan.price}<span className="text-sm text-gray-500">/{plan.duration_months}mo</span>
              </p>
              <ul className="space-y-2 mb-6">
                {(plan.features || []).map((feature, i) => (
                  <li key={i} className="flex items-center text-gray-600">
                    <span className="text-green-500 mr-2">✓</span>
                    {feature}
                  </li>
                ))}
              </ul>
              <button
                onClick={() => handleSubscribe(plan.id)}
                className={`w-full py-2 rounded-lg font-medium ${
                  plan.name === 'Pro' 
                    ? 'bg-blue-600 text-white hover:bg-blue-700' 
                    : 'bg-gray-100 text-gray-800 hover:bg-gray-200'
                }`}
              >
                Subscribe
              </button>
            </div>
          ))}
        </div>
      </div>
    </MainLayout>
  );
};

export default SubscriptionPlans;

TASK 2: Connect Subscription to real API (13:00-17:30)
────────────────────────────────────────────────────
// Already connected in SubscriptionPlans.jsx above

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 5 TASKS — RATHANAK stays on FRONTEND (UI Polish + Deploy)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: UI animations + transitions (08:30-12:00)
─────────────────────────────────────────────────
// src/styles/animations.css
@keyframes slideIn {
  from { transform: translateX(-100%); opacity: 0; }
  to { transform: translateX(0); opacity: 1; }
}

@keyframes fadeUp {
  from { transform: translateY(20px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

.animate-slide-in { animation: slideIn 0.3s ease-out; }
.animate-fade-up { animation: fadeUp 0.4s ease-out; }
.animate-pulse-slow { animation: pulse 2s infinite; }

// Add hover transitions to components
// transition-all duration-200 ease-in-out

TASK 2: Deploy frontend to Vercel (13:00-17:30)
────────────────────────────────────────────────
// Steps:
// 1. npm run build (test locally first)
// 2. Push to GitHub
// 3. Go to vercel.com
// 4. Import GitHub repo
// 5. Configure:
//    - Framework Preset: Vite
//    - Build Command: npm run build
//    - Output Directory: dist
// 6. Add Environment Variables:
//    VITE_API_BASE_URL=https://your-backend.onrender.com/api
// 7. Deploy

═══════════════════════════════════════════════════════════════════════════════
🎯 DAY 6 TASKS — RATHANAK stays on FRONTEND (Loading + Error Boundaries)
═══════════════════════════════════════════════════════════════════════════════

TASK 1: Loading states + skeleton screens (08:30-12:00)
───────────────────────────────────────────────────────
// src/components/SkeletonCard.jsx
const SkeletonCard = () => (
  <div className="bg-white p-6 rounded-xl shadow-md animate-pulse">
    <div className="h-4 bg-gray-200 rounded w-3/4 mb-4"></div>
    <div className="h-4 bg-gray-200 rounded w-1/2 mb-2"></div>
    <div className="h-4 bg-gray-200 rounded w-full"></div>
  </div>
);

export default SkeletonCard;

// Use in pages while loading
// {loading && <><SkeletonCard /><SkeletonCard /></>}

TASK 2: Error boundaries + fallback UI (13:00-17:30)
─────────────────────────────────────────────────────
// src/components/ErrorBoundary.jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('ErrorBoundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="min-h-screen flex items-center justify-center bg-gray-50">
          <div className="text-center p-8 bg-white rounded-xl shadow-md">
            <h2 className="text-2xl font-bold text-red-600 mb-4">Oops!</h2>
            <p className="text-gray-600 mb-4">Something went wrong.</p>
            <button
              onClick={() => window.location.reload()}
              className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700"
            >
              Reload Page
            </button>
          </div>
        </div>
      );
    }
    return this.props.children;
  }
}

export default ErrorBoundary;

// Wrap App in main.jsx:
// <ErrorBoundary><App /></ErrorBoundary>

═══════════════════════════════════════════════════════════════════════════════
✅ RATHANAK CHECKLIST
═══════════════════════════════════════════════════════════════════════════════

DAY 1 (Backend):
  ☐ PostgreSQL installed and running
  ☐ Database project_db created
  ☐ User project_user created
  ☐ users table created
  ☐ user_type table created
  ☐ students table created
  ☐ mentors table created
  ☐ Indexes added
  ☐ Constraints added

DAY 2 (Backend):
  ☐ skill table created
  ☐ province table created
  ☐ community_type table created
  ☐ community_post table created
  ☐ mentor_post table created
  ☐ subscription_plan table created
  ☐ subscription table created
  ☐ transaction_detail table created
  ☐ banking_payment table created

DAY 3 (Backend):
  ☐ mentor_portfolio table created
  ☐ mentor_skill table created
  ☐ community_history table created
  ☐ account_history table created
  ☐ account_history_log table created

DAY 4 (Frontend switch):
  ☐ Subscription Plans page created
  ☐ Connected to real API

DAY 5 (Frontend):
  ☐ UI animations added
  ☐ Frontend deployed to Vercel

DAY 6 (Frontend):
  ☐ Skeleton screens created
  ☐ ErrorBoundary implemented

═══════════════════════════════════════════════════════════════════════════════
END OF RATHANAK DOCUMENT
═══════════════════════════════════════════════════════════════════════════════

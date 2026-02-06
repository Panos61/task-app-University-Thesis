# Task Management App

A full-stack, real-time collaborative task management application built with **React**, **GraphQL**, **Node.js**, and **PostgreSQL**. Features a Kanban-style drag-and-drop board, Gantt chart visualization, project collaboration via invitation codes, and secure JWT authentication -- all containerized with Docker for production deployment.

**[Live Demo](http://167.235.30.231)** | **[Source Code](https://github.com/Panos61/task-app)**

---

## Features

### Authentication & Security
- User registration and login with **bcrypt** password hashing
- **JWT**-based session management with **httpOnly** cookies
- Protected routes with automatic redirect on expired sessions
- Account deletion with full data cleanup

### Project Management
- Create projects with custom names and color labels
- **Invitation system** -- share a 12-character hex code to invite collaborators
- Join existing projects via invitation codes
- Project-level permissions (owner vs. collaborator)
- Real-time collaborator and task count tracking

### Kanban Task Board
- **Drag-and-drop** task management across three columns: *Backlog*, *In Progress*, *Done*
- Inline task editing with **debounced auto-save** (title, description, priority, assignee)
- Task priority levels: Low, Medium, High
- Task assignment to project collaborators
- Configurable start/end dates per task
- Celebration animation on task completion

### Gantt Chart
- Interactive **Gantt chart** visualization of project tasks and timelines
- Multiple view modes: Day, Week, Month, Year
- Progress calculation based on task status
- **Print preview** functionality for exporting schedules

### Dashboard & Overview
- Personalized dashboard with real-time stats: project count, tasks assigned, collaborators
- Quick-access project sidebar with task counts
- "Tasks Assigned" and "My Projects" summary cards
- Join projects directly from the dashboard via invitation codes

### UI/UX
- Responsive design for desktop and mobile
- Skeleton loading states for smooth perceived performance
- Animated page transitions with **Framer Motion**
- Clean, modern interface using **Mantine UI** components + **Tailwind CSS**
- Form validation with real-time feedback via **Formik** + **Yup**

---

## Tech Stack

### Frontend
| Technology | Purpose |
|---|---|
| React 18 | UI framework |
| TypeScript | Type safety |
| Vite | Build tool & dev server |
| Apollo Client | GraphQL state management & caching |
| Mantine UI | Component library |
| Tailwind CSS | Utility-first styling |
| @dnd-kit | Drag-and-drop interactions |
| Framer Motion | Animations & transitions |
| Formik + Yup | Form handling & validation |
| React Router 7 | Client-side routing |
| gantt-task-react | Gantt chart visualization |
| react-to-print | Print/export functionality |
| Lucide React | Icon library |

### Backend
| Technology | Purpose |
|---|---|
| Node.js | Runtime environment |
| Express | HTTP server |
| Apollo Server | GraphQL API layer |
| GraphQL | API query language |
| PostgreSQL 17 | Relational database |
| JSON Web Tokens | Authentication |
| bcryptjs | Password hashing |
| TypeScript | Type safety |

### Infrastructure
| Technology | Purpose |
|---|---|
| Docker | Containerization |
| Docker Compose | Multi-container orchestration |
| Multi-stage Dockerfiles | Optimized production images |

---

## Architecture

```
┌──────────────────┐     GraphQL      ┌──────────────────┐      SQL       ┌──────────────────┐
│                  │   (Apollo Client) │                  │  (pg Pool)     │                  │
│   React + Vite   │ ◄──────────────► │  Apollo Server   │ ◄────────────► │   PostgreSQL     │
│   (Frontend)     │     Port 80      │  + Express       │    Port 5432   │   (Database)     │
│                  │                  │  (Backend)       │                │                  │
└──────────────────┘                  └──────────────────┘                └──────────────────┘
                                             │
                                      JWT Auth via
                                     httpOnly Cookies
```

### Backend Modules
The server follows a **modular architecture** with separation of concerns:

```
server/src/
├── modules/
│   ├── user/        # Auth, registration, user queries
│   ├── project/     # Project CRUD, invitations, collaboration
│   └── task/        # Task CRUD, status updates, assignments
├── database/        # PostgreSQL connection pool & migrations
├── middleware/       # JWT authentication middleware
└── index.ts         # Server entry point
```

Each module contains its own **resolvers**, **services**, and **type definitions**, keeping the GraphQL schema clean and maintainable.

### Database Schema

```
users              projects              tasks                project_users
─────              ────────              ─────                ─────────────
id (UUID)          id (UUID)             id (SERIAL)          project_id (FK)
username           name                  title                user_id (FK)
password (hash)    color                 description          joined_at
created_at         owner_id (FK)         status
                   invitation (12-char)  priority
                   task_count            due_date
                   created_at            start_date / end_date
                   updated_at            project_id (FK)
                                         assignee_id (FK)
```

---

## GraphQL API

### Queries
| Query | Description |
|---|---|
| `me` | Get authenticated user info |
| `users` | List project collaborators |
| `overview` | Dashboard stats (projects, tasks, collaborators) |
| `project` | Get project details by ID |
| `projects` | List all user projects |
| `task` | Get task details |
| `tasks` | List tasks for a project |

### Mutations
| Mutation | Description |
|---|---|
| `register` | Create new user account |
| `login` | Authenticate and receive JWT |
| `logout` | Clear session cookie |
| `deleteAccount` | Remove user and associated data |
| `createProject` | Create project with name & color |
| `joinProject` | Join via 12-char invitation code |
| `deleteProject` | Remove project (owner only) |
| `createTask` | Add task to a project |
| `updateTask` | Modify task fields (debounced) |
| `deleteTask` | Remove task from project |

---

## Getting Started

### Prerequisites
- [Docker](https://docs.docker.com/get-docker/) & [Docker Compose](https://docs.docker.com/compose/install/)
- Node.js 20+ (for local development without Docker)

### Run with Docker (Recommended)

```bash
# Clone the repository
git clone https://github.com/Panos61/task-app.git
cd task-app

# Create environment file
cp .env.example .env  # Edit with your DB credentials and JWT secret

# Start all services (frontend, backend, database)
docker compose -f docker-compose.prod.yml up --build
```

The app will be available at `http://localhost` (frontend) and `http://localhost:4000/graphql` (API).

### Run Locally (Development)

```bash
# Start the database
docker compose up db -d

# Backend
cd server
npm install
npm run dev        # Starts on port 4000

# Frontend (in a new terminal)
cd client
npm install
npm run dev        # Starts on port 5173
```

### Environment Variables

| Variable | Description | Example |
|---|---|---|
| `DB_HOST` | Database host | `db` |
| `DB_PORT` | Database port | `5432` |
| `DB_USER` | Database user | `postgres` |
| `DB_PASSWORD` | Database password | `your_password` |
| `DB_NAME` | Database name | `taskapp` |
| `JWT_SECRET` | Secret for signing tokens | `your_jwt_secret` |
| `CORS_ORIGIN` | Allowed frontend origin | `http://localhost` |
| `VITE_API_URL` | Backend URL for frontend | `http://localhost:4000` |

---

## Project Structure

```
task-app/
├── client/                          # React frontend
│   ├── src/
│   │   ├── views/
│   │   │   ├── Home/                # Landing page
│   │   │   ├── Auth/                # Login & registration
│   │   │   └── Dashboard/           # Main app
│   │   │       ├── components/      # Board, Gantt, ProjectList, UserAccount
│   │   │       └── Dashboard.tsx    # Dashboard layout
│   │   ├── graphql/                 # Queries, mutations, type definitions
│   │   ├── hooks/                   # Custom React hooks (useDebounce, useGanttType)
│   │   ├── assets/                  # SVG illustrations
│   │   └── main.tsx                 # App entry with Apollo & Router providers
│   ├── Dockerfile.fixed             # Multi-stage production build
│   └── package.json
│
├── server/                          # Node.js backend
│   ├── src/
│   │   ├── modules/
│   │   │   ├── user/                # User resolvers, services, types
│   │   │   ├── project/             # Project resolvers, services, types
│   │   │   └── task/                # Task resolvers, services, types
│   │   ├── database/                # PostgreSQL pool & schema
│   │   ├── middleware/              # JWT auth middleware
│   │   └── index.ts                 # Express + Apollo Server setup
│   ├── Dockerfile.fixed             # Multi-stage production build
│   └── package.json
│
├── docker-compose.yml               # Development config
├── docker-compose.prod.yml          # Production config
├── init.sql                         # Database schema initialization
└── README.md
```

---

## Key Technical Highlights

- **GraphQL with Apollo** -- Full query/mutation API with optimistic UI updates, normalized caching, and automatic cache invalidation on mutations
- **Drag-and-Drop** -- `@dnd-kit` implementation with custom collision detection and optimistic status updates that persist via debounced GraphQL mutations
- **Database Transactions** -- Critical operations (project creation/deletion, cascading task updates) wrapped in PostgreSQL transactions for data consistency
- **Debounced Auto-Save** -- Custom `useDebounce` hook prevents excessive API calls during rapid task editing, batching updates at 500ms intervals
- **Security-First Auth** -- JWT tokens stored in httpOnly cookies (preventing XSS), bcrypt password hashing, parameterized SQL queries (preventing injection)
- **Containerized Deployment** -- Multi-stage Docker builds minimize image size; Docker Compose orchestrates frontend, backend, and database with health checks

---

## License

This project is open source and available under the [MIT License](LICENSE).

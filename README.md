# InternFlow

**Internship & team management platform built with the MERN stack** (MongoDB, Express, React, Node.js).

InternFlow helps a company follow its interns from start to finish: who is supervising whom, what each intern is working on, how far along they are, and how they are evaluated. The same model works for any team that assigns people to time-boxed missions (apprentices, new hires on onboarding, consultants on assignments).

![Internship detail](docs/internship-detail.png)

## Features

- **Role-based access** (JWT): `admin`, `supervisor` (tutor), `intern`. Every query is scoped server-side, so an intern only ever sees their own internship and a supervisor only sees the interns they supervise.
- **Internships**: assign an intern to a supervisor with dates, department and status (planned / ongoing / completed / cancelled).
- **Task board**: supervisors create tasks with due dates; interns move them between *To do*, *In progress* and *Done*. Overdue tasks are highlighted.
- **Evaluations**: 1–5 scores on technical skills, communication, autonomy and teamwork, with an automatic average and comments.
- **Dashboard**: ongoing internships, task completion rate, overdue tasks, average evaluation score — computed for the current user's scope.
- **User management** for admins (create accounts, enable/disable).
- **First-run setup**: the first visitor creates the admin account; the endpoint locks itself afterwards.

| Dashboard | Internships | Users (admin) |
|---|---|---|
| ![](docs/dashboard.png) | ![](docs/internships.png) | ![](docs/users.png) |

## Tech stack

| Layer | Tools |
|---|---|
| Frontend | React 19, React Router 7, Tailwind CSS 4, Vite |
| Backend | Node.js 22, Express 4, Mongoose, Zod validation, JWT, bcrypt, Helmet, rate limiting |
| Database | MongoDB |
| Tests | Jest + Supertest (API integration tests, in-memory MongoDB) |
| Ops | Docker / docker-compose, GitHub Actions CI |

## Getting started

### With Docker

```bash
docker compose up --build
# open http://localhost:4000
```

### Local development

Requirements: Node.js 20+ and a MongoDB instance.

```bash
npm run install:all
cp server/.env.example server/.env    # adjust MONGO_URL / JWT_SECRET
npm run seed                          # optional demo data
npm run dev:server                    # API on :4000
npm run dev:client                    # React app on :5173 (proxies /api)
```

Demo accounts after `npm run seed` (password `password123`): `admin@demo.io`, `sara@demo.io` (supervisor), `lina@demo.io` (intern).

### Tests

```bash
npm test
```

The API test suite covers authentication, validation errors, role permissions, per-user data scoping, the task status rules for interns, evaluations and the dashboard statistics.

## API overview

| Method | Endpoint | Who |
|---|---|---|
| `GET/POST` | `/api/auth/setup` | first run only |
| `POST` | `/api/auth/login` · `GET /api/auth/me` | everyone |
| `GET/POST/PATCH/DELETE` | `/api/users` | admin (supervisors can list interns) |
| `GET/POST` | `/api/internships` | scoped; create: admin, supervisor |
| `GET/PATCH/DELETE` | `/api/internships/:id` | scoped; edit: its supervisor or admin; delete: admin |
| `POST` | `/api/internships/:id/tasks` | its supervisor or admin |
| `PATCH/DELETE` | `/api/tasks/:id` | interns: status only |
| `POST` | `/api/internships/:id/evaluations` | its supervisor or admin |
| `GET` | `/api/stats` | scoped dashboard |

## Project structure

```
server/
  src/models/        Mongoose schemas (User, Internship, Task, Evaluation)
  src/routes/        Express routers
  src/middleware/    auth (JWT, roles) and error handling
  tests/             API integration tests
client/
  src/pages/         Dashboard, Internships, InternshipDetail, Users, Login
  src/components/    Layout and UI primitives
```

## License

MIT — Dhia Kanzari

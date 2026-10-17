# MERN E-Learning Platform

A full-stack e-learning application built with the MERN stack (MongoDB, Express, React, Node.js). Users can browse courses, enroll via Razorpay payments, watch lectures, and track progress. Admins can manage courses and users, and a superadmin can control roles.

## Tech Stack

**Frontend**
- React 18 + Vite
- React Router, Axios, React Hot Toast, React Icons

**Backend**
- Node.js + Express
- MongoDB with Mongoose
- JWT authentication, bcrypt password hashing
- Nodemailer (OTP and password-reset emails)
- Razorpay (payments), Multer (file uploads)

## Project Structure

```
Mern-Elearning/
├── frontend/          # React + Vite client
│   └── src/
│       ├── admin/        # Admin dashboard (courses, users, stats)
│       ├── components/   # Shared UI (header, footer, cards, etc.)
│       ├── context/      # User & Course context providers
│       └── pages/        # Auth, courses, account, dashboard, etc.
└── server/            # Express API
    ├── controllers/      # Route handlers (user, course, admin)
    ├── database/         # MongoDB connection
    ├── middlewares/      # Auth, mail, uploads, error handling
    ├── models/           # Mongoose schemas
    └── routes/           # API routes (all prefixed with /api)
```

## Prerequisites

- Node.js (v18+ recommended)
- A MongoDB database (local or MongoDB Atlas)
- A Razorpay account (for payment keys) — optional; payments are disabled if keys are not set
- A Gmail account with an App Password (for sending OTP / reset emails)

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/SakshiSingh-NITBhopal/Mern-Elearning.git
cd Mern-Elearning
```

### 2. Backend setup

```bash
cd server
npm install
```

Create a `.env` file in the `server/` folder based on the provided template:

```bash
cp .env.example .env     # on Windows PowerShell: Copy-Item .env.example .env
```

Then fill in your own values:

| Variable            | Description                                                        |
| ------------------- | ------------------------------------------------------------------ |
| `PORT`              | Port the server runs on (e.g. `5000`)                              |
| `DB`                | MongoDB connection string                                          |
| `Activation_Secret` | Secret for signing account-activation (OTP) tokens                 |
| `Jwt_Sec`           | Secret for signing login JWTs                                      |
| `Forgot_Secret`     | Secret for signing password-reset tokens                           |
| `Razorpay_Key`      | Razorpay key id (payments disabled if empty)                       |
| `Razorpay_Secret`   | Razorpay key secret                                                |
| `Gmail`             | Gmail address used to send emails                                  |
| `Password`          | Gmail **App Password** (not your normal password)                  |
| `frontendurl`       | Frontend base URL, used in reset-password emails (e.g. `http://localhost:5173`) |

Start the server:

```bash
npm run dev     # with nodemon (auto-reload)
# or
npm start       # plain node
```

The API runs at `http://localhost:5000`. Routes are mounted under `/api` (e.g. `POST /api/user/login`).

### 3. Frontend setup

```bash
cd frontend
npm install
npm run dev
```

The client runs at `http://localhost:5173` and talks to the API at `http://localhost:5000` (configured in `frontend/src/main.jsx`).

## Roles & Admin Access

The app uses two role fields on each user:

- `role`: `user` or `admin` — `admin` unlocks the admin dashboard and course management.
- `mainrole`: `user` or `superadmin` — `superadmin` additionally unlocks the Users management page and the ability to promote/demote other users in-app.

New accounts are created as `user`. There is no self-service way to become an admin (by design).

### Creating the first superadmin (bootstrap)

The app has no UI to create a superadmin, so the first one must be set directly in the database. Register an account normally, then set the fields on that user document:

```js
db.users.updateOne(
  { email: "you@example.com" },
  { $set: { role: "admin", mainrole: "superadmin" } }
)
```

You can run this from the MongoDB Atlas UI (Browse Collections → `users`) or with `mongosh`. Log out and back in afterward so the frontend picks up the new role.

### Promoting other users

Once you are a superadmin, use the admin **Users** page to toggle any user between `user` and `admin`. This calls `PUT /api/user/:id`, which is restricted to superadmins.

## Security Notes

- `server/.env` holds real secrets and is git-ignored — never commit it.
- `server/.env.example` documents the required variables with placeholder values.
- If a secret is ever exposed, rotate it immediately (change the DB password, regenerate Razorpay keys, revoke the Gmail app password).

## Available Scripts

**Server** (`server/`)
- `npm run dev` — start with nodemon
- `npm start` — start with node

**Frontend** (`frontend/`)
- `npm run dev` — start Vite dev server
- `npm run build` — production build
- `npm run preview` — preview the production build
- `npm run lint` — run ESLint

# Payment Plan Calculator (EE Yönetim)

A clinic management web app for tracking patient installment plans, outstanding balances and dental supply inventory.

## Overview

Built for a dental clinic that lets patients pay for treatment in installments. Staff log in, register a patient with the treatment, total amount and number of installments, and the app generates the payment schedule, tracks which installments are paid, unpaid or overdue, and lists patients who still owe money. The UI is in Turkish.

The repo contains two parts:
- **Frontend** (root): React + Material UI single-page app
- **Backend** (`server/`): Express REST API with PostgreSQL via Sequelize

> The frontend currently stores data in the browser's `localStorage`. An adapter layer (`src/api/apiAdapter.js`) switches to the REST API when `USE_LOCAL_STORAGE` is set to `false`.

## Features

- **Login and protected routes** for clinic staff
- **Dashboard** with total patients, overdue payments and upcoming payments
- **Installment patients**: add a patient with contact details, total treatment amount and installment count; the schedule is generated automatically
- **Payment tracking**: mark each installment as paid with a payment method (cash, credit card, bank transfer); overdue installments are flagged by date
- **Search, filter and sort** by national ID / name, date range and payment status
- **Debtor patients**: a separate module for patients with an outstanding balance (treatment, doctor, remaining amount, last payment date)
- **Backup**: JSON export/import of the whole dataset in the data layer (not yet exposed in the UI)
- **Inventory and orders** (early stage): stock-in form and order list with PDF export (jsPDF); backend inventory endpoints are currently placeholders

### Backend API (`server/`)
- JWT authentication with access and refresh tokens, bcrypt-hashed passwords
- Routes for auth, patients (`/api/hastalar`), installments, users, settings, backup and inventory
- Sequelize models for patients, installments, users, settings, plus an inventory schema (materials, locations, stock lots, movements, suppliers, orders, price history)
- Helmet, CORS and Morgan middleware

## Tech stack

| Layer | Tools |
|-------|-------|
| Frontend | React 19, React Router 7, Material UI 7 (DataGrid, Date Pickers), date-fns, jsPDF |
| Build | Webpack 5, Babel |
| Backend | Node.js, Express 4, Sequelize 6, PostgreSQL, JWT, bcryptjs |
| Deploy | Docker (Nginx serving the static build), CapRover (`captain-definition`) |

## Project structure

```
src/
  api/            # apiClient (REST), apiAdapter (localStorage or API), dbSchema
  components/     # MainLayout, patient list, new patient dialog
  context/        # AuthContext, HastaContext (patients)
  pages/          # Home, Login, Installment patients, Debtor patients, Inventory
  utils/          # localStorageDB, date helpers (overdue detection)
server/
  src/
    config/       # Database config
    controllers/  # auth, patients, installments, users, settings, backup
    models/       # Sequelize models
    routes/
    middleware/   # JWT auth, error handler
    scripts/      # setupDb.js (create tables + admin user)
Dockerfile, nginx.conf, captain-definition   # Frontend container
server/Dockerfile, server/captain-definition # API container
```

## Getting started

### Frontend

```bash
npm install
npm start          # webpack dev server
npm run build      # production build to dist/
```

### Backend

```bash
cd server
npm install
npm run db:setup   # recreates all tables (drops existing data) and the initial admin user
npm run dev        # nodemon; or: npm start
```

The API listens on port `3001` by default. Configure it with environment variables (in `server/.env`):

- `PORT`, `NODE_ENV`, `CORS_ORIGIN`
- `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`
- `JWT_SECRET`, `JWT_EXPIRES_IN`, `JWT_REFRESH_EXPIRES_IN`
- `ADMIN_PASSWORD` (initial admin user)

Set your own values for every credential. Do not rely on the code's fallback defaults in production.

### Docker

Both the frontend and `server/` have their own `Dockerfile` and `captain-definition` for deployment to CapRover or any Docker host.

## Roadmap

See `PROJE_YOL_HARITASI_VE_BAGLAM.md` (Turkish) for planned work: full patient records, role-based access control and financial reporting.

---

Built by [Mertcan Eren](https://github.com/mertcaneren0)

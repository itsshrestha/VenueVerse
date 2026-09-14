# VenueVerse

A full-stack **venue booking & management** platform built for the Nepal market.  
Customers can discover and book venues; venue owners manage their listings; an admin panel oversees the entire platform.

> **Tech Stack:** React 18 · Vite · Apollo Client · GraphQL · Node.js · Express · MongoDB · Cloudinary · Mailtrap · eSewa

---

## Repository Structure

```
VenueVerse/
├── backend/    ← Express + Apollo Server (GraphQL API)
└── frontend/   ← React + Vite (SPA)
```

---

## Quick Start

### Step 1 — Clone the Repository

```bash
git clone https://github.com/<your-username>/VenueVerse.git
cd VenueVerse
```

### Step 2 — Set Up the Backend

```bash
cd backend
npm install
```

Follow the detailed guide in **[backend/README.md](./backend/README.md)** for:
- MongoDB Atlas / local setup
- Cloudinary upload preset creation
- Mailtrap email configuration
- `.env` file configuration

### Step 3 — Seed the Admin Account

```bash
# (inside backend/)
node seedAdmin.js
```

| Field | Value |
|-------|-------|
| Email | `admin@venueverse.com` |
| Password | `Admin@123456` |
| Login URL | `http://localhost:5173/admin` |

> ⚠️ Change the password after first login.

### Step 4 — Set Up the Frontend

```bash
cd ../frontend
npm install
```

Follow the detailed guide in **[frontend/README.md](./frontend/README.md)** for the `.env` configuration.

### Step 5 — Run Both Servers

Open two terminal tabs:

**Terminal 1 — Backend**
```bash
cd backend
npm run dev
# Runs at http://localhost:4000/graphql
```

**Terminal 2 — Frontend**
```bash
cd frontend
npm run dev
# Runs at http://localhost:5173
```

---

## Features

| Feature | Customer | Venue Owner | Admin |
|---------|----------|-------------|-------|
| Browse & search venues | ✅ | ✅ | ✅ |
| Book a venue | ✅ | — | — |
| Pay via eSewa | ✅ | — | — |
| Manage own bookings | ✅ | — | — |
| Write reviews | ✅ | — | — |
| Add / edit venues | — | ✅ | — |
| Manage bookings | — | ✅ | — |
| Dashboard & analytics | — | ✅ | ✅ |
| Approve venue owners | — | — | ✅ |
| Approve venues | — | — | ✅ |
| Manage users | — | — | ✅ |
| Manage services & categories | — | — | ✅ |

---

## Detailed Setup Guides

| Guide | Location |
|-------|----------|
| 📦 Backend Setup | [`backend/README.md`](./backend/README.md) |
| 🖥️ Frontend Setup | [`frontend/README.md`](./frontend/README.md) |

---

## License

[MIT](./LICENSE)

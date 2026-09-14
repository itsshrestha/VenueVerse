# VenueVerse — Backend

> **Stack:** Node.js · Express · Apollo Server (GraphQL) · MongoDB (Mongoose) · Cloudinary · Mailtrap · JWT

---

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Clone & Install](#2-clone--install)
3. [MongoDB Setup](#3-mongodb-setup)
4. [Cloudinary Setup](#4-cloudinary-setup)
5. [Mailtrap Setup](#5-mailtrap-setup)
6. [Environment Variables](#6-environment-variables)
7. [Run the Server](#7-run-the-server)
8. [Seed the Admin Account](#8-seed-the-admin-account)
9. [GraphQL Playground](#9-graphql-playground)
10. [Project Structure](#10-project-structure)

---

## 1. Prerequisites

| Tool | Minimum Version | Install Guide |
|------|----------------|---------------|
| Node.js | 18.x or later | https://nodejs.org |
| npm | 9.x or later | Bundled with Node.js |
| MongoDB | Atlas (cloud) **or** local 6.x+ | https://www.mongodb.com |
| Git | any | https://git-scm.com |

---

## 2. Clone & Install

```bash
# Clone the repository
git clone https://github.com/itsshrestha/VenueVerse.git
cd VenueVerse/backend

# Install dependencies
npm install
```

---

## 3. MongoDB Setup

You can use **MongoDB Atlas (cloud)** or a **local MongoDB** instance.

### Option A — MongoDB Atlas (Recommended)

1. Go to [https://cloud.mongodb.com](https://cloud.mongodb.com) and create a free account.
2. Click **"Build a Database"** → Choose the **Free (M0)** tier.
3. Select your preferred cloud provider and region, then click **Create**.
4. Under **Security → Database Access**, create a database user:
   - Username: `venueverse_user` (or anything you prefer)
   - Password: a strong password
   - Role: **Read and write to any database**
5. Under **Security → Network Access**, click **"Add IP Address"**:
   - For development: add `0.0.0.0/0` (allow all) — restrict in production.
6. Go to **Database → Connect → Drivers**:
   - Copy the connection string, which looks like:
     ```
     mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/<dbName>?retryWrites=true&w=majority
     ```
   - Replace `<username>`, `<password>`, and `<dbName>` (e.g., `VenueVerse`).
7. Paste the full URI as `MONGO_URI` in your `.env` file (see [§6](#6-environment-variables)).

### Option B — Local MongoDB

```bash
# Start MongoDB locally (macOS/Linux with Homebrew)
brew services start mongodb-community

# Or with systemctl (Linux)
sudo systemctl start mongod
```

Set `LOCAL_URI` in your `.env`:
```
LOCAL_URI=mongodb://localhost:27017/VenueVerse
```

> **Note:** `config/db.js` reads `process.env.MONGO_URI`, so set `MONGO_URI` even for local — or update the connect call to fall back to `LOCAL_URI`.

---

## 4. Cloudinary Setup

Cloudinary is used to **store and serve venue images, user profile photos, and legal documents**.

1. Create a free account at [https://cloudinary.com](https://cloudinary.com).
2. After logging in, go to your **Dashboard** — you'll see:
   - **Cloud Name**
   - **API Key**
   - **API Secret**
3. Copy all three values into `.env` (see [§6](#6-environment-variables)).

### Create Upload Presets

You need **two upload presets** (one signed, one unsigned):

1. In the Cloudinary Console go to **Settings → Upload → Upload presets**.
2. Click **"Add upload preset"**:

   | Preset | Signing Mode | Suggested Name | Folder |
   |--------|-------------|----------------|--------|
   | Signed | **Signed** | `sign_upload` | `venueverse/venues` |
   | Unsigned | **Unsigned** | `outside_preset` | `venueverse/users` |

3. Save each preset and copy the names into your **frontend** `.env` (`VITE_SIGNED_UPLOAD_PRESET`, `VITE_UNSIGNED_UPLOAD_PRESET`).

---

## 5. Mailtrap Setup

Mailtrap is used for **transactional emails** (verification codes, password resets).

### Option A — Mailtrap Testing Inbox (Development)

1. Sign up at [https://mailtrap.io](https://mailtrap.io).
2. Go to **Email Testing → Inboxes → My Inbox**.
3. Click **"Show credentials"** and copy the `SMTP` or `API Token`.
4. Note your **Inbox ID** (the number in the URL).
5. Fill in the values in `.env` (see [§6](#6-environment-variables)).

### Option B — Mailtrap Email Sending (Production / SMTP)

1. In Mailtrap, go to **Email Sending → Sending Domains**.
2. Add and verify your domain (e.g., `yourdomain.com`).
3. Go to **SMTP/API Settings** and copy the credentials.
4. In `config/mailtrap.config.js`, uncomment the SMTP block and comment out the testing block.

---

## 6. Environment Variables

Create a `.env` file in the `backend/` directory:

```bash
cp .env.example .env   # if .env.example exists, otherwise create manually
```

Then fill in **every** variable below:

```env
# ── MongoDB ────────────────────────────────────────────────────────────────
# Use ONE of the two URIs below (Atlas is recommended for production)
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/VenueVerse?retryWrites=true&w=majority
LOCAL_URI=mongodb://localhost:27017/VenueVerse

# ── App / Auth ─────────────────────────────────────────────────────────────
PORT=4000
NODE_ENV=development           # or "production"
JWT_SECRET=your_super_secret_jwt_key_change_this
SECRET_KEY=your_esewa_secret_key

# ── CORS ───────────────────────────────────────────────────────────────────
CLIENT_URL=http://localhost:5173   # Frontend origin (update in production)

# ── Cloudinary ─────────────────────────────────────────────────────────────
CLOUDINARY_URL=cloudinary://<api_key>:<api_secret>@<cloud_name>
CLOUD_NAME=your_cloud_name
CLOUD_API_KEY=your_cloud_api_key
CLOUD_API_SECRET=your_cloud_api_secret

# ── Mailtrap (Testing Inbox — development) ─────────────────────────────────
MAILTRAP_TOKEN=your_mailtrap_api_token
MAILTRAP_USER=api
MAILTRAP_HOST=live.smtp.mailtrap.io
MAILTRAP_PORT=587
MY_DOMAIN=noreply@yourdomain.com
```

> ⚠️ **Never commit your real `.env` to Git.** It is already listed in `.gitignore`.

### Variable Reference Table

| Variable | Required | Description |
|----------|----------|-------------|
| `MONGO_URI` | ✅ | MongoDB Atlas connection string |
| `LOCAL_URI` | Optional | Local MongoDB URI (fallback) |
| `PORT` | Optional | Server port (default `4000`) |
| `NODE_ENV` | ✅ | `development` or `production` |
| `JWT_SECRET` | ✅ | Secret for signing JWTs — keep long & random |
| `SECRET_KEY` | ✅ | eSewa payment secret key |
| `CLIENT_URL` | ✅ | Frontend URL for CORS allow-list |
| `CLOUDINARY_URL` | Optional | Full Cloudinary URL (auto-configures SDK) |
| `CLOUD_NAME` | ✅ | Cloudinary cloud name |
| `CLOUD_API_KEY` | ✅ | Cloudinary API key |
| `CLOUD_API_SECRET` | ✅ | Cloudinary API secret |
| `MAILTRAP_TOKEN` | ✅ | Mailtrap API token |
| `MAILTRAP_USER` | ✅ | Mailtrap SMTP user (usually `"api"`) |
| `MAILTRAP_HOST` | ✅ | Mailtrap SMTP host |
| `MAILTRAP_PORT` | ✅ | Mailtrap SMTP port (587) |
| `MY_DOMAIN` | ✅ | Sender email address (your verified domain) |

---

## 7. Run the Server

```bash
# Development mode (auto-restarts on file changes via --watch)
npm run dev

# Production mode
node server.js
```

The server will start at:
```
http://localhost:4000/graphql
```

---

## 8. Seed the Admin Account

After the server successfully connects to MongoDB, run the seed script **once** to create the default super-admin user:

```bash
node seedAdmin.js
```

**Expected output:**

```
✅  Connected to MongoDB
🎉  Admin account created successfully!
─────────────────────────────────────────
   Name     : Super Admin
   Email    : admin@venueverse.com
   Password : Admin@123456
─────────────────────────────────────────
⚠️   IMPORTANT: Change the password after your first login!
🔌  Disconnected from MongoDB
```

### Default Admin Credentials

| Field | Value |
|-------|-------|
| **Email** | `admin@venueverse.com` |
| **Password** | `Admin@123456` |
| **Role** | `Admin` |
| **Login URL** | `http://localhost:5173/admin` |

> 🔒 **Change the password immediately** after your first login via the Admin panel settings.

The script is idempotent — running it a second time will detect the existing admin and exit safely without creating duplicates.

---

## 9. GraphQL Playground

Once the server is running, open your browser and navigate to:

```
http://localhost:4000/graphql
```

This opens the **Apollo Sandbox** where you can explore the full GraphQL schema, run queries, and test mutations.

**Example — Login Mutation:**
```graphql
mutation {
  login(email: "admin@venueverse.com", password: "Admin@123456")
}
```

---

## 10. Project Structure

```
backend/
├── config/
│   ├── db.js                # MongoDB connection
│   ├── email_templates.js   # HTML email templates
│   ├── mailtrap.config.js   # Mailtrap transport setup
│   └── mailtype.js          # Email sending helpers
├── graphql/
│   ├── schema.js            # GraphQL type definitions
│   └── resolvers.js         # All query & mutation resolvers
├── middleware/
│   └── authenticate.js      # JWT authentication middleware
├── models/
│   ├── Booking.js           # Booking schema
│   ├── Categories.js        # Venue categories
│   ├── Common.js            # Shared sub-schemas (Image, Location, etc.)
│   ├── Review.js            # Review schema
│   ├── Service.js           # Service schema
│   ├── Transaction.js       # Payment transaction schema
│   ├── User.js              # User schema (with bcrypt hooks)
│   └── Venue.js             # Venue schema
├── utils/
│   ├── functions.js         # Utility/helper functions
│   └── Validation.js        # Input validation helpers
├── seedAdmin.js             # ← One-time admin seeder
├── server.js                # Express + Apollo Server entry point
├── package.json
└── .env                     # Your local environment variables (not committed)
```

---

## Common Issues

| Problem | Fix |
|---------|-----|
| `MongoServerError: Authentication failed` | Double-check the username & password in `MONGO_URI` |
| `Cannot find module './models/User.js'` | Make sure `"type": "module"` is in `package.json` and all imports use `.js` extensions |
| `MAILTRAP_TOKEN is not defined` | Ensure `.env` has all three Mailtrap variables |
| CORS errors from frontend | Ensure `CLIENT_URL` exactly matches the frontend origin (no trailing slash) |
| `Cloudinary upload fails` | Verify `CLOUD_NAME`, `CLOUD_API_KEY`, and `CLOUD_API_SECRET` are correct |
| Port already in use | Change `PORT` in `.env` or kill the process using port 4000 |

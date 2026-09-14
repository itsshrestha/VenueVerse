# VenueVerse — Frontend

> **Stack:** React 18 · Vite · Apollo Client (GraphQL) · Tailwind CSS v4 · Cloudinary · React Router v7

---

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Clone & Install](#2-clone--install)
3. [Cloudinary Setup (Frontend)](#3-cloudinary-setup-frontend)
4. [Environment Variables](#4-environment-variables)
5. [Run the Dev Server](#5-run-the-dev-server)
6. [Build for Production](#6-build-for-production)
7. [Connecting to the Backend](#7-connecting-to-the-backend)
8. [Project Structure](#8-project-structure)
9. [Route Map](#9-route-map)
10. [Deployment (Vercel)](#10-deployment-vercel)
11. [Common Issues](#11-common-issues)

---

## 1. Prerequisites

| Tool | Minimum Version | Install Guide |
|------|----------------|---------------|
| Node.js | 18.x or later | https://nodejs.org |
| npm | 9.x or later | Bundled with Node.js |
| Git | any | https://git-scm.com |

> The backend must also be running. See [`backend/README.md`](../backend/README.md) for setup instructions.

---

## 2. Clone & Install

```bash
# Clone the repository (skip if already done for the backend)
git clone https://github.com/<your-username>/VenueVerse.git
cd VenueVerse/frontend

# Install all dependencies
npm install
```

---

## 3. Cloudinary Setup (Frontend)

The frontend uses the **Cloudinary React SDK** to upload images (venue photos, user avatars, legal documents) directly from the browser to Cloudinary.

### 3.1 Get your Cloud Name & API Key

Follow steps 1–3 in the [backend Cloudinary guide](../backend/README.md#4-cloudinary-setup) — you need the same account.

### 3.2 Create Upload Presets

In your Cloudinary Console → **Settings → Upload → Upload presets**:

| Preset Purpose | Signing Mode | Preset Name (suggested) | Default Folder |
|---------------|-------------|------------------------|----------------|
| Venue images | **Signed** | `sign_upload` | `venueverse/venues` |
| User avatars & docs | **Unsigned** | `outside_preset` | `venueverse/users` |

> The preset names must exactly match `VITE_SIGNED_UPLOAD_PRESET` and `VITE_UNSIGNED_UPLOAD_PRESET` in your `.env`.

---

## 4. Environment Variables

Create a `.env` file in the `frontend/` directory:

```bash
# From the frontend/ directory
touch .env
```

Paste and fill in the following:

```env
# ── GraphQL API ────────────────────────────────────────────────────────────
# Point to your running backend (local dev or deployed URL)
VITE_GRAPHQL_API=http://localhost:4000/graphql

# ── App Mode ───────────────────────────────────────────────────────────────
VITE_MODE=development

# ── Cloudinary ─────────────────────────────────────────────────────────────
VITE_CLOUD_NAME=your_cloud_name
VITE_CLOUD_API_KEY=your_cloud_api_key
VITE_SIGNED_UPLOAD_PRESET=sign_upload
VITE_UNSIGNED_UPLOAD_PRESET=outside_preset

# ── Cloudinary Upload Folders ──────────────────────────────────────────────
VITE_UPLOAD_VENUE_IMAGE_FOLDER=venueverse/venues
VITE_UPLOAD_USER_IMAGE_FOLDER=venueverse/users
VITE_UPLOAD_USER_DOCS_IMAGE_FOLDER=venueverse/docs

# ── eSewa Payment Gateway ──────────────────────────────────────────────────
VITE_PAYMENT_SUCCESS_URL=http://localhost:5173/Home/venue/payment-success
VITE_PAYMENT_FAILURE_URL=http://localhost:5173/Home/venue/payment-failure
VITE_PAYMENT_PRODUCT_CODE=EPAYTEST   # Use "EPAYTEST" for sandbox, real code for production

# ── Sightengine (Image Moderation — optional) ──────────────────────────────
VITE_IMG_PURIFY_API_USER=your_sightengine_api_user
VITE_IMG_PURIFY_API_SECRET=your_sightengine_api_secret
```

> ⚠️ All Vite environment variables **must** start with `VITE_` to be accessible in the browser.  
> ⚠️ Never commit your real `.env` to Git — it is listed in `.gitignore`.

### Variable Reference Table

| Variable | Required | Description |
|----------|----------|-------------|
| `VITE_GRAPHQL_API` | ✅ | Full URL of the backend GraphQL endpoint |
| `VITE_MODE` | Optional | App mode flag (`development` / `production`) |
| `VITE_CLOUD_NAME` | ✅ | Cloudinary cloud name |
| `VITE_CLOUD_API_KEY` | ✅ | Cloudinary API key |
| `VITE_SIGNED_UPLOAD_PRESET` | ✅ | Signed upload preset name (for venue images) |
| `VITE_UNSIGNED_UPLOAD_PRESET` | ✅ | Unsigned upload preset name (for user photos) |
| `VITE_UPLOAD_VENUE_IMAGE_FOLDER` | ✅ | Cloudinary folder path for venue images |
| `VITE_UPLOAD_USER_IMAGE_FOLDER` | ✅ | Cloudinary folder path for user avatars |
| `VITE_UPLOAD_USER_DOCS_IMAGE_FOLDER` | ✅ | Cloudinary folder path for legal docs |
| `VITE_PAYMENT_SUCCESS_URL` | ✅ | Redirect URL after successful eSewa payment |
| `VITE_PAYMENT_FAILURE_URL` | ✅ | Redirect URL after failed eSewa payment |
| `VITE_PAYMENT_PRODUCT_CODE` | ✅ | eSewa product code (`EPAYTEST` for sandbox) |
| `VITE_IMG_PURIFY_API_USER` | Optional | Sightengine API user for image moderation |
| `VITE_IMG_PURIFY_API_SECRET` | Optional | Sightengine API secret |

---

## 5. Run the Dev Server

Make sure the **backend** is already running on `http://localhost:4000`, then:

```bash
# From the frontend/ directory
npm run dev
```

The app will be available at:
```
http://localhost:5173
```

Vite includes **Hot Module Replacement (HMR)** — changes are reflected instantly in the browser.

---

## 6. Build for Production

```bash
npm run build
```

The optimised static files are output to the `dist/` folder. Serve them with any static host (Vercel, Netlify, Nginx, etc.).

To preview the production build locally:
```bash
npm run preview
```

---

## 7. Connecting to the Backend

The Apollo Client is configured in `src/middleware/ApolloClient.jsx`. It reads `VITE_GRAPHQL_API` from the environment.

- **Development:** set `VITE_GRAPHQL_API=http://localhost:4000/graphql`
- **Production:** set `VITE_GRAPHQL_API=https://your-deployed-backend.com/graphql`

All GraphQL requests are sent with `credentials: "include"` so that the `authToken` HTTP-only cookie is forwarded automatically.

---

## 8. Project Structure

```
frontend/
├── public/                  # Static assets served as-is
├── src/
│   ├── assets/              # Images, icons, and fonts
│   ├── components/
│   │   ├── admin/           # Admin panel components
│   │   │   ├── Login.jsx    # Admin login page
│   │   │   ├── Reports.jsx
│   │   │   ├── RoleRequest.jsx
│   │   │   ├── Services.jsx
│   │   │   ├── Users.jsx
│   │   │   └── Categories.jsx
│   │   ├── Layout/          # Shared layout wrappers
│   │   │   ├── Layout.jsx        # Public layout
│   │   │   ├── Vendor_Layout.jsx # VenueOwner layout
│   │   │   ├── User_Layout.jsx   # Customer layout
│   │   │   └── Admin_Layout.jsx  # Super-admin layout
│   │   ├── User/            # Customer-facing components
│   │   ├── VenueOwner/      # Venue owner dashboard components
│   │   ├── BookNow.jsx
│   │   ├── PaymentSuccess.jsx
│   │   └── PaymentFailed.jsx
│   ├── middleware/
│   │   ├── ApolloClient.jsx # Apollo Client setup
│   │   ├── AuthContext.jsx  # Global auth state (React Context)
│   │   └── ProtectedRoute.jsx # Route guards
│   ├── pages/
│   │   ├── Auth/            # Login, SignUp, Verification, Reset
│   │   ├── Home/            # Landing, Venues, Contact, How it works
│   │   └── common/          # 404, VenueDetails, Reports
│   ├── App.jsx              # All route definitions
│   ├── main.jsx             # App entry point
│   ├── index.css            # Global styles
│   └── App.css
├── index.html               # Root HTML template
├── vite.config.js           # Vite configuration
├── vercel.json              # Vercel deployment config
├── eslint.config.js         # ESLint rules
└── package.json
```

---

## 9. Route Map

| Path | Component | Access |
|------|-----------|--------|
| `/` | Landing Page | Public |
| `/Venues` | Venues listing | Public |
| `/How-it-works` | How it works | Public |
| `/Contact` | Contact page | Public |
| `/Login` | Login | Public |
| `/SignUp` | Sign up | Public |
| `/forgot-password` | Email verification | Public |
| `/reset-password/:token` | Reset password | Public |
| `/OTPVerification` | OTP input | Public |
| `/venue/:id` | Venue detail | Public |
| `/Home` | Customer venues | 🔒 Authenticated |
| `/Home/my-bookings` | My bookings | 🔒 Authenticated |
| `/Home/favorites` | Saved venues | 🔒 Authenticated |
| `/Home/settings` | Profile settings | 🔒 Authenticated |
| `/Home/venue/:id/book-now` | Book a venue | 🔒 Authenticated |
| `/Home/BecomeVenueOwner` | Register as owner | 🔒 Authenticated |
| `/Dashboard` | Vendor dashboard | 🔒 VenueOwner |
| `/Dashboard/add-venue` | Add new venue | 🔒 VenueOwner |
| `/Dashboard/bookings` | Manage bookings | 🔒 VenueOwner |
| `/Dashboard/my-venues` | My venues list | 🔒 VenueOwner |
| `/Dashboard/settings` | Owner settings | 🔒 VenueOwner |
| `/admin` | Admin login | Public |
| `/super-admin` | Admin dashboard | 🔒 Admin only |
| `/super-admin/update-role` | Role requests | 🔒 Admin only |
| `/super-admin/users` | Manage users | 🔒 Admin only |
| `/super-admin/addServices` | Manage services | 🔒 Admin only |
| `/super-admin/addCategories` | Manage categories | 🔒 Admin only |

---

## 10. Deployment (Vercel)

A `vercel.json` is already included. To deploy:

```bash
# Install Vercel CLI globally
npm install -g vercel

# From the frontend/ directory
vercel
```

Set the same environment variables from [§4](#4-environment-variables) in the Vercel dashboard under **Project → Settings → Environment Variables**, making sure to update:

```env
VITE_GRAPHQL_API=https://your-backend-url.onrender.com/graphql
VITE_PAYMENT_SUCCESS_URL=https://your-frontend-url.vercel.app/Home/venue/payment-success
VITE_PAYMENT_FAILURE_URL=https://your-frontend-url.vercel.app/Home/venue/payment-failure
```

---

## 11. Common Issues

| Problem | Fix |
|---------|-----|
| `Failed to fetch` / network error | Make sure the backend is running and `VITE_GRAPHQL_API` is correct |
| CORS error in browser | Ensure `CLIENT_URL` in the backend `.env` exactly matches your frontend origin |
| Cloudinary upload fails | Check `VITE_CLOUD_NAME`, `VITE_CLOUD_API_KEY`, and preset names |
| White screen / 404 on page refresh | The `vercel.json` rewrites all paths to `index.html` — ensure it's present |
| `VITE_` variable is undefined at runtime | All env variables used in React **must** be prefixed with `VITE_` |
| eSewa payment not working | Use `EPAYTEST` as product code and test credentials in sandbox mode |

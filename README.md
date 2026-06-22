# Groundwork Books

Full-stack e-commerce platform for Groundwork Books, a volunteer-run bookstore. Built as a single Next.js full-stack application with live Square POS inventory sync, Pinecone semantic search, and a two-tier Redis caching layer. Deployed on Vercel.

**Live site:** [gw-website-react.vercel.app](https://gw-website-react.vercel.app/)

## Architecture

A single Next.js application handles both frontend rendering and backend logic through API routes, deployed together as serverless functions on Vercel. There is no separate backend server.

- **Frontend:** React, Next.js 15.4.6, Firebase Auth, TailwindCSS
- **Backend:** Next.js API routes (serverless functions)
- **Search:** Pinecone vector index for concept-based book discovery
- **Caching:** Redis look-aside cache over the Square API, plus client-side request coalescing
- **Integrations:** Square POS API, Instagram Basic Display API, Google Sheets API
- **Cart:** React Context + localStorage
- **Hosting:** Vercel (full-stack deployment)

## Features

- Email/password login and signup (Firebase Auth)
- Book catalog synced live from Square inventory
- Concept-based semantic search via Pinecone (no exact-title matching required)
- Cart with add, remove, and update
- Checkout via Square redirect with a confirmation page
- Protected routes for cart, checkout, and account
- Two-tier caching to reduce Square API latency and batch concurrent inventory lookups

## Project Structure

```
gw-website-react/
├── frontend/                 # Next.js full-stack application
│   ├── src/
│   │   ├── app/
│   │   │   ├── api/           # Backend API routes
│   │   │   │   ├── events/    # Events management
│   │   │   │   ├── instagram/ # Instagram integration
│   │   │   │   ├── orders/    # Order processing & management
│   │   │   │   ├── search/    # Book search functionality
│   │   │   │   └── square/    # Square POS integration
│   │   │   ├── (pages)/       # Frontend pages
│   │   │   └── layout.tsx
│   │   ├── components/        # Reusable React components
│   │   └── lib/              # Utilities, contexts, types
│   ├── public/               # Static assets
│   ├── package.json
│   └── .env                  # Environment variables (not committed)
└── package.json              # Root workspace config
```

## Quick Start

### 1. Install dependencies

```bash
cd frontend
npm install
```

Recommended VS Code extensions:

```
austenc.tailwind-docs
bradlc.vscode-tailwindcss
stivo.tailwind-fold   # optional
```

### 2. Environment setup

Create `frontend/.env` and configure the required environment variables for Square, Pinecone, Redis, and Firebase. Secrets are never committed; the `.gitignore` excludes all `.env` files.

### 3. Development

```bash
cd frontend
npm run dev   # runs on port 3000 (or next available)
```

- Frontend pages at `http://localhost:3000/`
- API endpoints at `http://localhost:3000/api/*`

### 4. Build before committing

The app autodeploys on Vercel, so verify the production build first:

```bash
cd frontend
npm run build
npm start
```

## API Routes

Backend functionality is implemented as Next.js API routes in `frontend/src/app/api/`. All routes are serverless functions that deploy automatically with the frontend.

- **`/api/events`** — Events management and retrieval
- **`/api/instagram`** — Instagram feed integration
- **`/api/orders`** — Order processing, payment handling, and admin management
  - `/api/orders/create` — Create new orders
  - `/api/orders/[orderId]` — Order details and updates
  - `/api/orders/admin/*` — Admin order management
  - `/api/orders/webhook/*` — Payment webhooks
- **`/api/search`** — Book search with text and status endpoints
- **`/api/square`** — Square POS integration
  - `/api/square/books` — Book inventory management
  - `/api/square/categories` — Category management
  - `/api/square/images` — Image handling

## Notes

- The Square access token is read server-side from environment variables and is never exposed in frontend code.
- All inventory and pricing are managed in the Square Dashboard.
- API routes handle all server-side logic and external integrations.
- No separate backend server, Firestore, or Firebase Cloud Functions are used; everything runs on Vercel's serverless platform.
- Tailwind theme colors are configured in `globals.css`.

Bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

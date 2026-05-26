# EZNDZ

> Project Management Platform for FRP-funded agricultural projects.

EZNDZ (Easy Bananas) is a comprehensive web-based platform designed to manage and monitor agricultural projects. It facilitates the end-to-end lifecycle of project management, including task tracking, file storage, contract generation and signing, dynamic form responses, and key performance indicator (KPI) monitoring.

## Features

- **Project & Activity Management:** Track project milestones, activities, and KPIs to ensure agricultural projects stay on track.
- **User & Access Management:** Role-based access control with secure authentication via JWT. Supports invite-based onboarding.
- **Contract & Form Handling:** Dynamically handle form submissions and generate/sign PDF contracts.
- **Cloud File Storage:** Integration with Cloudflare R2 (S3-compatible) for secure, scalable file uploads and pre-signed URL downloads.
- **AI Integration:** Leverages Google Generative AI for intelligent data processing or assistance within the platform.
- **Email Notifications:** Uses a custom Google Apps Script webhook to deliver automated system emails.
- **Responsive Web Interface:** Lightweight vanilla HTML/JS/CSS frontend built for speed, complete with Service Worker caching.

## Tech Stack

### Backend
- **Runtime:** Node.js (v22 recommended)
- **Framework:** Express.js
- **Database:** MongoDB (via Mongoose)
- **Authentication:** JSON Web Tokens (JWT) & bcrypt
- **PDF Processing:** `pdfkit`, `pdfjs-dist`, and `@napi-rs/canvas`

### Frontend
- **Stack:** Vanilla HTML5, CSS3, JavaScript (ES6 modules)
- **Architecture:** Statically served from the `/public` directory.

### External Services
- **Storage:** Cloudflare R2 (AWS S3 SDK)
- **AI:** Google Gemini API
- **Email:** Google Apps Script Web App Webhook

## Technical Specifications & Build

The application uses modern ES module syntax (`"type": "module"`) and is structured to be easily deployed via Docker.

### Directory Structure
- `server/` - Backend Node.js code. Contains:
  - `models/` - Mongoose database schemas.
  - `routes/` - Express API endpoints.
  - `utils/` - Helpers (Auth, DB connection, R2 storage, Email).
  - `index.js` - Main application entry point.
- `public/` - Frontend assets (HTML pages, CSS, JS scripts).

### Docker Environment
A `Dockerfile` is included and optimized for **Node 22 Alpine**. Since the application generates and manipulates PDFs, the image installs native system dependencies for `@napi-rs/canvas` and Skia graphics (e.g., `fontconfig`, `freetype`, `python3`, `make`, `g++`).

The build runs securely as a non-root user (`appuser`) and exposes port `7860`, ensuring compatibility with platforms like HuggingFace Spaces and Google Cloud Run.

## Setup & Installation

### Prerequisites
- **Docker & Docker Compose** (Recommended for easiest local setup)
- **Node.js** (v22+) if running bare-metal
- **MongoDB** (Atlas or local instance)

### 1. Environment Variables
Create a `.env` file in the root directory. You will need to configure the following variables:

```env
# MongoDB
MONGODB_URI=mongodb+srv://<user>:<pass>@<cluster>/ezndz

# Cloudflare R2 Storage (S3-Compatible)
R2_ACCOUNT_ID=your_account_id
R2_ACCESS_KEY_ID=your_access_key
R2_SECRET_ACCESS_KEY=your_secret_key
R2_BUCKET_NAME=ezndz
R2_ENDPOINT=https://<account_id>.r2.cloudflarestorage.com

# Authentication & Security
JWT_SECRET=your_super_secret_jwt_string

# Email Configuration (Google Apps Script)
APPS_SCRIPT_URL=your_apps_script_webhook_url
APPS_SCRIPT_KEY=your_apps_script_auth_key

# Application Settings
PORT=7860
APP_URL=http://localhost:7860
NODE_ENV=development

# Default Admin Credentials
DEFAULT_PM_EMAIL=admin@example.com
DEFAULT_PM_NAME=Admin User
DEFAULT_PM_PASSWORD=ChangeMe123!

# AI Integration
GEMINI_API_KEY=your_google_gemini_api_key
```

### 2. Running with Docker Compose (Local Dev)
The easiest way to spin up the full stack (App + Local MongoDB) is using Docker Compose:

```bash
docker-compose up -d --build
```
The application will be accessible at `http://localhost:7860`. The `public` folder is mapped as a volume, so frontend changes will reflect immediately without rebuilding.

### 3. Running Manually (Bare-metal)

1. Install dependencies:
   ```bash
   npm install
   ```
2. Start your local MongoDB server or ensure `MONGODB_URI` points to a valid cluster.
3. Start the development server:
   ```bash
   npm run dev
   ```
   *(For production, run `npm start` which executes `node server/index.js`)*

### 4. Initialization & Seeding
To initialize the database or generate dummy data (which also automatically creates the default Project Manager defined in the `.env` file):

```bash
npm run seed
```
To wipe the database:
```bash
npm run reset
```

## License
ISC

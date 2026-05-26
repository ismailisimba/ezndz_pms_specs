# EZNDZ

> Project Management Platform for FRP-funded agricultural projects.

EZNDZ (Easy Bananas) is a comprehensive web-based platform designed to manage and monitor agricultural projects. It facilitates the end-to-end lifecycle of project management, including task tracking, file storage, contract generation and signing, dynamic form responses, and key performance indicator (KPI) monitoring.

## Features & How They Work

### 1. Project & Activity Management
- **Projects**: The core entity. Administrators can set up agricultural projects and define their overarching goals.
- **Activities & KPIs**: Projects are broken down into measurable activities and milestones. Key Performance Indicators (KPIs) are tracked against these activities to ensure agricultural projects stay on track and meet their funding obligations.

### 2. User & Access Management
- **Role-Based Access Control (RBAC)**: Secure authentication via JWT (JSON Web Tokens). Supports distinct roles such as Project Managers, Employees, and external Vendors.
- **Onboarding**: Supports an invite-based onboarding process where users receive a secure token via email to set up their accounts.

### 3. Contract Generation & Signing Workflow
- **Dynamic PDF Generation**: Automatically generates printable PDF contracts using `pdfkit` based on predefined data (vendor details, scope of work, deliverables, payment terms).
- **Approval Workflow**: Contracts go through a structured lifecycle (Draft ➔ Pending Approval ➔ Approved ➔ Executed). Project Managers review and approve contracts before signing.
- **Digital & Manual Signatures**: 
  - **Digital**: Vendors and representatives can sign directly via a secure public link. Signatures are drawn on an HTML canvas, saved as PNGs, and embedded into the final PDF.
  - **Manual/Scanned**: Physical signed contracts can be uploaded. The platform uses Google Generative AI (Gemini) to extract images of the pages, intelligently detect the bounding boxes of the physical signatures, crop them, and map them to the document automatically.

### 4. Dynamic Forms & Data Collection
- **Form Builder**: Admins can build custom forms with various field types (text, textarea, checkbox, signature, rating, and GPS coordinates).
- **Public Links & Offline Support**: Forms can be shared via public URLs (with rate-limiting) for unauthenticated respondents (e.g., field agents or farmers). Supports offline submissions that sync when connectivity is restored.
- **AI-Powered Form Ingestion**: Similar to contracts, scanned physical forms can be uploaded. The Gemini API parses the scanned images, extracts the handwritten or printed data, crops signatures, and populates the digital form responses automatically.
- **Printable PDFs**: The platform can generate blank, structured, and printable PDF versions of any form for field distribution.

### 5. Cloud File Storage
- **Cloudflare R2 Integration**: Fully integrated with Cloudflare R2 (S3-compatible) using the AWS S3 SDK.
- **Secure Access**: All uploaded files (contract attachments, form evidence, signature PNGs) are stored securely. The platform generates temporary pre-signed URLs to grant secure access for downloading and viewing.

### 6. Automated Email Notifications
- **Google Apps Script Webhook**: Uses a custom Google Apps Script endpoint to bypass traditional SMTP limits and complexities. It delivers automated system emails such as contract approval requests, form submission receipts, and user invitations.

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

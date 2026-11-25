# HackManager

HackManager runs college hackathons from team registration through submissions, check-in, judging and results. React + TypeScript calls the Express API. Express uses Drizzle with SQLite, bcrypt and JWT. A private Python service handles face photos.

## How to start the website

**Prerequisites:** Node.js 22 or newer (tested on Node 24) and npm. Git is needed only to clone. No other setup is required; the SQLite database is created automatically.

1. **Clone the repo and enter it** (skip if you already have it):

   ```powershell
   git clone -b Aashish https://github.com/ShreyDudie/HackManager
   cd HackManager
   ```

2. **Terminal 1, start the backend API** (port 8000). Run these from the repository root:

   ```powershell
   npm --prefix backend-node install
   npm --prefix backend-node run db:migrate
   npm --prefix backend-node run dev
   ```

   Wait for `HackManager API listening on 127.0.0.1:8000`.

3. **Terminal 2, start the frontend** (port 8080):

   ```powershell
   npm --prefix Frontend install
   npm --prefix Frontend run dev
   ```

4. **Open <http://localhost:8080>** in your browser. Vite proxies `/api` and `/media` to the backend, so no `.env` files are needed for local development (copy `backend-node/.env.example` to `backend-node/.env` only to override defaults).

5. **Log in.** Seed the three demo accounts once (safe to re-run; it resets their password):

   ```powershell
   npm --prefix backend-node run seed
   ```

   | Role | Email | Password |
   | --- | --- | --- |
   | Admin / organizer | `admin@hci` | `password123` |
   | Judge | `judge@hci` | `password123` |
   | Student | `student@hci` | `password123` |

   Choose the matching role on the login page. These are local development credentials only; never seed them on a deployed server. Students can also sign up in the app. To create your own admin or judge instead:

   ```powershell
   $env:ACCOUNT_EMAIL = 'admin@example.edu'
   $env:ACCOUNT_NAME = 'Admin'
   $env:ACCOUNT_ROLE = 'admin'          # or 'judge'
   $env:ACCOUNT_PASSWORD = 'at-least-12-characters'
   npm --prefix backend-node run account:create
   Remove-Item Env:ACCOUNT_PASSWORD
   ```

**Troubleshooting**

- `Cannot find module ...tsx...` or `node-gyp` / "find VS" errors during install: the install did not finish. Run `Remove-Item -Recurse -Force backend-node
ode_modules` and then `npm --prefix backend-node install` again. This repo pins `better-sqlite3` 12.11.1, which downloads a prebuilt binary and needs no C++ compiler. Do not upgrade to 13.x, which compiles from source and requires Visual Studio Build Tools.
- Port 8000 or 8080 already in use: stop the other process. The frontend uses a fixed port (`strictPort`).
- Network errors in the browser: confirm the backend terminal is still running.
- Face verification is optional. To enable it, use Python 3.11 in a virtual environment from `services/face-service`, install `requirements.txt`, and run `python -m uvicorn app:app --host 127.0.0.1 --port 8001`. Both services must use the same `FACE_SERVICE_KEY`. Other features work without it.

**Checks:** `npm --prefix backend-node run check`, `npm --prefix backend-node test`, `npm --prefix Frontend test`, `npm --prefix Frontend run build`. See [account provisioning and browser checks](docs/express-development.md).

| Directory | Purpose |
| --- | --- |
| `Frontend/` | React app and Playwright browser journeys |
| `backend-node/` | Active API, Drizzle migrations and account/import tools |
| `services/face-service/` | Python image processing |
| `data/` | Local SQLite, private uploads and face profiles |
| `backend/` | Retired Django snapshot and original media for migration recovery |

See [cutover and recovery](docs/migration-status.md), [deployment](DEPLOYMENT.md) and [API contracts](docs/api-contracts.md).

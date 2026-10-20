# Skillstep

Skillstep is a React job-matching web app with a Python API. It stores accounts, candidate profiles, sessions, saved job IDs, and cached job listings in SQLite. It never fills the feed with invented sample jobs.

## Run locally

1. Copy `.env.example` to `.env` and add your provider credentials.
2. Run `python server.py` from this directory.
3. Open `http://localhost:4173`.

The frontend uses React and HTM from `esm.sh`; the first browser load needs internet access. The backend uses only Python's standard library. The API key stays in the server environment and is never sent to the browser. `/health` reports whether the server and jobs configuration are present.

## Live job data

Set `JOBS_API_URL`, `JOBS_API_KEY`, and `JOBS_API_SOURCE` on the server. Skillstep requests listings from the configured provider, caches them in SQLite, and does not fabricate listings when the provider errors or returns no data. Provider refreshes are rate limited by `JOBS_REFRESH_SECONDS` (minimum 60 seconds; honor your provider plan). A manual refresh cannot bypass this interval. Application URLs are screened for HTTPS and employer-specific or known hiring-system destinations; this is a safety filter, not a guarantee of job authenticity. Verify vacancies on the employer's careers site before sharing sensitive information.

The IndianAPI adapter currently expects `GET {JOBS_API_URL}/jobs?limit=50`, the `X-Api-Key` header, and job records with company, title, location, description, and application URL fields. Check the provider's current docs and account plan before deployment.

## Authentication

Email/password accounts work without an OAuth provider. Passwords are stored as salted PBKDF2-HMAC-SHA256 digests; session tokens are random and only their SHA-256 hashes are stored. Google and GitHub login are optional: configure `GOOGLE_CLIENT_ID` and/or `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, and `GITHUB_REDIRECT_URI`. Register the production HTTPS origins/callback with each provider. Use the same public origin for the frontend and API.

### Gmail password reset

Configure `APP_BASE_URL` with the public HTTPS site URL and set `SMTP_HOST=smtp.gmail.com`, `SMTP_PORT=587`, `SMTP_USER`, `SMTP_FROM_EMAIL`, and `SMTP_PASSWORD`. For Gmail, create a Google App Password after enabling 2-Step Verification; never use or publish the normal Gmail password. Keep all SMTP settings in server secrets. The reset link expires after 30 minutes, is single-use, and carries its random token in the URL fragment so it is not sent in HTTP requests or server access logs. The backend stores only a token hash, limits reset requests, and revokes existing sessions after a password is changed. If SMTP is not configured, the app reports that reset email is unavailable rather than claiming an email was sent.

## Public deployment (Render)

`render.yaml` provides a web service with a persistent disk mounted at `/var/data`. Set the jobs credentials and any OAuth values in the host dashboard; do not commit `.env` or secrets. Render persistent disks require a paid web-service plan. Use a disk-backed `SKILLSTEP_DATABASE` path for durable SQLite data, set `COOKIE_SECURE=true`, use HTTPS, and enable backups for the volume. Do not run multiple replicas sharing SQLite; move to managed PostgreSQL before horizontal scaling.

The included Python `http.server` implementation is suitable for a small student project and single-instance deployment, not high-traffic production. Before opening it to broad public use, add a managed database, general API rate limiting, account email verification, privacy policy and retention schedule, monitoring/backups, and a security review. Users can delete their account and associated profile/saved-job data through the app. Age is collected for match context; avoid collecting it if the product does not need it.

## Stored data

SQLite is created at `SKILLSTEP_DATABASE` (defaults to `data/skillstep.sqlite3` inside the project folder). Back up this file while the service is stopped or use SQLite's backup API. User profiles contain personal information; restrict access to the database and delete it when no longer needed.
"# stepskill" 
"# stepskill" 

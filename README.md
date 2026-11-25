# FIXFIND

FIXFIND connects South African drivers with local vehicle repair workshops. Customers can search public listings; workshop owners can publish profiles and request Standard (R199/month) or Featured (R399/month) subscriptions. It includes customer/workshop registration, secure password hashing, PostgreSQL-backed sessions, admin user/workshop/subscription management, and a responsive mobile-friendly website.

## Subscription payments: manual EFT

An administrator configures the FIXFIND receiving account in the Admin dashboard under **Subscription payout bank details**. Workshop owners choose a plan and receive the bank details and a unique FIXFIND payment reference. They transfer money from their own bank and submit the transaction reference. A subscription stays **pending** until an administrator independently confirms the cleared deposit and changes it to **active**. Pending subscriptions are not featured in search. The application cannot access anyone's bank, initiate transfers, or automatically verify EFTs. Never activate based only on a screenshot or payer-supplied reference; check your bank independently.

Bank details are stored in the application's PostgreSQL database and shown only to signed-in workshop accounts and administrators. Only enter an account intended to receive FIXFIND business payments. Do not enter bank login credentials, PINs, passwords, or card information. PayFast is not yet integrated; the EFT process is manual.

## Launch on Render (phone-friendly steps)

1. Open Render in your phone browser and sign in or create an account. Your FIXFIND repository is `venteryanica/FIXDIND` on GitHub.
2. In Render, tap **New** → **Blueprint** (sometimes shown as **Blueprint Instance**), connect GitHub if asked, and select `venteryanica/FIXDIND`. Render reads `render.yaml` to create the Node web service and PostgreSQL database.
3. Review the services and database/plan pricing Render shows before approving the Blueprint. Confirm the database connection is included. The Blueprint asks for `ADMIN_EMAIL` and `ADMIN_PASSWORD`: enter an email you control and a unique password of at least 12 characters. Keep this password private.
4. Approve deployment. Render installs the Node dependencies, creates the PostgreSQL tables automatically at startup, and supplies a persistent `SESSION_SECRET` and database connection string.
5. Wait for the web service to show **Live**. Open its `onrender.com` URL on your Android phone. Sign in using the admin email/password you supplied.
6. In Admin dashboard, enter FIXFIND's bank receiving details and save. Test a workshop registration/profile and subscription request. Confirm the workshop sees EFT instructions and the pending payment; test activation from the admin subscriptions table only after verifying a real deposit.

If Render says a database plan in `render.yaml` is unavailable or displays unexpected pricing, stop and review the plan options in your Render dashboard before continuing. Render pricing and plan availability can change. Keep database backups enabled for real customer data. `/api/health` is the health-check endpoint.

## Local development (optional)

Requires Node.js 20+ and PostgreSQL. Create a database, copy `.env.example` to `.env`, set `DATABASE_URL`, `SESSION_SECRET`, `ADMIN_EMAIL` and `ADMIN_PASSWORD`, then run `npm install` and `npm start`. Open `http://localhost:3000`. Never commit `.env`.

## Security and future PayFast

Passwords are hashed with bcrypt; sessions and application records are persisted in PostgreSQL; admin APIs are role-protected; login/registration is rate-limited; production cookies require HTTPS. Set a strong admin password and keep Render environment variables private. Before adding PayFast or any automated payments, implement current provider sandbox testing, server-side signature and notification verification, idempotency, and refund/cancellation handling. Never mark payments paid based only on a browser redirect or uploaded proof.

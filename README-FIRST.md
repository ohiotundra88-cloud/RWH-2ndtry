# RWH-2ndtry — Start Here

This repository is a complete VS Code handoff of the Ride With Huntington / Team Huntington Hub application, based on the most recent `chris-updates` branch at commit `fbfe8469b000006e998a3028640a2154fe2403a4`, plus the recovered local fundraiser-approval presentation changes and handoff documents listed in `EXPORT-MANIFEST.md`.

## Open in VS Code

1. Install Node.js 22.18 or newer and Git.
2. Clone this repository and open the folder containing `package.json`.
3. Accept the recommended extensions from `.vscode/extensions.json`.
4. Run `npm ci`.
5. Copy `.env.example` to `.env.local` and supply values from your own services.
6. Run `npm run check`.
7. Read `docs/VS-CODE-RECREATION-GUIDE.md`, then `README.md` and the rest of `docs/`.

## Authentication boundary

No Aspire Identity credential, client secret, registration, or concrete Aspire endpoint is included. The existing OIDC integration remains isolated in `src/server/session.server.ts` so it can be adapted to Microsoft Entra ID or another approved provider. Safe placeholders are provided in `.env.example` and `wrangler.jsonc`.

## Intentionally excluded

- `.git` history from the original repository
- real `.env` files and secrets
- production database records and uploaded files
- `node_modules`, `.output`, caches, logs, and generated build output
- access to existing Cloudflare, identity-provider, database, email, DNS, or Azure accounts

See `EXPORT-MANIFEST.md` for provenance and `VALIDATION-REPORT.md` for verified results.

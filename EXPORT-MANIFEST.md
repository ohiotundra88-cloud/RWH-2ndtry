# Export Manifest

- Export name: `RWH-2ndtry`
- Product: Ride With Huntington / Team Huntington Hub
- Export date: 2026-10-17
- Source branch: `chris-updates`
- Base commit: `fbfe8469b000006e998a3028640a2154fe2403a4`
- Source remote: `ohiotundra88-cloud/ride-with-huntington`
- Export method: tracked files from the base commit plus the reviewed working-tree overlay below
- Original Git metadata included: no
- Secrets included: no
- Production data/uploads included: no
- Dependencies/build output included: no

## Recovered working-tree overlay

These files were present outside the base commit and were deliberately preserved:

- `src/components/ApprovalTracker.tsx`
- `src/lib/fundraiser-requests.shared.ts`
- `src/lib/fundraising-pages.server.ts`
- `src/routes/my-fundraisers.$id.tsx`
- `tests/fundraiser-approval-presentation.test.ts`
- `docs/AZURE-REBUILD-REQUIREMENTS.md`
- `docs/AZURE-STATIC-SITE-REQUIREMENTS.md`
- `docs/VS-CODE-AZURE-FULL-REBUILD-PROMPT.md`
- `docs/VS-CODE-RECREATION-GUIDE.md`

## Handoff sanitization

The handoff removes concrete Aspire Identity endpoints and operational sender-domain defaults. OIDC client values are placeholders. The isolated provider adapter remains in source so a developer can replace it with Microsoft Entra ID or another approved provider without rewriting the rest of the application.

## Inventory

- 52 PostgreSQL migration files
- 14 TypeScript unit-test files
- VS Code extension, settings, and launch configuration
- Cloudflare and Node/Azure build paths
- architecture, configuration, deployment, Entra, data-export, Azure rebuild, and recreation documentation

## Exclusions

`.git`, `node_modules`, `.output`, build directories, caches, logs, private environment files, credentials, private keys, production exports, and uploaded production files are intentionally excluded.

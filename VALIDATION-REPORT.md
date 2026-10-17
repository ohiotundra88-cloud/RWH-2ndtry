# Validation Report

**Validated:** 2026-10-17  
**Source baseline:** `chris-updates` at `fbfe8469b000006e998a3028640a2154fe2403a4`, plus the reviewed overlay in `EXPORT-MANIFEST.md`

## Clean-room verification

Verification was performed from a newly created ZIP extracted into a disposable directory, not from the original working repository.

- ZIP integrity test: passed
- Clean `npm ci`: passed; 467 packages installed
- TypeScript (`npm run typecheck`): passed
- ESLint and accessible-control-name check (`npm run lint`): passed
- Unit tests (`npm test`): **118 passed, 0 failed, 0 skipped**
- Cloudflare production build (`npm run build`): passed
- PostgreSQL/database security suite (`npm run test:db`): not run because this machine does not have the required disposable PostgreSQL/PostgREST test environment configured

## Source inventory

- 52 PostgreSQL migrations
- 14 TypeScript unit-test files
- complete application routes, components, server functions, database types, email templates, jobs, static assets, VS Code configuration, deployment configuration, and documentation
- recovered fundraiser approval tracker presentation changes and regression test included

## Authentication and secret boundary

- Concrete Aspire Identity endpoints were removed from deployable configuration, source defaults, and type examples.
- No Aspire client secret, registration, or credential is included.
- `.env.example` contains placeholders only.
- Real `.env*` files, private keys, production data, uploads, dependencies, and build output are excluded.
- The isolated OIDC adapter remains in source and requires provider-specific configuration or replacement for Microsoft Entra ID/another approved provider.

## Dependency findings

`npm ci` reported 10 total dependency vulnerabilities: 5 high and 5 critical. A production-scoped `npm audit --omit=dev` reported **6 findings: 5 critical and 1 high**. The direct production finding is `@tanstack/react-start`; the remaining findings are transitive TanStack packages plus `source-map-js`. npm reports fixes are available.

These dependencies were not automatically changed during the handoff because dependency remediation changes the lockfile and requires a separate regression/security review.

## Warnings and scope limits

- Several installed packages emit deprecation warnings, including older React Email component packages, Recharts 2.x, and the installed ESLint release.
- The build emits non-fatal module-directive warnings from dependencies.
- Source delivery does not recreate production database records, uploaded files, identity-provider registrations, Cloudflare/Azure resources, DNS, email accounts, or secret values.
- Before production deployment, remediate the production dependency advisories and run the database security suite against a disposable PostgreSQL 17 environment.

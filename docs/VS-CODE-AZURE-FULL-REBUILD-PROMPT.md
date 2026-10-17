# Copy/Paste Prompt: Full Ride With Huntington Azure Rebuild in VS Code

> **Important clarification:** `pelotonia@huntington.com` is the sender mailbox/address, not an email-server hostname. The implementation must send through Huntington's approved Microsoft 365 transport—preferably Microsoft Graph with managed identity—or an approved SMTP relay. Never invent SMTP hostnames, usernames, passwords, tenant IDs, or permissions.

Copy everything inside the prompt block into the VS Code coding agent you want to use.

---

## Master implementation prompt

```text
You are the senior full-stack engineer and Azure platform engineer responsible for rebuilding and deploying the complete Ride With Huntington / Team Huntington Hub application.

Work directly inside the existing repository opened in VS Code. Do not create a simplified replacement, static-only site, prototype, mock-only application, or visual approximation. Preserve the current application as it exists today: its branding, layouts, responsive behavior, routes, data model, role restrictions, workflows, validation, accessibility, and business rules.

PRIMARY GOAL

Rebuild the full current Ride With Huntington application for production hosting in Microsoft Azure while preserving current behavior and appearance. Replace the temporary Cloudflare/Aspire/Resend platform integrations with Azure-compatible implementations. Use pelotonia@huntington.com as the From address for transactional communications and one-time-passcode authentication.

DO NOT STOP AFTER WRITING A PLAN. Inspect the repository, implement the required code and infrastructure, run the tests and builds, and produce the deployment and operating documentation. If credentials, tenant values, subscription access, DNS control, or mailbox permissions are missing, implement everything with explicit placeholders and provide the exact administrator steps required. Never invent credentials or claim that an external Azure resource was deployed when it was not.

REPOSITORY AND WORKING-TREE SAFETY

1. Begin with:
   - git branch --show-current
   - git rev-parse HEAD
   - git status --short
2. The intended Azure/self-host baseline is the self-host work at commit fbfe8469b000006e998a3028640a2154fe2403a4 or its current descendant.
3. The working tree may contain valuable modified and untracked files. Do not discard, reset, overwrite, stash, or checkout over them.
4. Review and preserve all current local changes, including current fundraiser approval presentation work and tests.
5. Do not edit generated src/routeTree.gen.ts manually.
6. Do not commit secrets, .env.local, certificates, private keys, production exports, or mailbox credentials.
7. Use LF line endings.

READ THESE FILES BEFORE CHANGING CODE

- README.md
- docs/ARCHITECTURE.md
- docs/CONFIGURATION.md
- docs/DEPLOYMENT.md
- docs/ENTRA.md
- docs/DATA-EXPORT.md
- docs/IT-ALLOWLIST.md
- docs/VISUAL-STUDIO.md
- docs/VS-CODE-RECREATION-GUIDE.md
- docs/AZURE-REBUILD-REQUIREMENTS.md
- package.json
- .env.example
- .github/workflows/ci.yml
- src/server/session.server.ts
- src/server/backend.server.ts
- src/server/runtime.ts
- src/start.ts
- src/lib/email-templates/send-email.ts
- jobs/pelotonia-sync/*
- all SQL migrations and database security tests

CURRENT APPLICATION STACK TO PRESERVE

- React 19 and TypeScript.
- Tailwind CSS 4 and shadcn/Radix UI.
- TanStack Router, Query, and Start.
- Vite 8 and Nitro 3.
- Node 22.18 or newer.
- PostgreSQL 17.
- PostgREST with supabase-js used only as the typed query builder.
- PostgreSQL row-level security as a mandatory authorization control.
- React Email templates.
- Existing file-storage interface.
- Existing Pelotonia/PledgeIt synchronization logic.
- Existing tests and CI quality gates.

FEATURE PARITY REQUIREMENTS

Preserve all currently implemented features and routes, including:

1. Home and dashboard.
2. Rider, volunteer, and challenger registration journey.
3. Pelotonia registration, ride route, travel, hotel, bike rental, apparel, mailing address, readiness, and progress.
4. Team snapshot, rider progress, subteams, fundraising totals, and Pelotonia Kids.
5. Fundraiser request submission, policy questions, captain selection, attachments, resubmission, and status tracking.
6. Captain approval followed by parallel Legal, Risk, Compliance, and Marketing review, then co-chair approval.
7. Fundraiser approval history and current visual approval tracker.
8. Public approved fundraiser pages, raffle information, payouts, year summaries, and clearly labeled demo checkout unless real payments are separately approved.
9. Public fundraising events and invite-only team events with the existing behavior.
10. Inbox, announcements, messages, email opt-out, and notification templates.
11. Resources, FAQ, expense guide, packing guide, family guide, and health/network self-check.
12. Vendor CRM, contacts, contributions, sponsorship tiers, Pelotonia Kids gifts, rider slots, attachments, and audit history.
13. Captains Lounge.
14. Role and participant administration, branding, FAQs, test email, site settings, and season administration.
15. Existing mobile behavior and accessibility semantics.

Do not redesign pages, rename routes, replace business rules, or remove features merely to simplify Azure deployment. If a current feature is prototype-only or browser-local, migrate it to a production database implementation or document it as a specific unresolved blocker requiring business approval.

TARGET AZURE ARCHITECTURE

Implement the following production target unless the repository's approved requirements document specifies a stricter Huntington standard:

1. Azure App Service for Linux for the Node web application.
   - Build with npm run build:node.
   - Run node .output/server/index.mjs.
   - Bind to the Azure PORT and 0.0.0.0.
   - Enable HTTPS only, Always On, managed identity, health checks, VNet integration, and staging slot deployment.
2. Azure Database for PostgreSQL Flexible Server 17.
   - Private access only.
   - TLS required.
   - No public database endpoint.
   - Preserve the existing compatibility roles, auth schema, migrations, RLS policies, functions, triggers, and tests.
3. Azure Container Apps internal environment for:
   - PostgREST pinned to a tested version.
   - nginx sidecar/gateway that exposes /rest/v1 internally and strips the prefix before forwarding to PostgREST.
   - No public PostgREST ingress.
4. Azure Container Apps Job for the nightly Pelotonia synchronization.
5. Azure Blob Storage for avatars, branding, captain documents, event fliers, fundraising assets, and vendor files.
6. Azure Key Vault for all production secrets.
7. Managed identities for the web app, job, Blob access, Key Vault access, and Microsoft Graph access where Huntington approves it.
8. Azure Container Registry for container images.
9. Application Insights, Log Analytics, Azure Monitor alerts, availability tests, and structured audit logging.
10. Azure Front Door/WAF if required by Huntington policy.
11. Canonical production origin: https://www.ridewithhuntington.com.
12. Redirect https://ridewithhuntington.com to the canonical www origin while preserving path and query string.

Create Bicep or Huntington-approved Terraform for the complete Azure environment. Do not make the production environment dependent on undocumented manual portal steps. Document the unavoidable Entra/Exchange/DNS administrator actions.

AUTHENTICATION REQUIREMENT: EMAIL OTP

Replace the current Aspire Identity login with secure email one-time-passcode authentication. The primary login identifier is the user's Huntington email address. OTP messages must be sent from pelotonia@huntington.com.

The OTP design must satisfy all of these requirements:

1. Only valid @huntington.com addresses and explicitly approved addresses in hub_email_allowlist may receive a usable login challenge.
2. Use a generic response such as “If this address is eligible, a code has been sent.” Do not reveal whether an account exists or is allowlisted.
3. Generate the code using a cryptographically secure random-number generator.
4. Use a six-digit, zero-padded numeric code unless Huntington security requires a stronger format.
5. Expire each code after 10 minutes or a stricter approved period.
6. Make each code single-use.
7. Invalidate older active challenges when a newer challenge is issued for the same email and purpose.
8. Limit verification attempts, recommended maximum five attempts per challenge.
9. Rate-limit requests by normalized email and source network identifier. Include a request cooldown and hourly cap.
10. Do not store the plaintext OTP. Store an HMAC-SHA-256 digest using a dedicated server-side OTP pepper from Key Vault, incorporating the challenge ID, normalized email, purpose, and code.
11. Compare digests using constant-time comparison.
12. Do not log plaintext OTP values or include them in telemetry, exception messages, URLs, query strings, analytics, or audit records.
13. Store challenges in PostgreSQL in a server-only table that anon and authenticated API roles cannot read or write.
14. Record created_at, expires_at, consumed_at, attempt count, send status, and limited security/audit metadata.
15. Keep raw IP addresses out of long-lived OTP records where policy requires minimization; use an HMAC or approved truncated/network representation for rate limiting.
16. Create server-side, revocable sessions after successful verification.
17. Store only a signed opaque session identifier in the cookie; store session state in PostgreSQL.
18. Session cookie must be Secure, HttpOnly, SameSite=Lax or stricter where compatible, Path=/, and use a __Host- prefix in production.
19. Implement idle and absolute session expiration. Use an eight-hour absolute maximum unless Huntington specifies another value.
20. Logout must revoke the server-side session immediately.
21. Add cleanup for expired challenges and sessions.
22. Preserve the current currentUser(request) contract so the rest of the application requires minimal change.
23. Map a verified email to the existing auth.users and profiles records without creating duplicates. Pre-created roster records must retain their data on first login.
24. Continue enforcing Huntington email/allowlist rules in PostgreSQL, not only in TypeScript.
25. Show a clear, accessible error page for denied or expired authentication without creating redirect loops.
26. Add CSRF protection to OTP request, verification, logout, and all state-changing server functions as appropriate.
27. Add audit events for OTP requested, send failed, verification failed, verification succeeded, session created, session revoked, and rate limited. Never record the code.
28. Add tests for expiry, single use, old-code invalidation, attempt limits, rate limits, generic responses, allowlist handling, duplicate-user prevention, session revocation, cookie flags, and timing-safe comparison.
29. Email OTP is not phishing-resistant. Document this limitation and require Huntington security approval. If Huntington requires phishing-resistant MFA, retain the session/user interface but substitute Entra ID authentication rather than weakening Entra controls.

OTP ROUTES AND USER EXPERIENCE

Implement accessible routes or server handlers equivalent to:

- GET /signin — email-entry page.
- POST /auth/otp/request — request a code; always returns a generic result.
- GET /signin/code — code-entry page.
- POST /auth/otp/verify — verify code and create session.
- POST or GET /auth/logout using the project's established safe pattern, with CSRF protection when state changing.
- GET /auth/me — preserve the current response contract.

The login UI must match the existing site design, work on mobile, support paste/autofill, expose correct labels and error summaries, and use autocomplete="one-time-code" where appropriate. The code must never be placed in a URL.

EMAIL REQUIREMENT: PELOTONIA MAILBOX

Treat pelotonia@huntington.com as the sender mailbox for:

- OTP emails.
- Fundraiser request notifications.
- Review-stage notifications.
- Approval/decision notifications.
- Team announcements.
- Event invitations and cancellations.
- Test email from the administrator interface.
- Other existing transactional templates.

Preferred production transport:

1. Microsoft Graph sendMail API.
2. Use the App Service managed identity or a dedicated Entra application identity.
3. Request only the minimum Mail.Send permission required.
4. Huntington Exchange/Entra administrators must restrict the application to pelotonia@huntington.com using the current approved Exchange Online Application RBAC or application-access control mechanism.
5. Send through POST /users/pelotonia@huntington.com/sendMail or the current supported equivalent.
6. Do not use a human mailbox password, legacy basic authentication, or embedded SMTP credentials.
7. Implement Graph sending as a new EMAIL_PROVIDER value such as graph.
8. Keep the email provider behind the current sendTemplateEmail interface so callers do not change.
9. Set EMAIL_FROM to Team Huntington <pelotonia@huntington.com>.
10. Support configurable Reply-To separately from From.
11. Keep EMAIL_REDIRECT_TO mandatory in every nonproduction environment.
12. OTP templates must never be redirected to real users in development/test.
13. Log provider message IDs and safe delivery status, but not sensitive message bodies or OTP codes.
14. Use timeouts, bounded retries for transient failures, and idempotency controls where possible.
15. OTP request responses must remain generic even if email delivery fails; operational alerts must reveal the failure to administrators.

If Huntington requires an SMTP relay instead of Microsoft Graph, implement it as another provider behind the same interface. Require the actual approved SMTP host, port, TLS mode, authentication mechanism, and relay restrictions from Huntington IT. Do not guess them. Never describe pelotonia@huntington.com itself as the SMTP server.

Microsoft references for the implementer:

- Graph sendMail: https://learn.microsoft.com/en-us/graph/api/user-sendmail?view=graph-rest-1.0
- App Service managed identity accessing Graph: https://learn.microsoft.com/en-us/azure/app-service/tutorial-connect-app-access-microsoft-graph-as-app-javascript
- Microsoft Graph permissions: https://learn.microsoft.com/en-us/graph/permissions-reference
- NIST authenticator guidance: https://pages.nist.gov/800-63-4/sp800-63b.html

DATABASE REQUIREMENTS

1. Preserve PostgreSQL 17 and PostgREST.
2. Preserve all current migrations and create new timestamped migrations; never edit an already applied migration.
3. Add tables/functions for OTP challenges, rate-limiting records if needed, revocable sessions, and security audit events.
4. Revoke direct anon/authenticated access to authentication-secret tables.
5. Use service_role only after explicit server authorization.
6. Preserve and rerun every existing RLS and registration security test.
7. Add database tests proving that normal authenticated/anonymous roles cannot read OTP digests or sessions belonging to other users.
8. Introduce a production migration ledger using Flyway, dbmate, or an approved equivalent.
9. Reload PostgREST schema cache after migrations.
10. Support both a fresh database and migration of the current preview database and files.

AZURE BLOB STORAGE REQUIREMENTS

1. Implement the existing put/get/delete adapter using @azure/storage-blob and DefaultAzureCredential.
2. Use the web app's managed identity.
3. Grant Storage Blob Data Contributor at the narrowest practical scope.
4. Keep the container private.
5. Preserve existing object keys and logical bucket prefixes.
6. Preserve MIME metadata.
7. Retain safe inline/download headers and block active uploaded content from executing on the Hub origin.
8. Add tests for upload, download, not found, delete, content type, invalid paths, and unauthorized access.
9. Provide a migration utility and verification report for moving existing R2 objects to Azure Blob Storage.

PELOTONIA JOB REQUIREMENTS

1. Preserve current sync behavior and fallback logic.
2. Deploy sync.mjs as an Azure Container Apps scheduled job.
3. Configure at least a 30-minute timeout.
4. Store secrets in Key Vault references.
5. Allow only required outbound destinations.
6. Alert if the latest successful sync is older than 26 hours.
7. Keep last-known good data when upstream public endpoints fail.
8. Do not claim the upstream Pelotonia/PledgeIt endpoints are official or stable.

PROTOTYPE DEBT TO COMPLETE

Move browser-only Super User administrative content and feature flags from localStorage/memory into PostgreSQL with:

- New migrations.
- Typed data access.
- Server functions.
- Role checks.
- RLS or trusted server-only access as appropriate.
- Validation.
- Audit history.
- Tests.

Do not silently drop this content. Preserve the current UI and behavior while replacing the persistence implementation.

PAYMENT BOUNDARY

The current fundraiser checkout is a demo and moves no money. Preserve the demo banner and behavior unless Huntington separately approves a real payment provider, merchant account, PCI scope, legal review, and security requirements. Do not add real card processing as part of this task.

CONFIGURATION REQUIREMENTS

Update .env.example and docs/CONFIGURATION.md with safe placeholders for at least:

- PUBLIC_ORIGIN
- HUB_REQUIRE_SIGN_IN
- SESSION_SECRET or session signing/pepper settings
- OTP_PEPPER
- OTP_TTL_SECONDS
- OTP_MAX_ATTEMPTS
- OTP_REQUEST_COOLDOWN_SECONDS
- OTP_REQUESTS_PER_HOUR
- HUB_DB_URL
- HUB_DB_JWT_SECRET
- AZURE_STORAGE_ACCOUNT_URL
- AZURE_STORAGE_CONTAINER
- EMAIL_PROVIDER=graph
- EMAIL_FROM=Team Huntington <pelotonia@huntington.com>
- EMAIL_REDIRECT_TO
- GRAPH_SENDER_USER=pelotonia@huntington.com
- Any tenant/client settings that are genuinely required when managed identity cannot be used
- HUB_REST_URL
- Pelotonia job settings
- Application Insights/OpenTelemetry settings

Do not place production values in the repository.

INFRASTRUCTURE-AS-CODE REQUIREMENTS

Create Bicep or approved Terraform that provisions/configures:

- Resource groups by environment.
- VNet and required subnets.
- App Service plan and web app.
- App Service managed identity, VNet integration, health check, staging slot, settings, and Key Vault references.
- PostgreSQL Flexible Server 17 and private DNS/networking.
- Container Apps environment.
- Internal PostgREST/nginx Container App.
- Scheduled synchronization job.
- Blob Storage and private container.
- Key Vault and RBAC.
- Azure Container Registry and managed-identity image pulls.
- Application Insights and Log Analytics.
- Alert rules and availability tests.
- Optional Front Door/WAF.
- Custom-domain/DNS outputs and administrator instructions.

Use parameter files for development, staging, and production. Production and nonproduction must not share databases, storage, identities, secrets, or email behavior.

CI/CD REQUIREMENTS

Extend GitHub Actions or provide Azure DevOps pipelines that:

1. Use npm ci from the committed lockfile.
2. Run formatting/lint.
3. Run TypeScript type checking.
4. Run all unit tests.
5. Rebuild the PostgreSQL test database and run all SQL security tests.
6. Check generated database types for drift.
7. Build the Node production output.
8. Build and scan container images.
9. Validate Bicep/Terraform.
10. Scan dependencies and secrets.
11. Publish immutable artifacts/images.
12. Deploy development, then staging, then production with approvals.
13. Run database migrations once as a controlled release step.
14. Run postdeployment smoke tests.
15. Use GitHub OIDC/federated credentials or Huntington's approved workload identity; do not store long-lived Azure passwords.
16. Support rollback to the prior web artifact and prior container revision.

TDD AND VERIFICATION PROCESS

Use strict test-driven development for every behavior change:

1. Write one focused failing test.
2. Run it and verify that it fails for the expected missing behavior.
3. Implement the minimum change.
4. Run the focused test until it passes.
5. Run the full affected suite.
6. Refactor only while green.
7. Repeat for the next vertical behavior slice.

Required tests include:

- OTP generation and digesting.
- OTP expiry, single use, resend invalidation, attempt limits, and rate limits.
- Generic anti-enumeration responses.
- Allowlist and Huntington-domain enforcement.
- Email provider behavior with a mocked Graph transport.
- No OTP in logs or persisted plaintext.
- Session creation, lookup, expiry, idle timeout, revocation, and cookie flags.
- Existing-account claiming and duplicate prevention.
- Database permissions around OTP/session tables.
- Blob storage adapter.
- Current approval workflow and RLS protections.
- Prototype-content persistence.
- Azure configuration validation.

Run and report the real output from:

- npm ci
- npm run typecheck
- npm run lint
- npm test
- npm run test:db
- npm run build:node
- Infrastructure validation commands
- Container build and scan

Do not report success if a command was not run or did not pass.

OBSERVABILITY AND SECURITY

1. Add structured logging with correlation IDs.
2. Redact cookies, JWTs, OTPs, email bodies, database passwords, client secrets, and authorization codes.
3. Add health and readiness endpoints.
4. Alert on elevated 5xx, unavailable database/gateway, failed OTP email sends, excessive OTP failures/rate limits, stale Pelotonia sync, storage authorization errors, and expiring certificates.
5. Add CSP, HSTS, nosniff, referrer policy, permissions policy, and frame-ancestor protection.
6. Preserve safe file-serving headers.
7. Add rate limits for OTP and other abuse-prone public endpoints.
8. Document email OTP's security limitations and the path to Entra MFA if required.
9. Produce a threat model covering OTP abuse, mailbox compromise, Graph permission scope, session theft, privilege escalation, RLS bypass, uploads, public fundraiser pages, and administrative functions.

MIGRATION AND CUTOVER

Provide tested runbooks and scripts for:

1. Exporting the existing PostgreSQL database and R2 files.
2. Restoring schema/data into Azure PostgreSQL without duplicating migration-trigger effects.
3. Copying files to Blob Storage with count/hash verification.
4. Removing preview-only seeded accounts and allowlist entries.
5. Preserving all existing users, roles, profiles, registrations, events, fundraisers, approvals, messages, vendor records, and settings.
6. Transitioning current users to email OTP without duplicate accounts.
7. Staging rehearsal and validation.
8. Production write freeze.
9. Final migration.
10. DNS cutover for www.ridewithhuntington.com.
11. Smoke testing.
12. Rollback without split-brain writes.

DEFINITION OF DONE

Do not call the project complete until all of the following are true:

- The current Ride With Huntington appearance and behavior are preserved.
- All required routes and workflows function against Azure-hosted services.
- Email OTP login works from request through revocable session creation.
- All production communications use the pelotonia@huntington.com From address through an approved Microsoft 365 transport.
- No mailbox password or legacy basic authentication is used.
- PostgreSQL RLS and all existing security tests still pass.
- Blob upload/download/delete works.
- Pelotonia synchronization runs and reports freshness.
- Prototype-only admin content is persisted or explicitly blocked by documented business decision.
- The full unit/database/build suite passes.
- Infrastructure is reproducible from code.
- Staging deployment and migration rehearsal succeed.
- Monitoring, alerts, backup, restore, rollback, and support runbooks exist.
- No secrets or production data are committed.
- A final implementation report lists changed files, actual test/build results, remaining administrator-only steps, unresolved risks, and exact deployment commands.

HUMAN/ADMINISTRATOR INPUTS YOU MUST REQUEST RATHER THAN GUESS

- Azure subscription and target region.
- Resource naming/tagging policy.
- DNS control for ridewithhuntington.com.
- Huntington tenant ID.
- Confirmation that pelotonia@huntington.com exists as a licensed or shared mailbox and may send application email.
- Microsoft Graph Mail.Send approval and Exchange restriction to that mailbox, or approved SMTP relay details.
- Approved test-recipient mailbox for EMAIL_REDIRECT_TO.
- Azure networking, firewall, private DNS, and WAF standards.
- Required availability, RPO, RTO, backup retention, and disaster-recovery policy.
- Production bootstrap administrators and external-email allowlist.
- Security approval for email OTP versus Entra MFA.
- Whether all routes require sign-in or public fundraiser/event pages remain public.

Start by inspecting the repository and writing a short implementation inventory that maps each requirement above to existing files, missing code, tests, infrastructure, and documentation. Then implement in small verified vertical slices. Do not stop at the inventory.
```

---

## Suggested use

1. Open the repository folder containing `package.json` in VS Code.
2. Make sure the coding agent has workspace file and terminal access.
3. Paste the complete master prompt.
4. Provide Azure/Huntington administrative values only through approved secret-management channels—not in the prompt or source files.
5. Require the agent to show real test and build output before accepting completion.

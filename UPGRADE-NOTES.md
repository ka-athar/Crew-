# Crew Ledger — Professional Workspace Upgrade

## Included
- Automatic server-side attendance creation on the first authenticated app load each day.
- Best-effort time-out on sign-out/app exit, heartbeat updates while active, and stale-session reconciliation on reconnect.
- One attendance record per user per workspace-local day.
- Four workspace-scoped chat spaces, each seeded with `#general` and `#announcements`.
- Workspace-scoped channel creation for leads, group creation, message sending, and server-side checks that prevent cross-workspace chat access.
- Workspace selector for the primary super-admin in chat, plus workspace-specific automatic assignment selection.
- More restrained login layout, improved app palette, responsive chat layout, and expanded Privacy Policy.
- Random temporary first-run admin credentials when no valid `ADMIN_PASSWORD` is configured; the generated credential is printed to server/function logs and must be changed on first sign-in.
- Privacy-policy acceptance timestamp and policy version recorded at successful sign-in.

## Verification
- `npm test`: 23 tests passed.
- JavaScript syntax checks passed for the client, API core, and local development server.

## Important attendance limitation
Browsers and mobile operating systems can terminate an app without delivering a final exit event. The app sends a best-effort exit request and keeps a server heartbeat; if that request cannot be delivered, the server reconciles the record on reconnect using the last heartbeat. In that exceptional case, time-out may be the last heartbeat time rather than the exact app-close time.

## Deployment
This ZIP contains source code; it does not itself publish changes to an existing live Netlify site. Deploy this folder through the configured GitHub/Netlify workflow after setting `ADMIN_USERNAME` and `ADMIN_PASSWORD` securely in Netlify environment variables. Existing stored user accounts are not automatically reset by this code upgrade.

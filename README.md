# Crew Ledger (Netlify edition)

Private task and deadline tracker for a team. Installable on a phone (PWA).
Netlify Functions run the API; Netlify Blobs store the data.


## Mandatory device verification (web / installable PWA)

- A valid password sign-in creates a **locked** server session. The app does not load workspace data until device verification succeeds.
- On each app launch/restart, return from the background, and after five minutes of inactivity, the interface locks and asks for local device verification. Protected API routes also reject locked sessions; this is not only a visual overlay.
- The browser uses WebAuthn with `userVerification: required`, which invokes the platform-supported authenticator. Depending on the device, that may be fingerprint, face recognition, or the device screen-lock PIN/password. The website never reads raw fingerprint/face data.
- The server stores a public-key credential and signature counter—not fingerprint images, face templates, or biometric samples. The authenticator handles biometric matching locally.
- Device verification is bound to the account identified by the existing HttpOnly, SameSite session cookie. At first setup, the account password is required before the device can be registered. Password login for an account that has no registered device opens a setup-only locked screen.
- Explicit sign-out revokes the current session. Hiding the app locks the server session, and a server-side unlock window expires after 15 minutes as a backstop if the browser is force-closed.
- Use HTTPS and a supported modern browser. The WebAuthn prompt may offer the phone's screen-lock method when biometric hardware is unavailable; the site cannot force a specific sensor or access the phone's raw biometric database.

## Deploy

Site is already created: id `5f36ef91-b8ea-43ba-b904-2eef82e52ca3` (crew-ledger-team).

### Option A: Netlify CLI
```
npm install
npm install -g netlify-cli
netlify login
netlify link --id 5f36ef91-b8ea-43ba-b904-2eef82e52ca3
netlify deploy --prod
```

### Option B: GitHub
Push this folder to a GitHub repo, then in Netlify choose
Add new project > Import from Git. Build settings are read from netlify.toml.

## First sign-in
Before the first deployment, set `ADMIN_USERNAME` and `ADMIN_PASSWORD` (at least 12 characters) in Netlify environment variables. If no password is configured, the app generates a random temporary admin password and writes it once to the function logs; retrieve it from the Netlify function logs, sign in as `admin` (or your configured username), and immediately choose a new password. Then add team members under People. Do not use a shared or publicly documented password in production.

## Install on phone
Open the site in Chrome (Android) or Safari (iPhone), then
Add to Home Screen / Install app.

## Notes
- Netlify may put "team login" protection on the site (visitors must be Netlify
  team members). Turn it off in Project configuration > Access & security >
  Visitor access if your team should reach the site.
- Data lives in Netlify Blobs. Fine for a small team; it is not a transactional
  database. Deploy previews share the same store.
- Local test: `npm run dev` (port 8888, in-memory store), `npm test`.

## Four-team upgrade (October 2026)

This version includes:
- Four workspaces with editable names. The original administrator is the super-admin; additional team-lead accounts are scoped to their assigned workspace.
- Team-specific member and task visibility, member-to-workspace assignment, and the ability to create team leaders from the Team tab.
- Seven task statuses: To do, In progress, Submitted, Under review, Needs changes, Waiting teacher's approval, and Completed.
- Weekly/monthly reports and per-person task tracking retained from the original app.
- Automatic daily attendance: successful sign-in immediately creates one server-timestamped entry for the day; sign-out, the app-exit event or the End work session control records time-out. A heartbeat supports recovery if the browser is force-closed, and the server reconciles stale records on reconnect. One attendance entry is kept per person per workspace-local day.
- Mobile file uploads up to 4 MB per file, stored with workspace data. The app currently keeps up to 80 uploaded files in its file ledger.
- Assisted assignment from pasted instructions or a .txt/.csv file. Format each line as `Member Name: task description — YYYY-MM-DD`. The helper matches names, usernames or emails and returns unmatched names for review. It is not an AI document parser for PDF/DOCX/images; review all assignments before relying on them.
- Workspace-isolated chat with default #general and #announcements channels per workspace, plus channel creation for leads, private groups with explicit member selection, and server-checked message access. Group messages are visible only to selected group members and the super-admin.
- Professional credential-based sign-in, forced password change for temporary credentials, login rate limiting, privacy consent before sign-in, and an expanded privacy policy.

## Deploy and important notes

Run `npm test` before deploying. Deploy with the Netlify CLI steps above or push this folder to a GitHub repository and import it into Netlify. This environment cannot upload the project to Google Drive or publish a Netlify deployment, so the ZIP deliverable is provided separately.

Uploads are stored as encoded data in Netlify Blobs documents rather than a dedicated object-storage bucket. This is suitable only for a small team and modest usage; for production at scale, migrate uploads to dedicated object storage and use a database with explicit retention, backup and deletion controls. Direct OAuth integration with Google Drive is not included. On mobile, a device file picker may expose Drive as a selectable source depending on the device and its apps.

The super-admin can create team-lead accounts by adding users in the People tab, choosing their workspace and setting Role to Team lead. Each workspace dashboard shows task completion, overdue work, attendance entries and active sessions. Team leads and members are server-scoped to their own workspace; the primary super-admin can oversee all four. Automatic assignment requires selecting a workspace for the primary admin and only matches active members of that workspace. Existing accounts are migrated so the earliest existing administrator retains super-admin access.

Attendance exit requests are best-effort because browsers and mobile operating systems may terminate a process without a final event. A server heartbeat and stale-session reconciliation reduce orphaned sessions, but for a force-closed app the recorded time-out may be the last heartbeat rather than the exact moment of exit.


## Latest workspace upgrade

- File uploads use a multipart form request from the browser and store file bytes separately from the workspace metadata. Files up to 4 MB per upload are supported.
- Attendance records estimate active-app time from one-minute heartbeats while the app tab is visible. Time-out is recorded on sign-out or page exit when the browser permits the request; force-closing a browser can only be reconciled from the last heartbeat.
- Optional biometric attendance uses WebAuthn/passkeys. Deploy over HTTPS and use a compatible browser/device. The operating system may verify a fingerprint, face, or device PIN; the app never receives raw biometric data. Each user must select **Set up fingerprint / device passkey** once before using biometric verification.
- Project task fields include start date, due date, priority, type, estimate, list/stage, sprint/cycle and tags.
- Install dependencies with `npm install` before local development or deployment. The `@simplewebauthn/server` package is required for passkey verification.


## v6 workspace security and notifications

- Device passkeys use WebAuthn on HTTPS and can use a phone's fingerprint, face unlock, or device PIN. The app never reads biometric images. Members first register a passkey from Attendance or More; after that, they can use it for sign-in and verified sign-out.
- Explicit sign-out requires password re-entry or a registered passkey. Browsers do not reliably allow a biometric prompt during force-close/page-exit events, so the app records a best-effort attendance exit and requires normal sign-in on the next visit if the session has ended.
- Team leads can edit, deactivate/reactivate, reset, or remove accounts in their own workspace; primary admins can manage all workspaces. Existing passwords are never visible to leads.
- Team leads can save a Google Drive folder URL per workspace. This opens the team folder; it is a link integration, not full Google Drive OAuth/API syncing. Full syncing needs a Google Cloud OAuth client, consent configuration and Drive API scopes.
- Email notifications are optional. In Netlify, set `SENDGRID_API_KEY` and `NOTIFICATION_FROM_EMAIL` under Environment Variables with Functions scope. Without these, in-app activity records still appear, but email is not sent.
- The SBBM AI floating assistant answers common app-use questions locally. It is a help guide, not a connected generative AI model and cannot inspect private documents.

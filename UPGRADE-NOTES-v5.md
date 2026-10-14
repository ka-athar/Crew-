# Crew Ledger v5 — File, Attendance and Project Management Upgrade

## Included
- Multipart upload from mobile/device file picker; binary file bytes are stored separately from workspace metadata. 4 MB per-file limit.
- Download route returns only files the signed-in account is permitted to access in its workspace.
- WebAuthn/passkey biometric verification for attendance. Device/browser may ask for fingerprint, face verification, or device PIN. Raw biometric data never reaches the app.
- Attendance records estimate active-app time from periodic heartbeats while the app is visible; session time and active time are shown separately.
- Task fields inspired by modern project management tools: start date, due date, priority (including urgent), task type, estimate, list/stage, sprint/cycle and tags.
- Privacy policy updated for passkeys, active time estimates and the expanded task metadata.

## Before deploying
1. Run `npm install` so Netlify installs `@simplewebauthn/server` and the existing dependencies.
2. Deploy on HTTPS. Passkeys require a secure context; browser/device support varies.
3. Test uploading a small text file, PDF and image from both desktop and mobile, then download each.
4. Sign in, open Attendance, select **Set up fingerprint / device passkey**, follow the operating-system prompt, and then test **Verify attendance with fingerprint / passkey**.
5. Confirm attendance duration is an estimate: active-app time uses visible-page heartbeats and cannot prove that a person was continuously working. A force-closed browser may not send its final time-out request.

## Notes
- File uploads are capped at 4 MB per file due to serverless request-size limits.
- Biometric/passkey support is implemented through WebAuthn and must be exercised on the actual deployed domain/device. This package has automated server tests for the upload pipeline and existing access rules; the operating-system biometric prompt cannot be fully simulated in the local test suite.

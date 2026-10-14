# Crew Ledger v6 — security, notifications, Drive links and SBBM AI

## Added
- WebAuthn passkey sign-in for accounts that have registered a device passkey.
- Explicit sign-out requires password re-entry or WebAuthn passkey verification.
- Team leads receive in-app activity notifications when a member signs in or uploads a file; team members receive in-app activity notices for new team files.
- Optional SendGrid email notifications for team leads on sign-in/file upload and for team members on file upload/task assignment.
- Per-workspace Google Drive folder URL configuration and open link.
- Team lead account removal endpoint with workspace scoping, in addition to edit, password reset, deactivate/reactivate.
- Responsive mobile chat improvements and fixed bottom-right SBBM AI in-app help button.
- SBBM AI provides a local FAQ-style guide for app features.

## Configure optional email
In Netlify, set `SENDGRID_API_KEY` and `NOTIFICATION_FROM_EMAIL` with Functions scope. If not set, email is skipped and app activity notifications remain.

## Important boundaries
- Passkeys require HTTPS, a compatible browser/device and `npm install` for `@simplewebauthn/server`. The device's OS handles fingerprint/face/PIN verification; the app never gets raw biometrics.
- Browsers do not permit reliable biometric prompts during force-quit/page-exit. Explicit sign-out is verified; exit events remain best-effort for attendance.
- Drive setup currently stores a team folder URL and opens it. It does not yet perform OAuth sign-in, upload files directly into Drive, or synchronize Drive contents. Those require Google Cloud OAuth credentials and Drive API configuration.
- SBBM AI is a built-in help guide, not a remote LLM integration; it does not inspect private files or execute account changes.
- Account removal is permanent for the account record. Consider deactivation first if task history must remain attributable to a live profile.

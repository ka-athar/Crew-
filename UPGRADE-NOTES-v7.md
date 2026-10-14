# Crew Ledger v7 — security and responsive polish

- Added team-lead notification attempts for both password-confirmed logout and verified passkey logout. Notifications are scoped to the member's own team and fail safely without blocking sign-out.
- Reduced the SBBM AI floating button and moved it to the lower-left, above the fixed navigation, with compact behavior on narrow screens.
- Centered the bottom navigation content and improved safe-area and narrow-device handling.
- Kept the WebAuthn/passkey approach: the browser/device handles fingerprint, face unlock, or device PIN. The app does not capture or store biometric images/templates; the server stores only passkey public-key credentials and counters.
- Existing 404 page retained as the custom not-found route.
- Validation: `npm test` passes (24 tests).

## Important deployment note
Passkeys require HTTPS (localhost is suitable for development), a compatible browser/platform authenticator, and the configured Netlify Blobs store. A site cannot force a device to provide a fingerprint specifically: the operating system chooses among available biometrics or device PIN according to its settings. Team-lead notifications depend on the app's configured notification delivery and each lead's saved contact details.

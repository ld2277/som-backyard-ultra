# SOM Backyard Ultra

Race-day app for the Yale SOM Backyard Ultra: race info, live leaderboard, per-loop pledging, award voting and profiles.
A static page (GitHub Pages) backed by Firebase Authentication and Cloud Firestore.

## One-time setup

### 1. Firebase project (about 10 minutes)
1. Go to https://console.firebase.google.com and **Add project** (name it `som-backyard-ultra`). Google Analytics can be off.
2. **Build → Authentication → Get started → Sign-in method → Email/Password → Enable**.
3. **Authentication → Settings → Authorized domains → Add domain**: your GitHub Pages host, e.g. `YOUR-USERNAME.github.io`.
4. **Build → Firestore Database → Create database → Production mode**, region `us-east1` or `nam5`.
5. **Firestore → Rules**: replace everything with the contents of `firestore.rules` and **Publish**.
6. **Project settings (gear) → General → Your apps → Web (</>)**: register an app (no hosting needed), then copy the `firebaseConfig` object into `firebase-config.js` in this repo.
7. Recommended: **Upgrade to the Blaze (pay as you go) plan** and set a budget alert of $5. The free plan allows 50,000 document reads per day, which 200 people opening the app on race day could exceed, and the app would stop until midnight. Expected real cost for the whole event is well under $1.

### 2. GitHub Pages
1. Create a repository (public is fine; nothing secret lives here) and push these files to the `main` branch.
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: main / (root) → Save**.
3. The app is live at `https://YOUR-USERNAME.github.io/REPO-NAME/` within a minute or two. Share that link.

## How access works
- Anyone with the link can open the page, but every action requires an account with an **@yale.edu** address created with the event code, checked by the Firestore rules server-side. Addresses are not verified by email, because Yale's mail filter quarantines Firebase's verification messages.
- Creating an account requires the **event code**. The rules hold only a SHA-256 hash of it. To change the code, compute the new hash (`printf 'NEWCODE' | shasum -a 256` on a Mac) and replace it in both `firestore.rules` (republish) and `index.html` (`CODE_HASH`).
- **Organizers** are the owner (in `firestore.rules`) plus anyone added from the Profile page. Organizers record yards, set the start time, manage awards and see all accounts.
- Forgotten passwords: the reset email from the log-in screen may be quarantined by Yale. The reliable fix is Firebase console → Authentication → Users → find the person → delete user (or "Reset password"), then they sign up again.

## Data
All data lives in Firestore under the project: `accounts`, `results`, `photos`, `pledges`, `awards/{costume|sign}/entries`, `awards/{costume|sign}/votes`, `config/race`, `config/admins`. Export any time from the Firebase console.

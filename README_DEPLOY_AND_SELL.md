# The Peptide Journal — Complete Netlify project

This is the complete Git-connected Netlify project. It includes the journal app, the server-side buyer-code gate, Netlify Functions, seller code desk, PWA assets, and deployment instructions.

## Important: do not use Netlify Drop for this project

The screenshot error happened because the project was being deployed through Netlify's **Dropped files / Netlify Drop** flow. That flow is for static files and does not build and publish the Netlify Functions this project needs for buyer-code activation and access protection. Deploying only the `public` folder would also omit the protected app and license functions.

**Use Git-connected deployment or the Netlify CLI instead.** The ZIP is structured with the project files at its root so it can be uploaded to a GitHub repository as a complete project.

## What's included

- `netlify.toml` — Netlify build, publish, function, and route settings.
- `netlify/functions/app.mjs` — server-side buyer-code gate and protected app delivery.
- `netlify/functions/license.mjs` — code issuance, activation, and revocation endpoints.
- `netlify/app-template.mjs` — protected app HTML served after license verification.
- `private/app.html` — readable source copy of the app for editing; not published as a public static file.
- `public/admin.html` — seller code desk; every action requires the private admin secret.
- `public/` PWA manifest, service worker, and app icons.
- `package.json` — Netlify Blobs dependency and build script.

## Included app tabs

1. Overview
2. Peptide Log
3. Steroid Tracker
4. Testosterone
5. Symptoms
6. Progress Photos
7. Meals & Food
8. Workouts & Recipes
9. Notes & List
10. Cheat Sheet
11. Reference Sheet
12. My Reference Notes

Steroid and testosterone entries and lab records are saved locally in the buyer's browser and included in JSON backup/restore. The trackers are for recordkeeping only; they do not calculate doses or recommend cycles or treatment changes. The reference pages label source claims as unverified and do not reproduce dosing, mixing, or preparation protocols.

## Deploy to your existing Netlify site using GitHub

1. Download and extract this ZIP on a computer.
2. Create a **private** GitHub repository (for example, `the-peptide-journal`).
3. Upload the *contents* of the extracted project folder to the repository's top level. The top level must contain `netlify.toml`, `package.json`, `netlify/`, `private/`, and `public/`.
4. In Netlify, open your existing site and choose **Project configuration → Build & deploy → Continuous deployment** (or **Add new project → Import an existing project** if reconnecting it).
5. Connect the private GitHub repository.
6. Confirm these build settings (they are also in `netlify.toml`):
   - Build command: `npm install && npm run build`
   - Publish directory: `public`
   - Functions directory: `netlify/functions`
7. Set the Node version to 22 if Netlify asks. The included `.nvmrc` specifies 22.
8. Add these two environment variables in **Project configuration → Environment variables**. Set them for the production deploy and ensure Functions can read them:
   - `ADMIN_SECRET` — a long, random private value used only by the seller code desk.
   - `LICENSE_SIGNING_SECRET` — a different long, random private value used to sign buyer license cookies.

   Generate two separate values on a computer with Node.js using this command twice (do not reuse the same value):
   ```sh
   node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))"
   ```
   Paste one generated value into each Netlify environment variable. Do not put either secret in GitHub, this ZIP, the app HTML, or Etsy downloads.
9. Trigger a fresh production deploy and wait for it to finish.
10. Open the public site in a private/incognito browser. It should show the buyer access-code screen. Do not test with an issued code until the license service has deployed and the two environment variables are set.

## Alternative: deploy with Netlify CLI

On a computer with Node.js and the Netlify CLI installed:

```sh
npm install
npm run build
npx netlify login
npx netlify link
npx netlify deploy --build --prod
```

Set the same two environment variables in the linked Netlify site's settings before the production deploy. This project requires Netlify Functions and Netlify Blobs; a static drag-and-drop deploy is not a supported substitute.

## Issue a buyer code after an Etsy sale

1. Open `https://YOUR-SITE.netlify.app/admin.html` after deployment.
2. Enter your `ADMIN_SECRET` in the seller code desk.
3. Generate one code and copy it immediately.
4. Deliver that individual code manually through the Etsy order message/digital-delivery workflow.
5. Keep a private record of which Etsy order received each code. The service stores a hash of the code, not the readable code.
6. Use the revoke tool if a code needs to be deactivated.

The admin page itself can be visited publicly, but code issuance and revocation require the server-side admin secret. Never share the admin secret with buyers.

## Buyer experience and privacy

- A buyer enters their unique code once. The license is activated for that browser/device and a secure, HttpOnly cookie is set.
- Journal entries, notes, photos, symptoms, medication records, and lab records stay in that buyer's browser local storage. They are not sent to the license service.
- Buyers should export JSON backups regularly. Restore the backup on a replacement device/browser if needed.
- A code is single-device/single-browser activation. Contact the shop for help if a buyer changes devices or clears site data.
- Revocation blocks online access on the next load, but cannot erase an already cached offline copy. Browser storage is not encrypted; buyers should protect their device and backups.

## Pre-sale test checklist

- [ ] Netlify production deploy succeeds and Functions appear in the deploy.
- [ ] Fresh browser at the site root displays the buyer access-code gate.
- [ ] Invalid code is rejected.
- [ ] A valid code unlocks the journal.
- [ ] The same code works again on the same browser/device.
- [ ] The same code is rejected on a second browser/device.
- [ ] A second code works independently.
- [ ] Revoking a code blocks online access on next load.
- [ ] Steroid Tracker, Testosterone, Cheat Sheet, and Reference Sheet tabs open.
- [ ] Add records, export a JSON backup, then import it and confirm records return.
- [ ] Test on iPhone Safari and Android Chrome before listing the product.

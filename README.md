# Medicine Reference

A single-source application delivered as:
- GitHub Pages progressive web app (PWA)
- Android APK built from the same `www/` application using Capacitor

## Database
The complete source dataset is bundled at `www/data/medicines.json.gz`. The app decompresses it on first launch and saves the complete records in IndexedDB for subsequent offline use. All 26,205 source rows are retained, including multiple brand rows belonging to the same generic medicine.

The dataset's source field identifies MedEx Bangladesh. Content is displayed as supplied; this application does not independently validate clinical accuracy, current registration status, local availability, or Nigerian-approved prescribing information.

## Run locally
Requires Node.js 20+.
```bash
npm install
npm run android:add
npm run android:sync
```
Open `android/` in Android Studio to build/install, or run:
```bash
cd android
./gradlew assembleDebug
```

## GitHub Pages
1. Push this repository to GitHub.
2. In repository Settings → Pages, select **GitHub Actions** as the build/deployment source.
3. The included workflow publishes the `www/` folder.
4. Your app URL will be `https://YOUR-USERNAME.github.io/YOUR-REPOSITORY/`.

## APK build through GitHub Actions
The workflow `Build Android APK` creates the Android project, syncs the shared web app, builds a debug APK and uploads it as a workflow artifact. Download the APK from the completed workflow run under **Artifacts** and install it on your Android phone (allow installation from that source when Android prompts).

## Updating the database
Replace `www/data/medicines.json.gz` with a gzip-compressed JSON array using the same 20 field names. Increment the service worker cache version in `www/sw.js` when publishing an update.

## Important
This is a reference/search application, not a prescribing or diagnostic tool. Always verify doses, interactions, contraindications, pregnancy information and other clinical decisions against current authoritative references and local regulatory requirements.


## Android database troubleshooting
The APK workflow verifies that `www/data/medicines.json.gz` is copied into the Capacitor Android public assets before compiling. The app reports the exact attempted asset URL and HTTP status if the file cannot be loaded.

## Latest update
This complete package includes Android database request diagnostics and navigation controls on medicine detail screens: Back to results, Home, and Clear search. The service-worker cache version is incremented so clients fetch the updated shell.

## Gzip decompression compatibility
The workflows install the `pako` runtime dependency and copy its browser build to `www/vendor/pako_inflate.min.js` before deployment or Capacitor synchronization. The application uses this bundled decompressor instead of relying on the browser's `DecompressionStream`, improving compatibility with Android WebView. The Pako script is loaded before `app.js` and precached by the service worker.

## Android asset packaging fix
The Android workflow explicitly copies `www/data/medicines.json.gz` and the bundled Pako browser script into `android/app/src/main/assets/public/` after Capacitor sync, then verifies the database byte-for-byte and validates the gzip stream before compiling. This prevents an APK from being produced without the database asset.

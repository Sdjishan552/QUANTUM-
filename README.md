# Quant Vault

A private, light-theme, local-first question bank for quantitative aptitude practice. By default, chapters, questions, and source images stay in IndexedDB in the browser you use. Optional Firebase sign-in syncs your saved question bank between devices.

## Open in VS Code

1. Unzip the folder and open `quant-vault` in VS Code.
2. In VS Code, install the **Live Server** extension (Ritwick Dey), then right-click `index.html` and choose **Open with Live Server**.
3. The app opens in your browser. Create a chapter first, then add questions.

The app needs to run from a local web server rather than opening `index.html` directly. This enables IndexedDB and the service worker. You can also serve the folder with any static file server.

## Publish for your own access with GitHub Pages

1. Create a new, empty GitHub repository and extract this project into its root so `index.html` is at the root.
2. In VS Code, open **Source Control**, initialise a repository, commit the files, and publish/push the repository to GitHub.
3. On GitHub, open **Settings → Pages**. Under **Build and deployment**, select **Deploy from a branch**, choose `main` and `/(root)`, then save.
4. GitHub will show the published address in Pages settings after deployment. Open it in your browser and optionally install it from the browser menu.

GitHub Pages hosts the app files only. Your questions are never uploaded to GitHub. They remain in your browser unless you connect Firebase below. Use **Backup & Settings → Download backup** as an additional safeguard.

## Included features

- User-created chapters and topic lists; rename, reorder, and delete chapters.
- Responsive phone-first navigation with a persistent bottom tab bar and an expandable sections drawer; desktop keeps its full sidebar workspace.
- Independent questions with permanent generated IDs, source, remarks, optional topic, star status, and optional original image.
- Search and filters, card/list switch, bulk selection, reassignment, deletion, and selected-question export.
- IndexedDB storage, JSON backup/restore, and offline app shell.
- Optional account-based Firebase Authentication and Firestore sync, with per-account database rules and timestamp-based merging across devices.
- Camera/image upload with editable OCR draft preview.
- Compact, black-and-white PDF output in chapter-organized study-book or shuffled mixed exam-paper style. The print layout omits app controls, uses space-efficient headings, and provides configurable paper, typography, spacing, and book binding margins.

## Connect your Firebase project

The app ships with Firebase connection code, an empty web-config template, and security rules. You create and own the Firebase project; this zip does not contain project credentials.

1. In the [Firebase Console](https://console.firebase.google.com/), create a project and register a **Web app**. Copy its Firebase web configuration into `firebase-config.js`, replacing each `PASTE_...` value.
2. In **Authentication → Sign-in method**, enable **Email/Password**.
3. Create a **Cloud Firestore** database. Open its **Rules** tab, paste the contents of `firestore.rules`, and publish the rules.
4. In **Authentication → Settings → Authorized domains**, add the domain where you will use the app, including your `username.github.io` GitHub Pages host. Keep `localhost` for VS Code testing.
5. Commit `firebase-config.js` with the other app files. Its web config identifies the project but is not an admin credential. **Never put a service-account JSON/private key in this app or GitHub.** The security rules restrict reads and writes to the signed-in user's own UID.
6. Refresh the app. Open **Backup & Settings → Firebase connection**, create an account, or sign in on each device with the same account. The first sync merges the local and cloud collections; later edits sync while online. Deletions and edits also sync.

### What syncs, and what stays local

- **Cloud by default after sign-in:** chapter and question records, IDs, stars, topics, remarks, settings, and deletion markers. Each signed-in account has its own private Firestore path.
- **Local by default:** uploaded question images, OCR draft text, and temporary previews. This keeps the app useful without a storage bucket or added billing setup. JSON backup still includes the retained original images.
- **Optional image cloud sync:** turn on **Sync original images too** in the Firebase panel. This uploads source images to Firebase Storage, limited to 12 MB each by the included `storage.rules`. You must create the Storage bucket, publish those rules, and add `storageBucket` in the web config. Firebase currently requires the **Blaze pay-as-you-go billing plan** for Cloud Storage, even though no-cost usage may be available; attach a billing account only if you choose to enable image sync. Review current [Firebase Storage setup requirements](https://firebase.google.com/docs/storage/web/start) and [Firebase pricing](https://firebase.google.com/pricing) first.

The app does not store your Firebase password. Firebase Authentication keeps the sign-in session in that browser. Local saves continue if you are offline; cloud sync resumes when the connection returns. When the same record was edited on two devices, the most recent edit timestamp wins. Keep periodic JSON backups as an independent recovery option.

## Edit/delete verification code and exam countdown

On the first attempt to edit, move, or delete a saved question, Quant Vault asks you to create and confirm a six-digit code. The app stores only a salted PBKDF2 hash, and the hash syncs with Firebase settings so your code is the same on each signed-in device. After five incorrect entries, verification pauses for 30 seconds. You can change the code after verifying the current one, or reset a forgotten code from **Backup & Settings**; reset keeps your questions, and the next protected action prompts you to create a new code. This is an extra confirmation barrier for the app, not encryption or account-grade security.

The top bar counts down to **July 1, 2027**. It uses the device's local calendar date and updates daily.

## OCR and mathematics notes

OCR is optional and manual: it loads Tesseract.js and English text data from public file servers when you select **Try text extraction**, so internet access is required for the initial download and OCR will not run offline. If one script file server is unavailable, the app tries a second. OCR cleanup removes stray vertical bars and trailing page labels, joins wrapped lines, and formats common fractions, square roots, and superscript powers with LaTeX. Tesseract is general printed-text OCR, **not a mathematical OCR engine**, so no automatic step can guarantee perfect math or wording. Inspect/edit every extraction before saving. No API key or paid external OCR account is required. A specialist math OCR service requires a separate account, internet, and service-specific credentials and may incur charges.

Question text remains editable plain text. KaTeX renders LaTeX enclosed in `\\( ... \\)`, `\\[ ... \\]`, or `$$ ... $$` while online; the raw notation remains stored and editable. The KaTeX library is loaded from jsDelivr, so formatted math needs a network connection unless your browser has cached the library. Offline, the question bank still works and the notation remains visible as text.

## PDF and binding

Choose **PDF & Book Builder**, select questions, adjust settings, then use **Preview & print PDF → Print / Save as PDF**. In the browser print dialog, choose **Save as PDF**. The print stylesheet uses CSS paged-media `:left` / `:right` rules: odd/right-hand pages receive the larger inside margin on the left; even/left-hand pages receive it on the right. Browser print engines vary; check the generated PDF before a large print run. The first page is the cover and uses the odd-page margin rule.

## Data and browser compatibility

- Data is per browser and per device. Clearing site data removes the local collection.
- The offline shell and question management work after the site has been loaded once over HTTPS (GitHub Pages) or localhost. OCR and CDN-hosted KaTeX need internet.
- PDF creation uses the browser print dialog, which can save to PDF without a PDF service.
- No analytics, sign-in, cloud sync, or remote question storage are included.

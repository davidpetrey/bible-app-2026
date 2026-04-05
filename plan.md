## Plan: Bible Web App with Accounts and MongoDB Realm

Build a full-stack Bible web app using MongoDB Atlas + Realm serverless backend and vanilla JavaScript, with user registration/login and persistent saved favorites. No Node.js server required.

**Steps**

*Phase 1: Set up Atlas and Realm*
1. Create a MongoDB Atlas cluster and a Realm app linked to it.
2. Configure Realm authentication: email/password provider enabled.
3. Create a seed data endpoint or trigger in Realm to ingest a public domain Bible dataset into the `verses` collection (executed once).

*Phase 2: Design collections and security*
4. Create MongoDB collections:
   - `verses` for the Bible text (book, chapter, verse, text).
   - `favorites` for user-saved verse ranges (userId, bookName, chapterStart, verseStart, chapterEnd, verseEnd).
   - Realm's built-in `users` collection handles email/password accounts.
5. Configure Realm Rules (Row-Level Security) on `verses` (public read) and `favorites` (owner-only read/write).

*Phase 3: Building Realm backend logic*
6. Create Realm Functions for:
   - Search/query Bible text (book, chapter, verse lookup).
   - List favorites for the logged-in user.
   - Create/delete a favorite entry.
7. Optionally expose these as HTTPS endpoints via Realm HTTP Service.

*Phase 4: Build frontend*
8. Create `public/` folder with vanilla JS frontend:
   - `index.html` — login/register forms, Bible browsing, search, favorites UI.
   - `styles.css` — layout and responsive design.
   - `app.js` — state management, DOM updates, Realm SDK calls, auth handling.
9. Install Realm Web SDK via npm or script tag in `index.html`.
10. Wire frontend to Realm functions and auth using the Realm Web SDK (Realm.User.logInWithCredentials, callFunction, etc.).

*Phase 5: Deploy and test*
11. Deploy frontend to a static host (Atlas App Services can serve static files, or use Firebase, Vercel, etc.).
12. Test user registration, login, Bible search, favorite creation/deletion, and persistence.

**Relevant files**
- `public/index.html` — login, Bible UI, favorites.
- `public/styles.css` — layout.
- `public/app.js` — Realm SDK integration, state, DOM updates.
- Realm dashboard: Functions, Rules, and Collections configuration (console-based, not local files).
- Optional seed script (local Node.js script or Realm Function to load Bible data once).

**Verification**
1. Set up Realm app and create `verses` collection with sample Bible data.
2. Configure Realm Rules to allow public read on `verses` and owner-only on `favorites`.
3. Open frontend, register a user, and confirm auth works.
4. Search or browse verses and confirm they load from MongoDB.
5. Save a favorite, reload, and confirm it persists for that user.

**Decisions**
- Use Realm built-in auth (email/password) instead of custom JWT logic.
- Store Bible text in MongoDB, use Realm Functions to query it.
- Deploy frontend separately from Realm (or use Realm's static file hosting).
- No local Node.js server—all backend logic is serverless via Realm.

**Further Considerations**
1. Seed data approach: decide between a one-time script or a Realm trigger to load Bible verses.
2. Frontend deployment: use Atlas App Services static hosting, Vercel, Netlify, or GitHub Pages.
3. Performance: consider indexing the `verses` collection on (book, chapter, verse) for fast lookups.
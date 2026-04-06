## Plan: Bible Web App 2026 with MongoDB Atlas Data API and Triggers

Build a full-stack Bible web app using MongoDB Atlas Data API for direct database access and Atlas Triggers for event-driven serverless logic, with third-party authentication (e.g., Auth0) and vanilla JavaScript. No Node.js server required.

**Steps**

_Phase 1: Set up Atlas and Data API_

1. Create a MongoDB Atlas cluster.
2. Enable the Atlas Data API on the cluster (via Atlas UI > Data API).
3. Set up a third-party authentication provider (e.g., Auth0) for user registration/login, and configure it to store user IDs in MongoDB.
4. Create a one-time seed script or Atlas Trigger to ingest a public domain Bible dataset into the `verses` collection.

_Phase 2: Design collections and security_

5. Create MongoDB collections:
   - `verses` for the Bible text (book, chapter, verse, text).
   - `favorites` for user-saved verse ranges (userId, bookName, chapterStart, verseStart, chapterEnd, verseEnd).
   - `users` for storing user data from the auth provider.
6. Configure Data API access: Use API keys for read/write permissions, with role-based access (public read on `verses`, user-specific on `favorites` via queries).

_Phase 3: Building backend logic with Atlas Triggers_

7. Create Atlas Triggers for event-driven tasks:
   - Scheduled Trigger: Run once to seed Bible data.
   - Database Trigger on `favorites`: Handle deletions or notifications on changes.
   - (Optional) Authentication Trigger: Sync user data on login.
8. For interactive queries (search, list favorites), use Data API directly from frontend.

_Phase 4: Build frontend_

9. Create `public/` folder with vanilla JS frontend:
   - `index.html` — login/register forms, Bible browsing, search, favorites UI.
   - `styles.css` — layout and responsive design.
   - `app.js` — state management, DOM updates, Data API calls, auth integration.
10. Integrate auth provider SDK (e.g., Auth0 SPA SDK) via npm or script tag.
11. Wire frontend to Data API using fetch() for queries/inserts, and auth provider for login.

_Phase 5: Deploy and test_

12. Deploy frontend to a static host (Vercel, Netlify, GitHub Pages, etc.).
13. Test user registration, login, Bible search, favorite creation/deletion, and persistence.

**Relevant files**

- `public/index.html` — login, Bible UI, favorites.
- `public/styles.css` — layout.
- `public/app.js` — Data API integration, state, DOM updates.
- Atlas UI: Data API configuration, Triggers setup (console-based, not local files).
- Optional seed script (local Node.js script to load Bible data once).

**Verification**

1. Enable Data API and create `verses` collection with sample Bible data.
2. Set up auth provider and confirm user data syncs to MongoDB.
3. Open frontend, register a user, and confirm auth works.
4. Search or browse verses and confirm they load via Data API.
5. Save a favorite, reload, and confirm it persists for that user.

**Decisions**

- Use third-party auth (e.g., Auth0) instead of deprecated Atlas auth.
- Store Bible text in MongoDB, query via Data API.
- Use Atlas Triggers for reactive/background tasks; Data API for direct frontend access.
- Deploy frontend separately (no Atlas hosting).

**Further Considerations**

1. Seed data approach: one-time local script or scheduled Atlas Trigger.
2. Auth provider choice: Auth0, Firebase, or AWS Cognito.
3. Performance: index `verses` on (book, chapter, verse); use Data API filters for security.
4. Security: Store API keys securely; validate user IDs in queries.

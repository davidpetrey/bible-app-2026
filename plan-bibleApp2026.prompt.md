## Plan: Bible Web App 2026 with Cloudflare Workers and MongoDB Atlas

Build a full-stack Bible web app using Cloudflare Workers as the serverless API layer for database interactions with MongoDB Atlas, Atlas Triggers for event-driven logic, third-party authentication (e.g., Auth0), and vanilla JavaScript frontend.

**Steps**

_Phase 1: Set up Atlas, Cloudflare, and auth_

1. Create a MongoDB Atlas cluster.
2. Set up a Cloudflare account and install Wrangler CLI for deploying Workers.
3. Set up a third-party authentication provider (e.g., Auth0) for user registration/login, and configure it to store user IDs in MongoDB.
4. Create a one-time seed script or Atlas Trigger to ingest a public domain Bible dataset into the `verses` collection.

_Phase 2: Design collections and security_

5. Create MongoDB collections:
   - `verses` for the Bible text (book, chapter, verse, text).
   - `favorites` for user-saved verse ranges (userId, bookName, chapterStart, verseStart, chapterEnd, verseEnd).
   - `users` for storing user data from the auth provider.
6. Configure MongoDB connection: Use Atlas connection string in Workers, with role-based access (public read on `verses`, user-specific on `favorites` via queries).

_Phase 3: Building backend logic with Workers and Triggers_

7. Create Cloudflare Workers for API endpoints:
   - Worker for querying verses (search, browse).
   - Worker for managing favorites (add, delete, list).
   - Worker for user auth validation.
     Ensure Workers are configured with `nodejs_compat` compatibility flag and compatibility date 2024-09-23 or later to support MongoDB driver.
8. Create Atlas Triggers for event-driven tasks:
   - Scheduled Trigger: Run once to seed Bible data.
   - Database Trigger on `favorites`: Handle deletions or notifications on changes.
   - (Optional) Authentication Trigger: Sync user data on login.

_Phase 4: Build frontend_

9. Create `public/` folder with vanilla JS frontend:
   - `index.html` — login/register forms, Bible browsing, search, favorites UI.
   - `styles.css` — layout and responsive design.
   - `app.js` — state management, DOM updates, Worker API calls, auth integration.
10. Integrate auth provider SDK (e.g., Auth0 SPA SDK) via npm or script tag.
11. Wire frontend to Workers using fetch() for queries/inserts, and auth provider for login.

_Phase 5: Deploy and test_

12. Deploy Workers using Wrangler.
13. Deploy frontend to a static host (Vercel, Netlify, GitHub Pages, etc.).
14. Test user registration, login, Bible search, favorite creation/deletion, and persistence.

**Relevant files**

- `public/index.html` — login, Bible UI, favorites.
- `public/styles.css` — layout.
- `public/app.js` — Worker API integration, state, DOM updates.
- `workers/` folder: Worker scripts (e.g., `verses.js`, `favorites.js`).
- `wrangler.toml` — Cloudflare Workers configuration.
- Atlas UI: Triggers setup (console-based, not local files).
- Optional seed script (local Node.js script to load Bible data once).

**Verification**

1. Deploy Workers and create `verses` collection with sample Bible data.
2. Set up auth provider and confirm user data syncs to MongoDB.
3. Open frontend, register a user, and confirm auth works.
4. Search or browse verses and confirm they load via Workers.
5. Save a favorite, reload, and confirm it persists for that user.

**Decisions**

- Use Cloudflare Workers as the API layer instead of deprecated Atlas Data API.
- Store Bible text in MongoDB, query via Workers using MongoDB driver.
- Use Atlas Triggers for reactive/background tasks; Workers for interactive API calls.
- Deploy frontend separately; Workers via Cloudflare.

**Further Considerations**

1. Seed data approach: one-time local script or scheduled Atlas Trigger.
2. Auth provider choice: Auth0, Firebase, or AWS Cognito.
3. Performance: index `verses` on (book, chapter, verse); use Workers for efficient queries.
4. Security: Store MongoDB connection securely in Workers; validate user IDs in Workers.

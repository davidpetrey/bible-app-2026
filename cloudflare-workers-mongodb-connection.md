## Configure MongoDB connection in Cloudflare Workers

Use MongoDB Atlas from Cloudflare Workers securely, with a Worker-bound Atlas user and role-specific access.

### 1. Atlas user / role setup

Create a dedicated Atlas database user for Workers:

- Database: your app DB (for example `bible-app`)
- Roles:
  - `read` on collection `verses`
  - `readWrite` on collection `favorites`
  - optionally `readWrite` on collection `users` if you sync auth profile data

This gives the Worker only the permissions it needs.

### 2. Store the connection string as a secret

In `wrangler.toml`, do not hardcode the URI. Use a Wrangler secret instead:

```toml
name = "bible-api"
compatibility_date = "2026-04-07"
compatibility_flags = ["nodejs_compat"]
```

Then run:

```bash
wrangler secret put MONGODB_URI
```

The secret value should be your Atlas connection string, for example:

```text
mongodb+srv://workerUser:<PASSWORD>@cluster0.example.mongodb.net/bible-app?retryWrites=true&w=majority
```

### 3. Worker code pattern

Use the MongoDB Node driver inside the Worker:

```js
import { MongoClient } from "mongodb";

const uri = MONGODB_URI; // bound by Wrangler secret
let client;
let db;

async function getDb() {
  if (!db) {
    client = new MongoClient(uri, { tls: true });
    await client.connect();
    db = client.db("bible-app");
  }
  return db;
}
```

### 4. Enforce role-based access in code

#### Public read on `verses`

Expose only read/query routes in the Worker:

```js
async function handleVerses(request) {
  const db = await getDb();
  const verses = db.collection("verses");

  const query = {
    /* build from search/book/chapter params */
  };
  const results = await verses.find(query).limit(50).toArray();

  return new Response(JSON.stringify(results), {
    headers: { "Content-Type": "application/json" },
  });
}
```

This keeps `verses` readable by the public endpoint without exposing admin privileges.

#### User-specific access on `favorites`

Always attach authenticated `userId` to queries and inserts:

```js
async function listFavorites(userId) {
  const db = await getDb();
  return db.collection("favorites").find({ userId }).toArray();
}

async function addFavorite(userId, favoriteData) {
  const db = await getDb();
  return db.collection("favorites").insertOne({ ...favoriteData, userId });
}
```

And for deletes/updates:

```js
await db.collection("favorites").deleteOne({
  _id: new ObjectId(favoriteId),
  userId,
});
```

This prevents any request from modifying favorites without the authenticated `userId` filter.

### 5. Authentication integration

The Worker should verify the auth provider token and resolve `userId` from it before using favorites routes.

- verify JWT from Auth0/Firebase/AWS Cognito
- extract `sub` or `user_id`
- use that value in all favorites access

### 6. Important caveat

Cloudflare Workers require `nodejs_compat` for the MongoDB driver and a recent compatibility date.

If the MongoDB driver does not work in your Worker runtime, use a small HTTP proxy service or a secure HTTP gateway, but the Worker-based approach is still the right architecture.

### Summary

- Use a dedicated Worker Atlas DB user
- Store `mongodb+srv://...` in `MONGODB_URI` secret
- Set `compatibility_flags = ["nodejs_compat"]`
- Query `verses` through a public read route
- Query `favorites` only with authenticated `userId` filters
- Never expose raw connection credentials in the frontend

## Document structure for MongoDB collections

### 1. `verses`

Use this collection for Bible text lookup.

Fields:

- `_id`: ObjectId
- `book`: string
- `chapter`: number
- `verse`: number
- `text`: string
- `version` (optional): string
- `tags` (optional): array of strings

Example:

```json
{
  "_id": { "$oid": "650f5f7e1a2b3c4d5e6f7890" },
  "book": "John",
  "chapter": 3,
  "verse": 16,
  "text": "For God so loved the world that he gave his one and only Son...",
  "version": "ESV",
  "tags": ["gospel", "faith"]
}
```

Recommended indexes:

- `{ book: 1, chapter: 1, verse: 1 }`
- `{ book: 1, chapter: 1 }`
- optionally text index on `text` if you need search

---

### 2. `favorites`

Store each user’s saved verse ranges or verse bookmarks.

Fields:

- `_id`: ObjectId
- `userId`: string
- `book`: string
- `chapterStart`: number
- `verseStart`: number
- `chapterEnd`: number
- `verseEnd`: number
- `label` (optional): string
- `notes` (optional): string
- `createdAt`: ISODate
- `updatedAt`: ISODate

Example:

```json
{
  "_id": { "$oid": "650f60a81a2b3c4d5e6f7891" },
  "userId": "auth0|123456789",
  "book": "Psalms",
  "chapterStart": 23,
  "verseStart": 1,
  "chapterEnd": 23,
  "verseEnd": 6,
  "label": "Comfort passage",
  "notes": "Read when feeling anxious.",
  "createdAt": { "$date": "2026-04-07T12:00:00Z" },
  "updatedAt": { "$date": "2026-04-07T12:00:00Z" }
}
```

Recommended indexes:

- `{ userId: 1 }`
- `{ userId: 1, book: 1, chapterStart: 1 }`

---

### 3. `users`

Keep user metadata and auth-linked IDs.

Fields:

- `_id`: ObjectId
- `userId`: string
- `email`: string
- `name`: string
- `provider`: string
- `createdAt`: ISODate
- `lastLoginAt`: ISODate
- `settings` (optional): object

Example:

```json
{
  "_id": { "$oid": "650f61501a2b3c4d5e6f7892" },
  "userId": "auth0|123456789",
  "email": "jane@example.com",
  "name": "Jane Doe",
  "provider": "Auth0",
  "createdAt": { "$date": "2026-04-07T12:05:00Z" },
  "lastLoginAt": { "$date": "2026-04-07T15:30:00Z" },
  "settings": {
    "preferredVersion": "ESV",
    "displayMode": "dark"
  }
}
```

Recommended indexes:

- `{ userId: 1 }`
- `{ email: 1 }` (if you query by email)

---

## Notes

- Use `userId` from your auth provider as the stable link between `users` and `favorites`.
- Keep `verses` public-read through Worker routes, but protect `favorites` access by filtering on `userId`.
- If you need range support in `favorites`, store both `chapterStart`/`verseStart` and `chapterEnd`/`verseEnd` as shown.

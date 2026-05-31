# Rags²Riches — Alternative Thrift

A fullstack version of the **Rags²Riches** thrift-store site: a polished single-page
frontend backed by a real **Express REST API** with a persistent JSON datastore.

> You know Goodwill. Rags 2 Riches has Good News.

The original was a single 1.9 MB HTML file with everything (CSS, JS, and 13 base64
images) inlined. This repo splits it into a maintainable frontend + a working backend
so forms actually submit, inventory is served from an API, and data persists.

---

## ✨ What it does

| Page | Backend feature |
|------|-----------------|
| **Shop** | Live inventory grid with category filters + search (`GET /api/products`) |
| **Donate** | Working "schedule a free pickup" form (`POST /api/donations/pickup`) |
| **Events** | Upcoming events served live (`GET /api/events`) |
| **Impact** | Live counters that grow as people join / book pickups (`GET /api/impact`) |
| **Membership** | Pick a tier and join the Collective (`POST /api/membership/join`) |
| **Stories** | Read approved stories + submit your own (`GET`/`POST /api/stories`) |
| **Newsletter** | The footer signup actually subscribes (`POST /api/newsletter`) |

All submissions are validated and saved to `server/db.json`.

---

## 🚀 Quick start

You need [Node.js](https://nodejs.org) **18+**.

```bash
git clone https://github.com/<your-username>/rags2riches.git
cd rags2riches
npm install
npm start
```

Then open **http://localhost:3000**.

The datastore (`server/db.json`) is created automatically from `server/seed.js` on
first run. To wipe it back to the seed data:

```bash
npm run reset-db
```

For auto-reload during development:

```bash
npm run dev
```

---

## 🗂 Project structure

```
rags2riches/
├── server/
│   ├── index.js      # Express app: REST API + serves the frontend
│   ├── db.js         # tiny JSON-file datastore (no native deps)
│   ├── seed.js       # initial products, events, tiers, stories, etc.
│   └── db.json       # generated on first run (git-ignored)
├── public/           # the frontend (served statically)
│   ├── index.html    # the single-page app shell
│   ├── css/styles.css
│   ├── js/
│   │   ├── spa.js    # original client-side router (tab navigation)
│   │   ├── fx.js     # original animation / FX layer
│   │   └── api.js    # NEW: connects the pages to the backend
│   └── images/       # the 13 photos (extracted from the original base64)
├── package.json
└── README.md
```

---

## 🔌 API reference

Base URL: `http://localhost:3000`. All responses use `{ "ok": true, "data": ... }`
on success or `{ "ok": false, "error": "..." }` on failure.

### Read
- `GET /api/categories` — shop categories
- `GET /api/products` — products. Query: `?category=<id>` and/or `?q=<search>`
- `GET /api/products/:id` — single product
- `GET /api/events` — upcoming events (sorted by date)
- `GET /api/impact` — impact stats (some figures reflect live submissions)
- `GET /api/membership/tiers` — the four membership tiers
- `GET /api/donations/info` — drop-off locations + accepted / not-accepted lists
- `GET /api/stories` — approved community stories
- `GET /api/health` — health check

### Write
- `POST /api/membership/join` — `{ name, email, tier }`
- `POST /api/newsletter` — `{ email }`
- `POST /api/donations/pickup` — `{ name, email, address, date, items[] }`
- `POST /api/stories` — `{ title, author?, body }` (queued for moderation)
- `POST /api/contact` — `{ name, email?, message }`

Example:

```bash
curl -X POST http://localhost:3000/api/membership/join \
  -H "Content-Type: application/json" \
  -d '{"name":"Ava Reyes","email":"ava@example.com","tier":"gold"}'
```

---

## 📦 Putting it on GitHub

```bash
git init
git add .
git commit -m "Rags2Riches fullstack site"
git branch -M main
git remote add origin https://github.com/<your-username>/rags2riches.git
git push -u origin main
```

`node_modules/` and `server/db.json` are already in `.gitignore`, so the repo stays
clean — anyone who clones it just runs `npm install && npm start`.

---

## ☁️ Deploying

Because it's a standard Node web service, it runs as-is on most platforms:

- **Render / Railway / Fly.io / Heroku** — set the start command to `npm start`.
  The server reads `process.env.PORT`, which these platforms set automatically.
- **Docker** — a minimal image:

  ```dockerfile
  FROM node:20-alpine
  WORKDIR /app
  COPY package*.json ./
  RUN npm install --omit=dev
  COPY . .
  EXPOSE 3000
  CMD ["npm", "start"]
  ```

> Note: the JSON datastore writes to a local file, which is fine for a single
> instance / demo. For production scale, swap `server/db.js` for a real database
> (SQLite, Postgres, etc.) — the rest of the code doesn't change.

---

## 📄 License

MIT — see [LICENSE](LICENSE).

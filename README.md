<div align="center">

# 🧸 Re.Brinque — API

**Toys deserve more than one childhood.**

A community marketplace where people **donate** and **swap** toys instead of throwing them away — with real-time chat, Google sign-in, and AI moderation that keeps the community safe.

[![Node.js](https://img.shields.io/badge/Node.js-339933?logo=node.js&logoColor=white)](#)
[![Express 5](https://img.shields.io/badge/Express-5-000000?logo=express&logoColor=white)](#)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)](#)
[![Socket.IO](https://img.shields.io/badge/Socket.IO-010101?logo=socket.io&logoColor=white)](#)
[![JWT](https://img.shields.io/badge/Auth-JWT-000000?logo=jsonwebtokens&logoColor=white)](#)
[![Swagger](https://img.shields.io/badge/API%20docs-Swagger-85EA2D?logo=swagger&logoColor=black)](#)

*Academic project — Grupo 7 · This repository is my personal copy of the backend.*

</div>

---

## 💡 The idea

Most marketplaces are built to sell. **Re.Brinque is built to circulate.**

Parents end up with toys their kids have outgrown, while other families can't afford new ones. Re.Brinque connects them: you publish a toy as a **donation** or a **swap**, someone finds it, you chat, you close the deal — and both sides rate the experience.

And because it's a community platform open to everyone, safety is not an afterthought: every listing's **text and images pass through automated AI moderation** before it goes live.

## ✨ What it does

- 🎁 **Listings for donation or swap** — each toy is published as `DOACAO` (donation) or `TROCA` (swap), with condition (`NOVO`, `SEMINOVO`, `USADO`) and images.
- 🏷️ **Categories and rich filtering** — browse the catalogue by category, brand, type, condition and status.
- 🔄 **A negotiation lifecycle** — a listing moves through `DISPONIVEL → NEGOCIANDO → FINALIZADO`, so both sides always know where the deal stands.
- 💬 **Real-time chat per listing** — one conversation per listing/interested person, powered by Socket.IO. Messages are persisted, so nothing is lost when you close the tab.
- 🔐 **Two ways to sign in** — email + password (bcrypt hashed) or **Google Sign-In**, both issuing a JWT.
- 🛡️ **AI content moderation** — listing text is screened by **Hive AI** and uploaded images by **SightEngine** (nudity, violence, weapons, gore, self-harm, offensive content) before they reach other users.
- 🖼️ **Cloud-hosted images** — the frontend uploads directly to **Cloudinary** using a config the API hands over after authentication.
- 👤 **Profiles with photo** and a trust score (`pontuacao`) attached to every account.
- ⭐ **Ratings and transactions** are part of the data model, so reputation is a first-class citizen.

## 🔄 How a deal flows

```
Sign in (email or Google)
        │
        ▼
Publish a toy  ──▶  text + image moderation  ──▶  listing goes live
        │
        ▼
Someone finds it  ──▶  opens a conversation  ──▶  real-time chat
        │
        ▼
Deal closes  →  listing marked FINALIZADO  →  both sides can rate
```

## 🧱 Tech stack

| Layer | Choice |
| --- | --- |
| Runtime | Node.js · Express 5 |
| Database | PostgreSQL (`pg` connection pool) |
| Real-time | Socket.IO (rooms per conversation) |
| Auth | JWT + bcrypt · Google Sign-In (`google-auth-library`) |
| Files | Cloudinary (direct browser upload) |
| Moderation | Hive AI (text) · SightEngine (images) |
| Docs | Swagger / OpenAPI 3.0 via `swagger-autogen` |

## 🔌 API overview

Base URL: `/api` — interactive docs at **`/api-docs`** once the server is running.

| Group | Endpoints |
| --- | --- |
| **Auth** | `POST /auth/register` · `POST /auth/login` · `POST /auth/google-callback` · `POST /auth/sync` |
| **Listings** | `GET /anuncios` · `GET /anuncios/:id` · `GET /anuncios/usuario/:usuario_id` · `POST /anuncios` · `PUT /anuncios/:id` · `PATCH /anuncios/:id/status` · `DELETE /anuncios/:id` |
| **Chat** | `POST /chat/conversas` · `GET /chat/usuario/:userId/conversas` · `GET /chat/conversas/:conversationId/mensagens` |
| **Profile** | `GET /perfil` · `PUT /usuarios/perfil/foto` · `GET /usuarios/:id/foto` |
| **Uploads** | `GET /cloudinary/config` |
| **Moderation** | `POST /moderar-texto` · `POST /moderar-upload` |

**Real-time events (Socket.IO):** the client joins a `conversa_<id>` room with `joinRoom` and emits `enviarMensagem`; every participant receives `novaMensagem` as soon as it is stored.

## 🗄️ Data model

`usuarios` · `enderecos` · `categorias` · `anuncios` · `imagensAnuncio` · `conversas` · `mensagens` · `transacoes` · `avaliacoes`

Ready-to-run schema scripts are included: **`rebrinquePostgree.sql`** (PostgreSQL) and **`rebrinque.sql`** (legacy SQL Server version).

## 🚀 Running locally

**Prerequisites:** Node.js 18+ and a PostgreSQL database.

```bash
# 1. Install dependencies
npm install

# 2. Configure the environment (create a .env file)
#    DATABASE_URL=postgresql://user:pass@host:5432/dbname
#    JWT_SECRET=...
#    GOOGLE_CLIENT_ID=...        GOOGLE_CLIENT_SECRET=...
#    CLOUDINARY_CLOUD_NAME=...   CLOUDINARY_API_KEY=...   CLOUDINARY_UPLOAD_PRESET=...
#    HIVE_SECRET_KEY=...         SIGHTENGINE_USER=...      SIGHTENGINE_SECRET=...
#    PORT=3000

# 3. (optional) regenerate the OpenAPI spec
npm run swagger-gen

# 4. Start
npm run dev     # nodemon
npm start       # production
```

The server boots on `http://localhost:3000` and the API docs are served at `/api-docs`.

## 📁 Project structure

```
├── index.js                 # Express app, CORS, routes, Swagger
├── socketManager.js         # Socket.IO: join rooms, send/persist messages
├── db.js                    # PostgreSQL pool
├── routes/                  # HTTP routes (auth, anuncios, chat, ...)
├── controllers/             # Business logic
├── middlewares/             # JWT auth middleware
├── swagger.js               # OpenAPI spec generator
└── rebrinquePostgree.sql    # Database schema
```

## 👥 Context

This backend was built with my **Grupo 7** teammates for an academic project. The original repository lives under the team's organization; this copy is kept by me for portfolio and reference purposes.

## 👤 Author

**Diovani Facco** — [LinkedIn](https://www.linkedin.com/in/diovani-facco-3a247b11a/) · [diovanifacco@gmail.com](mailto:diovanifacco@gmail.com)

## 📄 License

ISC.

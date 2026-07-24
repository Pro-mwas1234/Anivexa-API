<div align="center">


<img src="docs/logo.svg" width="80" height="80"/>


# Anivexa API 2.2

**Anime streaming aggregator API — one endpoint, all your sources.**

![Views](https://visitor-badge.laobi.icu/badge?page_id=walterwhite-69.Anivexa-API)
[![Discord](https://img.shields.io/badge/Join%20Discord-5865F2?style=flat-square&logo=discord&logoColor=white)](https://discord.gg/MARQ9z9QSX)
[![GitHub stars](https://img.shields.io/github/stars/walterwhite-69/Anivexa-API?style=flat-square&color=yellow)](https://github.com/walterwhite-69/Anivexa-API/stargazers)

</div>

---

## What is this?

A single API that aggregates anime episode lists and streaming links from multiple providers. Give it an AniList ID, get back everything — episodes, sources, and stream URLs — all in one place.

It's the backbone powering **[Anivexa](https://github.com/walterwhite-69/Anivexa)**, a full anime streaming client built on top of this.

---

## Providers

| Provider | Status | Notes |
|---|---|---|
| **AllManga** | ✅ Active | Large Library |
| **AnimePahe** | ❌ Removed | Cloudflare JS Challenge — no reliable bypass |
| **Reanime** | ✅ Active | Solid source for a wide range of titles |
| **AniKoto** | ✅ Active | Good library, consistent |
| **AnimeGG** | ✅ Active | Fuzzy title matching + compact-query fix for sequels (e.g. Re:Zero S4) |
| **AniNeko** | ✅ Active | Reliable slug-based matching |
| **AniDB App** | ✅ Active | Language-aware, AniDB ID backed |
| **AniZone** | ✅ Active | HLS + subtitles, sub-only; year-based re-scoring prevents wrong-season matches |
| **2dhive** | ✅ Active | Uses MAL ID internally; AniList ID used everywhere else |
| **Anibd** | ✅ Active | Uses Anilist ID internally; AniList ID used everywhere else |
| **Kickassanime** | ✅ Active | Fuzzy search, medium library |

---

## Routes

```
GET /map/:anilistId
```
Returns cross-platform ID mappings — MAL, TVDB, TMDB, Kitsu, AniDB, and more.

```
GET /episodes/:anilistId
GET /episodes/:provider[/:provider...]/:anilistId
```
Returns episode lists in a single response with smart background refresh. Pass one or more provider names in the path to filter results — e.g. `/episodes/anizone/allmanga/16498` returns only those two. Omit providers to get all of them.

```
GET /watch/:provider/:anilistId/sub|dub/:provider-:ep
```
Returns stream URLs for a specific episode from a specific provider.

```
GET /stream/reanime/:id/sub|dub/:ep
```
302 redirect directly to the HLS stream.

---

## Self-hosted

```bash
git clone https://github.com/walterwhite-69/Anivexa-API
cd Anivexa-API
node server.js
```

Runs on Node.js. No build step needed.

---

## Deploying on Vercel

The API works on Vercel out of the box. However, **AniDB App** makes direct requests to `anidb.app`, and Vercel's serverless IPs tend to get blocked by it. To fix this, deploy the included proxy worker to Cloudflare Workers and set your proxy URL in `providers/anidbapp.js`.

### 1. Deploy the proxy worker

You need [Node.js](https://nodejs.org) and a free [Cloudflare account](https://cloudflare.com).

```bash
npm install -g wrangler
wrangler login
cd proxy
wrangler deploy
```

This deploys a small worker to your Cloudflare account. Copy the URL it gives you (e.g. `https://anidb-proxy.yourname.workers.dev`).

### 2. Set your proxy URL

Open `providers/anidbapp.js` and replace the placeholder:

```js
const PROXY = "YOUR_PROXY_URL";
```

with your deployed worker URL:

```js
const PROXY = "https://anidb-proxy.yourname.workers.dev";
```

### 3. Deploy to Vercel

```bash
vercel --prod
```

The provider will try a direct request to `anidb.app` first. If that gets blocked, it automatically falls back through your proxy worker.

---

## What changed recently

### AniZone (new provider)
- Sub-only HLS streams with subtitle and chapter support
- Parses Alpine.js `x-data` blobs directly from the page — no API needed
- Year-based re-scoring on `resolveSeries` prevents wrong-season matches when AniZone uses `(YEAR)` suffixes
- Compact-query fallback (e.g. `Re:ZERO` → `ReZERO`) to catch all season variants in search

### 2dhive (MAL ID fix)
- 2dhive URLs are keyed by MAL ID, not AniList ID — fixed across all episode and stream endpoints
- AniList ID is still used for all route IDs and response metadata; only the actual 2dhive network calls use the resolved MAL ID

### AnimeGG (search fix)
- Added compact-query fallback in `searchFn` (same strategy as AniZone) so titles like `"Re:Zero"` resolve to `"ReZero"` before hitting AnimeGG's search — catches all season slugs including `rezero-starting-life-in-another-world-season-4`

### AllManga
- Cloudflare Turnstile was added sitewide — the provider code is still present but the source is unreachable without a real browser solving the challenge. Marked unusable.

### AnimePahe
- Removed from the active provider list. Switched to Cloudflare JS Challenge; no reliable server-side bypass exists. Code retained but not wired into the episode aggregator.

---

## Contributing

> **Only request providers that self-host their content. No scrapers of third-party sites.**

Got a provider you'd like added? Open an issue or drop it in the Discord.

This project is community-kept-alive — if it helps you, please:

- ⭐ **Star the repo** so others can find it
- 💬 **[Join the Discord](https://discord.gg/MARQ9z9QSX)** to discuss, report issues, or suggest providers
- 🛠️ **Open a PR** if you want to add or fix something

---

<div align="center">

hope it helped :3

[![Discord](https://img.shields.io/badge/Join%20the%20community-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/MARQ9z9QSX)

</div>

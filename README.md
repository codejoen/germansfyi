# Germans in the Valley

A curated list of Germans with deep roots in the Bay Area — founders, engineers, investors, researchers, and more.

**Live site:** [germans.fyi](https://germans.fyi) (coming soon)

## Architecture

```
germans.fyi (Cloudflare Pages)            Cloudflare Workers + KV
┌─────────────────────────────┐          ┌──────────────────────────┐
│  index.html      (public)   │          │  germans-api Worker      │
│  vorschlagen.html (suggest) │───POST──▶│    POST /submit          │
│  admin.html      (admin)    │◀──GET────│    GET  /submissions     │
│  people.json     (data)     │───POST──▶│    POST /submissions/del │
│  404.html                   │          │    POST /deploy           │
└─────────────────────────────┘          │                          │
        ▲                                │  KV: germans-submissions │
        │ auto-deploy on push            └──────────────────────────┘
        │
   GitHub repo (main branch)
```

## Files

| File | Purpose |
|---|---|
| `index.html` | Public homepage — renders the people list from `people.json` |
| `people.json` | Data file — array of HTML strings, one per person |
| `vorschlagen.html` | Public suggestion form — POSTs to Cloudflare Worker |
| `admin.html` | Admin panel — edit list inline, manage submissions, deploy |
| `404.html` | Custom 404 page |
| `README.md` | This file |

## How it works

### People list

People are stored as an array of HTML strings in `people.json`. Section markers like `==Founders==` create category headers. Each entry is a single `<li>` innerHTML.

`index.html` fetches `people.json` and renders each entry as a list item, with section markers creating `<h3>` headers.

### Admin panel (`admin.html`)

Password-protected admin panel with:
- **Inline editing** — contenteditable fields for each person entry
- **Link management** — select text and add/remove hyperlinks via floating toolbar
- **Drag-and-drop reordering** — grab handles to reorder entries
- **Add/remove people** — add new entries or delete existing ones
- **Submissions inbox** — view pending suggestions, approve or dismiss
- **Deploy button** — commits and pushes changes via Cloudflare Worker + GitHub API

### Cloudflare Worker (`germans-api`)

Serverless API with KV storage.

| Method | Path | Auth | Description |
|---|---|---|---|
| `POST` | `/submit` | None | Store a new suggestion in KV |
| `GET` | `/submissions` | `?secret=` | List all pending suggestions |
| `POST` | `/submissions/delete` | `?secret=` | Delete a suggestion by ID |
| `POST` | `/deploy` | `?secret=` | Commit and push changes via GitHub API |

## Deployment

Hosted on **Cloudflare Pages**, connected to the GitHub repo. Any push to `main` triggers automatic deployment.

## Tech stack

- **Frontend:** Plain HTML, CSS, vanilla JavaScript — no frameworks, no build step
- **Hosting:** Cloudflare Pages (auto-deploy from GitHub)
- **Backend:** Cloudflare Workers + KV (form submissions + deploy)
- **Font:** Georgia (serif)

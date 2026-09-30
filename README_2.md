# Paper Tracker

A single-file, no-backend app for managing your reading list: a Trello-style board, two Obsidian-style connection graphs (one for papers, one for notes), a sortable list, markdown+LaTeX notes in a full-size editor, live citation counts (including a direct arXiv lookup), and a correct, visual writing-streak habit tracker — all in one page you host yourself on GitHub Pages.

## What it does

- **Board view** — drag papers between "To Read / Reading / Read / Reference" columns (or add your own columns). Each card shows importance, a computed "smart score," citation count, and tags.
- **Two graphs, Obsidian-style** — a dark, glowing canvas like Obsidian's, split into two focused views instead of one crowded one:
  - **🕸️ Papers graph** — papers are the main citizens, sized by citation count + importance and colored by status. Any note linked from a paper shows up small and muted for context.
  - **🧠 Notes graph** — notes are the main citizens, sized purely by how many things link to them (exactly like Obsidian — write more, connect more, the node grows). Linked papers show up small and muted for context.
  - Write `[[Some Title]]` anywhere in any paper's notes or a standalone note and it becomes a real edge in the relevant graph(s) — if the title doesn't exist yet, it's auto-created as a note. **Hover any node to light up its direct connections** and dim everything else, which is what makes a busy graph actually readable. Drag nodes, scroll or use the +/− buttons to zoom, click to open. A **🧠 New note** button on either graph starts a standalone note directly.
- **List view** — a sortable table of everything, click any column header to sort.
- **Notes, with math, in a real editor** — a full-size (near full-screen) write/preview editor for every standalone note, and a generously-sized one on every paper's Notes tab, with LaTeX-style math via KaTeX: `$E=mc^2$` for inline, `$$\int_0^1 x^2\,dx$$` for a centered display equation. A **🔗 Insert link** button searches your existing papers/notes and drops in a `[[Title]]` for you. Press **Ctrl/Cmd+Enter** anywhere in the standalone note editor to save instantly.
- **Backlinks, automatic** — open any paper or note and its "Connections" tab shows everything that links to it — no manual picking required, it's derived straight from what you've written.
- **Writing streak, fixed and visual** — a 🔥 counter in the top bar tracks consecutive days you've saved any note content, using your *local* calendar day (an evening save now always counts for the right day, wherever you are — this previously used UTC and could misfile a late-evening note as "tomorrow"). Click it for a GitHub-style heatmap with real intensity levels (more saves in a day = darker cell, with a "Less…More" legend), current/longest streak, and total days logged. A gentle one-time-a-day nudge appears if you've gone quiet for a couple of days.
- **Reminders** — Settings → 🔔 Reminders generates a downloadable `.ics` file with a recurring daily/weekday alarm — import it into your phone's calendar app for a real notification, no account or server needed. There's also an optional recipe (below) for true push notifications via a free GitHub Actions + ntfy.sh workflow.
- **Smart priority score** — a 0–100 score per paper blending citation count, recency, your own star rating, and how well its tags match "interest tags" you set in Settings. Adjust the weights to match how you actually want to prioritize.
- **Citation stats & lookup, hardened** — paste a DOI, an arXiv ID/URL (`/abs/`, `/pdf/`, with or without a version — all recognized now), or a plain title, and **Fetch** auto-fills everything. For arXiv links specifically, it queries **both** Semantic Scholar **and** arXiv's own Export API at the same time, trusting arXiv for the paper's own details (title/authors/year/abstract) and Semantic Scholar for the citation count — so it no longer depends on a single service having indexed the paper. Everything has a timeout and one automatic retry on rate limits, so it degrades gracefully instead of silently doing nothing. Newly added papers get a quiet automatic citation lookup in the background, and papers with stale/missing counts (14+ days) are refreshed a few at a time whenever you open the app — no manual "refresh" clicking required.
- **Your own GitHub repo as a sync backend** — optionally push/pull your data (papers, notes, settings, activity) as a JSON file inside this same repo using a personal access token, so your list follows you across browsers/devices. Off by default; your data always lives in the browser's local storage first.
- **Zotero** — not wired up yet (you chose to skip it for now). The data model already has a `zoteroKey` field on every paper, so a one-way "import from Zotero" pass is a small, self-contained addition later — just ask.

Everything runs client-side. There's no server, no build step, no database to host — it's one HTML file.

## Try it locally first

Just double-click `index.html`, or open it in any browser. Everything works immediately, seeded with a few sample papers so you can see how it behaves — edit or delete them once you're oriented.

## Deploy it to GitHub Pages (new dedicated repo)

This keeps it completely separate from your existing `arezooaalipanah.github.io` site, so there's zero risk of breaking your current page.

1. Go to **github.com → New repository**. Name it something like `paper-tracker`. Public repo, no need to add a README/license (you already have one here).
2. Upload `index.html` (and this `README.md` if you want) to the repo — easiest way: on the repo's page, click **Add file → Upload files**, drag both files in, commit.
3. Go to the repo's **Settings → Pages**.
4. Under "Build and deployment," set **Source: Deploy from a branch**, **Branch: main**, folder **/ (root)**. Save.
5. Wait ~1 minute, then your app is live at:
   `https://arezooaalipanah.github.io/paper-tracker/`

### Link it from your homepage

Add a link/button somewhere on `arezooaalipanah.github.io` (in whatever file has your nav or project list), e.g.:

```html
<a href="https://arezooaalipanah.github.io/paper-tracker/">📚 Paper Tracker</a>
```

If you'd rather it live *inside* your existing site instead of a separate repo, that also works: just add `index.html` into a `paper-tracker/` subfolder of your `arezooaalipanah.github.io` repo instead of a new repo, and it'll be reachable at the same kind of URL — everything else below works identically either way.

## Where your data lives

Your papers, notes, and settings are saved to **this browser's local storage** the instant you make a change — nothing is ever sent anywhere unless you turn on GitHub sync or click a citation-lookup button. That means:

- It's private by default — only visible in the browser/device you used to add it.
- Clearing your browser's site data (or a fresh incognito window) starts you over. **Use Settings → Local backup → Export JSON** every so often, especially before doing anything that might clear site data.
- If you want the same list on your phone and your laptop, turn on GitHub sync (next section) — or just re-import an exported JSON file on the other device.

## Optional: sync across devices via your GitHub repo

This stores your data as `data/papers.json` right inside the same repo, so any device can pull it.

1. Go to **github.com/settings/tokens?type=beta** → **Generate new token**.
2. Give it a name like "paper-tracker sync," set expiration however you like, and under **Repository access** choose **Only select repositories** → pick your `paper-tracker` repo (don't grant it access to anything else).
3. Under **Permissions → Repository permissions**, set **Contents: Read and write**. Leave everything else as "No access."
4. Generate it, copy the token (starts with `github_pat_…`).
5. In the app, click the **⚙️ Settings** icon → **Sync via your GitHub repo** → fill in your username, the repo name, branch (`main`), and paste the token → **Push data to GitHub**.
6. On another device/browser, open the same URL, go to Settings, fill in the same repo details and token, and click **Pull data from GitHub**.

The token is stored only in that browser's local storage and is sent only to `api.github.com` when you click push/pull — it is never written into the page itself, so the public HTML file stays safe to host even though sync is configured per-browser.

## Tuning how it prioritizes

Open **Settings → Smart priority weights**. Four sliders (citations, recency, your rating, interest-tag match) control the "smart score" shown on every card/row. Add your current focus areas under **Interest tags** (e.g. `rag, agents, efficiency`) — unread papers tagged with those will rank higher automatically. There's no single "correct" formula here, so it's worth nudging the sliders until the top of your "To Read" column actually matches what you'd pick by hand.

## Customizing columns

Settings → **Board columns** lets you rename or remove the default four (To Read / Reading / Read / Reference), and the **+ Add column** button on the board lets you add new ones (e.g. "Someday," "Writing up," "Cite in my paper").

## A note on the citation/metadata lookups

These use Semantic Scholar's free public Graph API (no key required) with a CrossRef fallback for DOIs. It's a shared free tier, so if a lookup fails, it's usually a brief rate limit — the app retries once automatically; if it still fails, wait a few seconds and try again. Bulk-refreshing (and the quiet background top-up) is deliberately paced (~1 request/second) to stay polite to the free API.

## Optional: real push notifications to your phone

The built-in `.ics` reminder (Settings → 🔔 Reminders) is a calendar alarm — reliable, but it's not a "push notification." If you want an actual phone push with zero always-on server, here's a free recipe using GitHub Actions (a scheduler that lives in this repo) and [ntfy.sh](https://ntfy.sh) (a free, no-signup push relay):

1. Install the **ntfy** app (iOS/Android), and subscribe to a topic name only you know — treat it like a password, e.g. `arezoo-reading-x7f2`. Anyone who knows the exact topic name can send to it, so make it unguessable rather than something like `paper-tracker`.
2. Ask me (Claude) to add the workflow file next time we talk, and tell me your topic name — I'll create `.github/workflows/reading-reminder.yml` in this repo that curls `https://ntfy.sh/<your-topic>` on whatever schedule you want, for free, using GitHub's own infrastructure (no server for you to run or pay for).
3. Once it's in the repo, GitHub Actions runs it automatically on schedule, and your phone gets a real push notification from the ntfy app.

This is entirely optional — the in-app streak counter and the calendar reminder both work today with no setup at all.

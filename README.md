# The Archive

A wiki for tracking players, their OCs, and the stories they've been in — all cross-linked automatically. No app to install, no database, no build step. Just three JSON files and one HTML file.

## Adding a new entry

Everything lives in the `data/` folder:

- `data/players.json` — the people
- `data/ocs.json` — their characters
- `data/stories.json` — the arcs/sessions they show up in

To add someone or something new, open the relevant file (either locally or by clicking it on GitHub and hitting the pencil/edit icon), copy one of the existing entries, and fill in your own values. Then commit.

### Adding a player
```json
{ "id": "your-id-here", "name": "Display Name", "bio": "A sentence or two.", "image": "", "ocIds": [] }
```
- `id` — lowercase, no spaces, used in the URL. Keep it unique.
- `ocIds` — leave this empty. It fills itself in automatically once you give one of your OCs a matching `playerId` (see below) — you never need to maintain this list by hand.
- `image` — optional. A link to a hosted image (imgur, discord CDN, etc). Leave `""` to skip it.

### Adding an OC
```json
{
  "id": "your-oc-id",
  "name": "Display Name",
  "playerId": "your-id-here",
  "bio": "",
  "tags": ["some-tag"],
  "storyIds": [],
  "relationships": [{ "ocId": "other-oc-id", "label": "friend" }]
}
```
- `playerId` — must match a player's `id` exactly. This is what links them to their player.
- `tags` — anything you want. Tags become clickable and gather everyone/everything sharing that tag on one page.
- `storyIds` — the `id`s of any stories this OC appears in. Must match a story's `id` exactly.
- `relationships` — other OCs, by `id`, with a short label describing the connection.

### Adding a story
```json
{ "id": "your-story-id", "title": "Story Title", "summary": "", "date": "", "image": "", "ocIds": ["oc-id-1", "oc-id-2"], "tags": [] }
```
- `ocIds` — everyone in the cast, by `id`.

## The golden rule: only edit the source, never the copy

Every "reverse" list — a player's roster of OCs, a story's cast list, who else shares a tag — is calculated automatically from these forward links. You never need to update two places for one change. Add an OC with the right `playerId` and `storyIds`, and it'll show up on that player's page and that story's page on its own.

## Publishing / updating on GitHub Pages

**First-time setup** (only needed once):
1. Push this whole folder to a GitHub repo.
2. In the repo, go to **Settings → Pages**.
3. Under "Build and deployment," set Source to **Deploy from a branch**, branch `main`, folder `/ (root)`.
4. Save. GitHub gives you a URL (something like `https://yourname.github.io/repo-name/`) — that's your live wiki.

**Every time after that:**
1. Edit a JSON file (directly on github.com works fine — no local setup needed).
2. Commit the change (GitHub's web editor commits for you when you hit "Commit changes").
3. Wait 30–60 seconds. GitHub Pages rebuilds automatically and your edit is live.

That's it — no build step, no server, nothing to run. Editing JSON on your phone through the GitHub app or mobile browser works exactly the same way.

## Local preview (optional)

Opening `index.html` directly by double-clicking it won't work — browsers block a page from fetching local JSON files that way. If you want to preview before pushing, run a tiny local server from this folder:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000` in a browser.

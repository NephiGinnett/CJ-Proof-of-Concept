# The Archive

A wiki for tracking players, their OCs, and the stories they've been in — all cross-linked automatically. No app to install, no database, no build step. Just three JSON files and one HTML file.

## Adding a new entry (the easy way)

Click **+ Add entry** in the top nav of the site itself. Pick Player / OC / Story, fill in the fields, and it builds properly-formatted JSON for you on the right as you type — including turning names you type into other fields (like an OC's story list) into the right lowercase-hyphenated ids automatically. Hit **Copy JSON**, then paste it into the matching file below as a new entry in the array (see "Adding a new entry (by hand)" for exactly where it goes), and commit.

This builder doesn't save anything on its own — it's a static site with no backend, so there's no direct "upload." It just saves you from hand-writing JSON and getting a field wrong.

## Adding a new entry (by hand)

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

## Checking it still works

There's a self-test page at `test.html`. Open it the same way you'd open the wiki — on the live
site it's just `.../test.html` — and it will load the real `index.html` and the real `data/*.json`
files, click through the site the way a visitor would, and tell you what it found.

It checks two different things:

- **Your data.** Valid JSON, no duplicate ids, and every cross-link pointing at something that
  actually exists — a `playerId`, `storyIds`, a story's `ocIds`, a relationship. A typo'd id
  doesn't crash the site, it just makes an entry quietly disappear from a list, which is easy to
  miss by eye. This catches it.
- **The site itself.** Every page type opens, the reverse lists derive themselves correctly, the
  **+ Add entry** builder produces JSON the site can read back, and text with odd characters in it
  (quotes, angle brackets) stays text instead of turning into markup.

Green all the way down means it's safe to publish. A red row tells you which entry is wrong and
what to fix. It's read-only — it never writes to your data files.

## Local preview (optional)

Opening `index.html` directly by double-clicking it won't work — browsers block a page from fetching local JSON files that way. If you want to preview before pushing, run a tiny local server from this folder:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000` in a browser.

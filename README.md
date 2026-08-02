# The Archive

A wiki for tracking players, their OCs, and the stories they've been in — all cross-linked automatically. No app to install, no database, no build step. Just three JSON files and one HTML file.

## Uploading a file

Click **↑ Upload** in the top nav, then **Choose files** (or drag them onto the page). Give it
`players.json`, `ocs.json`, `stories.json`, or any combination — it works out which is which from
what's inside them, so the filename doesn't have to be exact.

Nothing is saved yet. The file loads straight into the site, a gold bar appears saying
**Preview — not published**, and you can browse around exactly as if it were live: every page,
every cross-link, every tag. It also checks the file over and tells you if anything points at
something that doesn't exist — a `playerId` with a typo in it, a story id that doesn't match.

When it looks right, press **Publish**. Then either:

- **Publish to GitHub** — writes the file into the repo for you, the same as committing it by hand.
  Needs a GitHub token the first time (see below); after that it's one press.
- **Download the file instead** — hands you the finished file to drag onto GitHub yourself, no
  token involved.

If it doesn't look right, press **Discard** and the published data comes straight back. Nothing
you preview ever touches the repo until you publish it.

### The token, once

Publishing straight to GitHub needs a fine-grained personal access token with **Contents:
Read and write** on this repository, and nothing else. Settings → Developer settings → Personal
access tokens → Fine-grained tokens. Paste it into the Upload page once and it's remembered in
that browser.

It's stored in your browser only — never in the repo, never sent anywhere except GitHub. But
anyone using that browser profile can publish with it, and anyone you hand the token to can write
to the repo, so don't paste it on a shared computer and don't give it to other players. There's a
**Forget token** button. If you'd rather not use one at all, "Download the file instead" does the
same job with a manual commit at the end.

## Adding a new entry (the easy way)

Click **+ Add entry** in the top nav. Pick Player / OC / Story, fill in the fields, and it builds
properly-formatted JSON as you type — including turning names you type into other fields (like an
OC's story list) into the right lowercase-hyphenated ids automatically.

**Add to preview** drops it into the site right then, and takes you to its new page so you can see
it. From there it's the same as an upload: look around, then publish when it looks right. If you'd
rather do it by hand, **Copy JSON** still gives you the entry to paste into the matching file.

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
  **+ Add entry** builder produces JSON the site can read back, uploading a file previews it and
  discarding it puts the published data back, and text with odd characters in it (quotes, angle
  brackets) stays text instead of turning into markup.

Green all the way down means it's safe to publish. A red row tells you which entry is wrong and
what to fix. It's read-only — it never writes to your data files.

## Local preview (optional)

Opening `index.html` directly by double-clicking it won't work — browsers block a page from fetching local JSON files that way. If you want to preview before pushing, run a tiny local server from this folder:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000` in a browser.

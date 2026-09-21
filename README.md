# Vocabulario

A Spanish vocabulary trainer with spaced repetition. One HTML file, no build step,
no backend. Cards are stored in the browser's local storage on the device you use.

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app: markup, styles, logic |
| `manifest.webmanifest` | Makes it installable as a home-screen app |
| `sw.js` | Service worker, so it opens and works offline |
| `icon-192.png`, `icon-512.png` | App icons (Spanish flag) |
| `apple-touch-icon.png` | Home-screen icon on iOS |

## Publishing it on GitHub Pages

1. Create a new repository on GitHub, e.g. `vocabulario`. Public is fine; Pages on
   a private repo needs a paid plan.
2. Upload these files to the root of the repository (drag them into the
   "uploading an existing file" box, or `git add . && git commit && git push`).
3. In the repo, go to **Settings → Pages**.
4. Under **Source**, pick **Deploy from a branch**. Branch: `main`, folder: `/ (root)`. Save.
5. Wait a minute, then open `https://<your-username>.github.io/vocabulario/`.

All paths are relative, so it works from a subfolder like `/vocabulario/` without
any changes.

## Installing it on your phone

- **iOS (Safari)**: open the URL, tap Share, then *Add to Home Screen*. It must be
  Safari — Chrome on iOS cannot install web apps.
- **Android (Chrome)**: open the URL, tap the menu, then *Install app* or
  *Add to Home screen*.

Once installed it launches full screen, with no browser chrome, and opens offline.

## Where the data lives

Cards live in `localStorage` on that one device. They survive closing the app and
restarting the phone. They do **not** sync between devices, and they are gone if
you clear the browser's site data or delete the app.

So: use **Deck → Export backup** now and then. It writes a JSON file you can keep
anywhere. **Import backup** merges a file back in, matching cards by id and keeping
whichever copy was edited most recently, so importing on a second device is a
reasonable manual sync.

## Updating the app

Edit `index.html`, then bump `VERSION` in `sw.js` (`"v1"` → `"v2"`) and push both.
Without the bump, installed phones may keep serving the cached old build.

## How the scheduling works

A simplified SM-2, the algorithm Anki grew out of.

- New cards: 1 minute, then 10 minutes, then they graduate to 1 day.
- **Again** on a graduated card halves its interval, drops the ease factor by
  0.20, and puts it back in a 10-minute loop.
- **Hard** multiplies by 1.2 and drops ease by 0.15.
- **Good** multiplies by the card's ease factor (starts at 2.5).
- **Easy** multiplies by ease × 1.3 and raises ease by 0.15.
- Intervals get ±5% of randomness so cards added on the same day don't clump
  together forever, and cap out at 3 years.

Each button shows the resulting interval before you press it. New cards per day
defaults to 20 and is adjustable in Deck → Settings.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `Space` | Reveal the answer, then grade it Good |
| `1` `2` `3` `4` | Again / Hard / Good / Easy |
| `Enter` (Add tab) | Move to the next field, or save from the English field |
| `Cmd/Ctrl + Enter` | Save from anywhere in the Add form |

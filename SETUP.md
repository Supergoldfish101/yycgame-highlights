# Live gallery setup

One static page (`index.html`) that reads a public Google Drive folder and shows a live grid. Nothing runs on a server; the parents' browsers call the Drive API directly.

## 1. Share the Drive folder

1. In Google Drive, right-click the folder Autosync uploads into → Share → General access → **Anyone with the link**, role **Viewer**.
2. Open the folder and copy the ID from the URL: `https://drive.google.com/drive/folders/<THIS_PART>`.

Optional: create subfolders inside it (for example `vs Lincoln`, `vs Central`). Each subfolder becomes a tab on the page. Point Autosync at the current game's subfolder, or just leave everything in the root folder for a single feed.

## 2. Create an API key (free)

1. Go to https://console.cloud.google.com and create a project (any name, e.g. `highlights-gallery`).
2. APIs & Services → Library → search **Google Drive API** → Enable.
3. APIs & Services → Credentials → Create credentials → **API key**.
4. Click the new key to edit it:
   - **Application restrictions**: HTTP referrers. Add your site, e.g. `https://yourname.github.io/*` (add `http://localhost/*` too while testing).
   - **API restrictions**: Restrict key → Google Drive API only.
5. Copy the key.

The key only allows listing files the public already has access to, and only from your site, so it is safe to leave in the page source.

## 3. Fill in the page

Open `index.html`, find the `CONFIG` block near the top of the script, and set:

```js
title:    "Eagles Varsity 2026",
apiKey:   "AIza...",
folderId: "1AbC...",
```

Everything else can stay as is. `pollSeconds: 30` is a good balance; the Drive API free quota is far above what a few dozen parents polling every 30 seconds will use.

## 4. Host it

**GitHub Pages** (free, custom domain optional):

1. Create a public repo, e.g. `highlights`.
2. Add `index.html` to it and push.
3. Repo → Settings → Pages → Source: Deploy from branch `main`, folder `/ (root)`.
4. The site appears at `https://<your-user>.github.io/highlights/` within a minute or two.

Cloudflare Pages or Netlify work the same way: drag the folder in, get a URL.

## 5. Test

- Open the URL. The dot in the header should turn green and say `live`.
- Protect a frame on the camera. It should appear at the top of the grid within one poll interval, with a NEW badge, and a toast if the viewer has scrolled down.
- Tap a tile: lightbox with swipe, arrow keys, Download, and Share (uses the phone's share sheet).

## Previewing without a key

Add `?demo` to the URL to see the layout with placeholder images:
`index.html?demo`

## Troubleshooting

| Symptom | Cause |
|---|---|
| "API key not valid" | Key is restricted to a referrer that doesn't match the URL you're on. Check the referrer list. |
| "File not found" / empty with photos present | Folder isn't shared as Anyone with the link, or the folder ID is wrong. |
| Thumbnails broken but list loads | Files themselves aren't public. Sharing the folder should cover them; re-check that Autosync isn't uploading to a different folder. |
| Nothing new appears | Autosync hasn't uploaded yet. Check the Autosync app's history. |

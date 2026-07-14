# Stem Fix — Stem Repair Console

A five-stage repair chain for stems — transient gate, high-pass filter, low-mid EQ cut,
compressor, stereo imaging — built for the specific fault pattern common in AI-generated
audio: washed-out reverb tails, low-mid mud, and cluttered stereo image.

Everything runs client-side with the Web Audio API. No server, no upload, no backend.
That also means it works as an installable app with zero extra infrastructure.

## What's in this folder

| File                       | Purpose                                              |
|-----------------------------|-------------------------------------------------------|
| `index.html`                | The whole app — UI, DSP, and playback in one file     |
| `manifest.json`              | Tells Android/Chrome how to install it as an app       |
| `service-worker.js`          | Caches the app shell so it works offline once installed |
| `icon-192.png` / `icon-512.png` / `icon-maskable-512.png` | App icons |
| `apple-touch-icon.png`, `favicon-32.png` | iOS / browser tab icons          |
| `.nojekyll`                  | Stops GitHub Pages from mangling the deploy (see below) |

## 1. Push it to GitHub

```bash
cd Stem-Fix
git init
git add .
git commit -m "Stem Fix"
git branch -M main
git remote add origin https://github.com/<your-username>/Stem-Fix.git
git push -u origin main
```

## 2. Turn on GitHub Pages

Repo → **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main`, folder `/ (root)` → Save.
Give it a minute, then your app is live at:

```
https://<your-username>.github.io/Stem-Fix/
```

The `.nojekyll` file matters here — without it, GitHub Pages runs your site through Jekyll,
which silently drops any folder starting with a dot (like `.well-known`, which some Android
packaging tools need later). Keep it in the repo root even if it looks like an empty do-nothing file.

## 3. Install it on Android

Open the GitHub Pages URL in **Chrome on Android**. You'll either get an automatic
"Add Stem Fix to Home screen" prompt, or you can trigger it manually:

Chrome menu (⋮) → **Install app** (or **Add to Home screen**)

This isn't a shortcut — Chrome generates a real installed **WebAPK** with its own icon in your
app drawer, its own task-switcher entry, and it opens full-screen with no browser chrome.
Once installed, the service worker means it keeps working with no signal, on a plane, in
the studio, wherever.

This is genuinely the fastest path to "an Android app" for something only you (or people you
send the link to) will use. No Play Store review, no signing key to manage, and any edit you
push to `main` shows up for everyone next time they open it.

## 4. Optional: a real, sideloadable `.apk`

If you specifically want an actual `.apk` file — to sideload without visiting the URL first,
or to eventually list on the Play Store — use **[PWABuilder](https://www.pwabuilder.com)**:

1. Deploy to GitHub Pages first (step 2), so PWABuilder has a real URL with your manifest to read.
2. Go to pwabuilder.com, paste your GitHub Pages URL, click **Start**.
3. It'll score your manifest/service worker (should be green across the board with what's
   already in this repo) — click **Package for stores → Android**.
4. It hands you a `.zip` containing a signed `.apk` (install directly on a device) and a
   `.aab` (what the Play Store actually wants).

Under the hood this uses Google's own **Bubblewrap**/Trusted Web Activity tooling — it's not
a hacky wrapper, it's the same mechanism Play Store PWAs use. No coding required on your end
beyond what's already in this repo.

## Notes for future you

- Bump `CACHE_NAME` in `service-worker.js` (e.g. `stem-fix`) any time you change
  `index.html` — otherwise installed devices will keep serving the old cached version for a
  while.
- All the DSP is in one script block in `index.html` under `StemFixDSP` — if this grows
  into more than a stem-repair tool, that's the natural place to split into modules.

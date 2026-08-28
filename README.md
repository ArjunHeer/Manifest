# Manifest — Calisthenics Progress

A progression tracker for the 3-day and 4-day calisthenics splits. Log every set, see
what you did last time, and climb one rung at a time.

**Files in this folder — keep them together, the paths are relative:**

```
manifest/
├── index.html              the whole app
├── manifest.webmanifest    app name, icon, standalone display
├── sw.js                   offline cache
└── icons/
    ├── icon-192.png            icon-512.png
    ├── icon-maskable-192.png   icon-maskable-512.png
    ├── apple-touch-icon-180.png
    ├── favicon-32.png
    └── emblem-source.png   the logo at 512px, if you need to re-export
```

Your training data is stored **on the device**, in the app's own storage. It is never
uploaded anywhere. That also means it does not sync between devices, and clearing the
app's storage erases it.

---

## Option 1 — Install straight to your home screen (2 minutes, no build)

This gives you the icon, the app name, its own window with no browser chrome, and full
offline use. On Android this is a genuine installed app (Chrome packages it as a WebAPK,
which is an APK generated for you on the fly).

1. Put this folder online. Any static host works — the free options:
   - **Netlify Drop** (`app.netlify.com/drop`): drag the folder onto the page. Instant URL.
   - **GitHub Pages**: create a repo, upload the contents of this folder, then
     Settings → Pages → Source: `main` / root. You get `https://<you>.github.io/<repo>/`.
   - **Cloudflare Pages**, **Vercel**, or any web host you already have.

   It **must** be `https://` — service workers and installation are blocked on plain http.

2. On your Pixel, open that URL in Chrome.
3. Tap the ⋮ menu → **Add to Home screen** → **Install**.

Done. It appears in your app drawer as **Manifest** with the emblem icon, opens
fullscreen, and works with no signal.

---

## Option 2 — Get an actual `.apk` file (PWABuilder, ~10 minutes)

Use this if you specifically want an APK file you can sideload or keep.

1. Host the folder as in Option 1 and copy the URL.
2. Go to **https://www.pwabuilder.com** and paste the URL.
3. It will score the app — the manifest and service worker here are already complete,
   so it should pass without changes.
4. Click **Package for stores → Android**.
   - Package ID: something like `com.yourname.manifest`
   - App name: `Manifest`
   - Leave "Signing key" as **Create new** and **download the key + password**. Without
     that key you can never ship an update to the same app.
5. Download the zip. Inside you get `app-release-signed.apk` (sideload this) and
   `app-release-bundle.aab` (only needed for the Play Store).
6. Copy the `.apk` to your Pixel, tap it, and allow **Install unknown apps** when prompted.

The zip also contains `assetlinks.json`. Upload that to `/.well-known/assetlinks.json` on
your host — it verifies you own the site and removes the thin URL bar from the top of the
app. Without it the app still works, it just shows the address for a moment on launch.

---

## Option 3 — Build the APK yourself (Bubblewrap)

If you'd rather not use a web service. Requires **JDK 17** and the **Android SDK**
(easiest via Android Studio) on your computer.

```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest https://<your-host>/manifest/manifest.webmanifest
bubblewrap build
```

`bubblewrap build` outputs `app-release-signed.apk` in the project directory. It will
prompt you to create a signing keystore on first run — again, keep it safe.

---

## Why an APK couldn't be compiled for you directly

Building an Android package requires the Android SDK build tools (`aapt2`, `d8`,
`apksigner`) and Gradle. In the environment this app was written in, `dl.google.com`,
`maven.google.com` and `services.gradle.org` are all blocked, and no Android SDK is
installed — so the compile step cannot run there. Every option above moves that one step
to a machine or service that does have the toolchain. Option 1 skips the compile
entirely and still gets you an installed app.

---

## Notes

- **Updating the app**: replace `index.html` on your host and bump `CACHE = 'manifest-v1'`
  in `sw.js` to `manifest-v2`. Otherwise the old cached copy keeps being served.
- **Backing up your log**: your sessions live in the app's local storage. There is no
  export button yet — worth adding before you have a year of data in there.
- **Two installs, two logs**: the copy in the Claude chat and the installed app keep
  separate data. Pick one and stay on it.
- **The icon**: `icons/emblem-source.png` is the artwork at 512px with a transparent
  background, if you ever want to regenerate the sizes or use it elsewhere.

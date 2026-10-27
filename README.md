# TikTok Identifier

An Android app for collecting TikTok video links in bulk. Log in to TikTok inside the app, tap the videos you want, and copy all of their links to the clipboard at once, one link per line.

> **Unofficial.** This project is not affiliated with, endorsed by, or connected to TikTok or ByteDance. "TikTok" and its logo are trademarks of their respective owners. Intended for personal use.

---

## Features

- **Built-in TikTok browser.** Log in to your own account and browse like normal.
- **Multi-select.** Turn on *Select* mode and tap as many videos as you want. Selected videos get a red outline and a check mark. Tap again to deselect.
- **Copy all at once.** One tap copies every selected link to the clipboard, one per line, ready to paste anywhere.
- **Works on any video grid.** Profiles, search results, Favorites, Liked and more.
- **Add the open video.** While watching a single video, add it to the selection with one button.
- **PC mode.** Loads the desktop version of TikTok, which avoids the "Open TikTok app" popup and makes search and the Liked tab work properly. Can be switched off.
- **Clean links.** Tracking parameters are stripped. Links look like `https://www.tiktok.com/@user/video/1234567890`.
- **Photo posts supported.** `/photo/` links are handled the same way as videos.
- **Stays logged in.** The session is stored by the in-app browser like in any browser.

---

## How to use

The bottom bar has five buttons:

| Button | What it does |
|---|---|
| **Select: OFF / ON** | Toggles selection mode. While ON, tapping a video selects it instead of opening it. |
| **+ Video** | Adds the video you currently have open (a single video page) to the selection. |
| **Copy (n)** | Copies all *n* selected links to the clipboard, one per line. |
| **Clear** | Empties the selection. |
| **PC: ON / OFF** | Switches between the desktop and mobile versions of TikTok (reloads the page). |

### Typical workflow

1. Open the app and log in to TikTok.
2. Go to a profile, your Liked or Favorites tab, or a search result.
3. Tap **Select: OFF** so it reads **Select: ON**.
4. Tap every video you want. They are highlighted and the counter on **Copy** goes up.
5. Scroll to load more videos and keep selecting. The selection is kept while you browse.
6. Tap **Copy (n)**, then paste the links anywhere (notes, chat, a document).
7. Turn **Select** back OFF to open videos normally again.

### Tips

- On the **For You** feed there are no per-video links to tap. Open a video on its own page and use **+ Video** instead.
- The desktop page looks small on a phone. Pinch to zoom.
- The Back button goes back in the browser history and closes the app when there is nothing left to go back to.
- The selection lives in memory only and is cleared when the app is closed.

---

## How it works

The app is a single Android activity built around a `WebView` that loads `tiktok.com`.

1. **Browsing and login.** The `WebView` loads TikTok directly, with JavaScript, DOM storage and cookies enabled. Login happens on TikTok's own page, so the app never sees or stores your password. Cookies are kept by the `WebView`, which keeps you logged in.
2. **PC mode.** The app builds a desktop Chrome user agent from the device's own WebView version and forces a wider page viewport, so TikTok serves its desktop layout.
3. **Script injection.** After each page load and each in-page navigation, the app injects a small script (`assets/inject.js`) into the page. The injection is idempotent, so running it repeatedly is safe.
4. **Finding videos.** The script looks for links shaped like `/@user/video/<id>` or `/@user/photo/<id>`. When you tap something, it looks for the link on the element you touched, and if there is none, for a container that holds exactly one video link.
5. **Select mode.** While Select is ON, the script intercepts touch and click events on videos before TikTok's own code sees them, so tapping selects instead of opening. It then reports the link to the app through a JavaScript bridge (`TTBridge.toggle`).
6. **Selection state.** The app keeps selected links in an ordered set, pushes the current selection back into the page so highlights stay correct (even when TikTok loads more videos while you scroll), and updates the button counters.
7. **Clipboard.** **Copy** joins all selected links with line breaks and places them on the Android clipboard.
8. **Deep links are blocked.** Non-web links such as `tiktok://` or `intent://` are ignored so the page stays inside the app instead of trying to launch the official TikTok app.

---

## Privacy

- No analytics, no tracking, no ads and no accounts of its own.
- The app does not run any server and does not send your links or data anywhere. It only talks to TikTok's website, exactly like a browser would.
- The only permission it requests is `INTERNET`.
- Your TikTok login is handled by TikTok's own page inside the in-app browser.

---

## Install
- Scroll Down To Releases And Download
"Tiktok Link Identifier v1.0.apk".
- Go To Your Downloads Folder.
- Find The Application And Install!

 ---

## Requirements

- Android 7.0 (API 24) or newer
- An up-to-date *Android System WebView* / Chrome (updates through the Play Store)
- Internet connection and a TikTok account

---

## Technical details

| | |
|---|---|
| Language | Java (no third-party libraries) |
| UI | Programmatic layout: `WebView` plus a button bar |
| Page logic | Plain JavaScript injected from `app/src/main/assets/inject.js` |
| Build system | Gradle with Android Gradle Plugin 8.5.2 |
| Min / target SDK | 24 / 34 |
| CI | GitHub Actions (JDK 17, Gradle 8.9) |

### Project structure

```
TikTokLinkPicker/
├── .github/workflows/build.yml      CI workflow that builds the APK
├── build.gradle, settings.gradle    Gradle configuration
└── app/
    ├── build.gradle                 App module configuration
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/example/tiktoklinks/MainActivity.java
        ├── assets/inject.js         Selection logic injected into the page
        └── res/                     Launcher icons
```

---

## Known limitations

- TikTok may block or limit sign-in with Google or Apple inside embedded browsers. Email, phone or QR login is more likely to work.
- The For You feed has no per-video links, so use **+ Video** on a single video page.
- TikTok changes its website from time to time. If selecting stops working after a site update, the link detection in `inject.js` may need adjusting.
- Debug builds only. The APK is not signed for the Play Store.

---

## Disclaimer

This is a personal-use tool that works in your own logged-in browser session. Respect TikTok's Terms of Service and the rights of the creators whose videos you collect.

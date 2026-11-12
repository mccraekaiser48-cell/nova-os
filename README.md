# Nova OS — Browser app replaced with the full FireFox

Only `index.html` changed. One block, around line 27210: the app that used to be
"Brave, streamed over noVNC" is now "FireFox", pointing at

    https://mccraekaiser48-cell.github.io/firefox-wasm./

- app id stays `browser`, so every existing shortcut, pin and Start-menu entry
  keeps working and now launches FireFox
- the tile and window-header icon uses the site's real `icon-192.png`
- clicking it opens the browser in its own window (1280x900) and leaves a
  launch panel in the OS window with an "Open FireFox" button

## Why it opens a window instead of an iframe

Gecko needs SharedArrayBuffer, which browsers only expose to cross-origin
isolated documents. Cross-origin isolation is inherited by the whole frame
tree, so an iframe inside a desktop that is not isolated can never get it — the
engine would stall at `dependency: loading-workers`.

Making the whole OS cross-origin isolated is possible (COOP/COEP headers from
`sw.js`), but it applies to every page of the OS and can break embedded
third-party sites, so this build does not do it.

## Install

1. Replace `index.html` in the nova-os repository with this one.
2. Commit to the branch Pages publishes.
3. Reload the OS with Ctrl+Shift+R (the service worker serves navigations
   network-first, so no cache bump is needed).
4. Click the FireFox tile in the Start menu.

## Keeping logins

The engine's profile lives in memory (MEMFS), so cookies survive only while the
FireFox window stays open. Clicking the tile again now **focuses** that window
instead of navigating it — navigating would reload the engine and sign you out.
Closing the window is a full reset.

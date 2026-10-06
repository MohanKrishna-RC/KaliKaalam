# Privacy Policy — Kalikaalam (NovaGenesis)

**Last updated:** 6 October 2026

## Summary

Kalikaalam does not collect, transmit, sell or share any data. There is no
account, no server and no analytics.

## What the extension stores, and where

Everything is kept locally in your browser profile using `localStorage` and
`chrome.storage.local`. Nothing is ever uploaded.

| Data | Where | Why |
| --- | --- | --- |
| Display settings (theme, wallpaper, dial style, time zone) | `localStorage` key `chronosphere:v1:config` | So your setup survives a restart |
| Wallpaper images you upload | `localStorage` key `chronosphere:v1:wallpapers` | Images never leave your device |
| Alarm definitions (time and label only) | `localStorage` and `chrome.storage.local` | So the background worker can fire a notification |

## Permissions, and why each is needed

- **`storage`** — keeps your settings and alarms on your device.
- **`alarms`** — schedules your alarms so a notification can appear even when no
  Kalikaalam tab is open. Nothing is read from outside the extension.
- **`notifications`** — shows your alarm when it fires.

The extension requests **no host permissions**, registers **no content scripts**,
and injects nothing into the pages you visit.

## Network requests

The only outbound requests are to `fonts.googleapis.com` and `fonts.gstatic.com`
to download the display fonts. If you prefer zero outbound requests, the app
falls back to system fonts and works identically. Nothing about your settings,
location, or usage is ever sent anywhere.

## Location

The GPS button in the control deck is opt-in and uses the browser's geolocation
API only when you press it. Coordinates are used locally to compute sun and moon
positions and are never transmitted.

## Children

The extension collects no data from anyone, including children under 13.

## Changes

Any future change to this policy will be published in this file and reflected in
the extension's version bump in the store listing.

## Contact

Questions or concerns: open an issue on the project's source repository.

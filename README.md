# Masjid Iqamah Timer

*Built for my masjid, shared with everyone.*

## The problem

I'm the Honorary Chairman of **Masjid Rahimabi Kebun Limau** in Singapore. Running a masjid, you learn quickly that the small things matter and one of the most persistent headaches was the **iqamah timing**.

It was all manual originally as it has been for years with no timer, no display. 

The call to iqamah was left to the imam leading prayer for that solat, which meant timing varied slightly from imam to imam. 

Some jemaah noticed. They'd tell us the iqamah never felt consistent

So I built this: a display that runs itself. It pulls the official MUIS timings and moves through every stage — pre-adhan, adhan, iqamah countdown, saf alignment, and a dimmed screen during the prayer itself — automatically, with zero manual switching. One consistent countdown for every imam, every prayer, every day. The imams follow the same clock, the jemaah see the same timing, and nobody has to manage anything by hand.

Sharing it for free, no strings, no account needed. 

## What it does

- Pulls daily prayer times from the [AlAdhan API](https://aladhan.com/prayer-times-api) using **method 11 (MUIS / Majlis Ugama Islam Singapura, Shafi'i Asr)**, scoped to Singapore.
- Runs entirely as **one static HTML file** — no build step, no server, no framework. Open it in any browser or load it from a USB drive.
- Automatically walks through six display states around each prayer, with no input needed:
  1. **Idle** — live clock, Gregorian + Hijri date, all five prayer cards, next-prayer highlight
  2. **Pre-adhan** — countdown starting N minutes before adhan (default 3)
  3. **Adhan** — prayer-time announcement + the du'a after adhan
  4. **Iqamah countdown** — per-prayer configurable gap (default 9 min, Maghrib 4 min), with a rotating hadith
  5. **Saf alignment** — "Rapatkan Saf" prompt with a soft chime
  6. **Prayer (stealth)** — screen dims to near-black for 15 minutes so the TV doesn't distract worshippers, then returns to idle
- **Works offline after first load.** The fetched timetable and Hijri date are cached in `localStorage`; if the network is down, the display keeps running off the last cached data instead of breaking.
- Keeps the TV screen awake via the Screen Wake Lock API.
- Ships a hidden admin panel for on-site configuration — no code editing required.

## Getting started

1. Download `masjid-display.html` from this repo.
2. Open it in a browser on the TV (or any kiosk device), or load it via USB.
3. On first load with an internet connection, it fetches the day's timings automatically and caches them.
4. For a dedicated TV, set the browser to open this file on boot in kiosk/full-screen mode (most smart TVs and mini-PCs support this natively).

No installation, npm, or backend is required — it's a single self-contained file.

## Admin panel

Press **`D`** on a keyboard, or tap the invisible hotspot in the **top-left corner** of the screen, to open the settings panel. From there you can:

- Rename the mosque as shown on screen
- Set the iqamah gap (minutes after adhan) individually for each of the five prayers
- Set how many minutes before adhan the "prepare for prayer" screen appears
- Toggle the soft chime played at adhan and saf-alignment
- Force-refresh the day's prayer timings
- Use the **preview slider** or per-prayer **preview buttons** to fast-forward the display through every state for testing, without waiting for real prayer times

The panel auto-closes after 2 minutes of inactivity.

## Configuration reference

All settings are stored in the browser's `localStorage` (not in the file itself), so they persist across reloads but are specific to that device/browser.

| Setting | Default | Notes |
|---|---|---|
| Mosque name | `Masjid` | Shown top-left in idle mode |
| Minutes before adhan for pre-adhan screen | 3 | Applies to all prayers |
| Iqamah gap per prayer | 9 min (Maghrib: 4 min) | Minimum 2 min; iqamah is called at *adhan time + gap*, not from the end of the adhan screen |
| Chime | On | Synthesised tone (no audio file), plays at adhan and saf-alignment |

## Security & deployment notes

This app has no login, server, or user accounts — it's a static file, so most conventional "hacking" concerns (credential theft, server compromise) don't apply directly. The practical risks are at the device/network layer, not in this file:

- **Content-Security-Policy** is set in the page to restrict all network access to only itself, Google Fonts, and `api.aladhan.com` — even if the file were tampered with, injected scripts pointing elsewhere won't load.
- The API response is validated before use and only ever inserted as plain text, never as HTML, so a malformed or malicious API reply can't inject markup into the page.
- **What this app can't protect against:** who has physical or file-system access to the device running it. Treat the deployment like any other public kiosk:
  - Run it on a locked-down kiosk account/profile with the browser pinned to this file.
  - Disable OS-level remote access, USB autorun, and unnecessary services on the display device.
  - Keep the device on a segmented/guest network if possible, since it only ever needs outbound access to `api.aladhan.com` and Google Fonts.
  - Restrict who has physical access to the device serving the page (not just the TV).

## Attribution

Prayer time data provided by the [AlAdhan API](https://aladhan.com), calculation method 11 (MUIS). This project is not officially affiliated with or endorsed by MUIS.

## License

MIT — see [LICENSE](LICENSE).

# Roadmap

Plans for the next releases. Nothing here is built yet.

## 1.3.0 — Screens, kids & family

Ship as betas first (beta.1: Screens, beta.2: Kids & family) on our board and Colby's.

### Screens
- **Month arrows fade:** they show after a tap and fade after about 10 s when nobody's touching the screen (Tarin
  confirmed). A setting for each screen: Always / Fade / Never.
- **Tap the month name to pick a month:** a 12-month grid with a year switcher. This replaces the title tap's
  reload while editing (move the reload into ☰).
- **Swiping moves between pages:** Calendar → the screen's pinned pages, in the order they're pinned. It doesn't
  wrap around at the end, and chalk dots fade in to show where you are.
  - With nothing pinned it behaves as today (left = Chores).
  - View-only walls get their own page list, set in the console → 📺 Screens.
  - Swiping stays off in Draw / Decorate. The Notes switch, Split View and slideshow keep their own swipes (the
    slideshow already changes photos). Pages that scroll sideways take a swipe only from their edges.
  - Every page goes back to the calendar after a few quiet minutes, the time set for each screen.
  - Optional, off by default: swipe up or down to change the month.
- **Off hours (replaces the overnight slideshow):**
  - On update, the current overnight window becomes window #1 with mode Slideshow, keeping its brightness and speed.
  - Several windows, each on chosen days (for example weekdays 9–3).
  - Each window has a mode: Slideshow · Night clock · Dimmed calendar · Black · Screen off.
  - A board-wide default, overridden for each screen and editable in the console, the same way as the Notes column.
  - A "🛏 Bedtime" preset for kids' tablets.
  - While black or off, the board's work pauses (no updates, no GIFs, no photo checks).
  - The existing double tap for the photo frame stays.
- **Screen off, tried in this order:**
  1. Fully Kiosk: `fully.turnScreenOff()` / `turnScreenOn()`.
  2. FreeKiosk: its control API, if it has screen on/off (check), else through Home Assistant.
  3. A Home Assistant action at the start and end of a window (FreeKiosk or Fully integration, a TV, a smart plug).
  4. Chrome kiosk: stop asking to keep the screen awake, so the device's own screen sleep turns it off.
     - Change `docs/kiosk.md` (lines 72 and 83): set the device's screen sleep to 1–2 minutes instead of never.
     - Chrome only keeps a screen awake on HTTPS or `localhost`. Warn when it isn't possible.
  5. Anything else: black.
- **Night clock:** a big chalk clock, optionally with tomorrow's first event.
- **"Good morning" card:** the first tap after off hours shows the weather, today's events and the first routine
  for about 30 s, then the calendar.
- **A console line for each screen:** how it turns its screen off, whether keeping it awake worked, and which off-hours
  window it's in now.
- Optional: wake on motion through Home Assistant or Fully Kiosk.

### Kids & family
- **Morning and bedtime routines on kids' tablets:** steps in order with times and picture icons.
- **Picture chores for kids who can't read yet:** use the existing picture library.
- **Finished routines and chores add marbles to jars automatically.** Streaks too ("5 mornings in a row ⭐").
- **A kid's "My day" page:** just their events, extra large.
- **"How many sleeps" countdown** in big, simple text.
- **Countdowns between activities:** "10 minutes until we leave", as a shrinking chalk timer built on the existing
  timers.
- **Big-day mode:** for any starred event, and birthdays automatically. A border, a giant name, confetti, the
  person's photos and a "🎂 Today" Notes page.
- **Chalk notes from a phone:** written or drawn on a phone, shown on the wall, wiped after a day.

### Design, accessibility & sharpness (do the checks first; new 1.3.0 screens follow the results)
- **How sharp is it really (the low-resolution worry):** the board is laid out at a fixed 1252 × 692 and enlarged
  to fit (1.2.1), so a 1080p screen shows it at 1.5× and a 4K screen at 3×.
  - Take screenshots at 1080p, 1440p, 4K and on the Apolosign, and list everything that looks soft.
  - Likely soft spots to check: clipart, chalk drawings and emoji that are pictures rather than drawn shapes,
    photo and polaroid sizes, the background textures, and drawings saved as pictures rather than as strokes.
  - Fixes: shapes drawn as SVG where possible, a sharper copy of each picture for each screen size, and photos
    fetched at the size the screen shows them.
  - Consider letting big screens show more room (a bigger layout) instead of just enlarging the same one.
- **Readable from across the room:** a minimum text size at wall viewing distance, and an optional
  "Bigger writing" for each screen.
- **Contrast:** check every chalk and event color against every background (whiteboard and kraft paper especially).
  Add a check script; add a "clear chalk" high-contrast look.
- **Not color alone:** each calendar gets a small mark or pattern as well as its color.
- **Calmer motion:** follow the screen's "reduce motion" setting (confetti, GIFs, fades, the new arrow fade).
- **Tablets, the console and phones:** buttons at least 44 px, labels for screen readers, keyboard focus that
  shows, and an easy-to-read font option.
- **Board styles, from Tarin's real board** (mockups: https://claude.ai/artifact/ELWuftRLPTaEZ5muo7zQEJ):
  - Chalk marker (her look), Stick chalk, or Clean (today's).
  - Grid: Printed (thin cream lines, numbers in small boxes) or Hand-drawn.
  - A look for each picture: As is · Chalk marker · Marker + white outline · Colored outline · Stick chalk.
    Pictures are colored in once with short strokes and kept.
  - Lettering with an outline in a second color, or a white outline like a sticker, with marker streaks.
  - Ideas from her board: a dashed-line pen for flight paths, and doodle flowers and bees as pictures.
- **Look even better:** a design review of the board, with more realistic chalk (slightly hand-drawn grid lines,
  eraser smudges, dust at the edges), one icon style (chalk icons instead of mixed emoji in the controls), and
  clearer spacing and sizes for titles, days and events.

### Fast and sharp on any screen ("it's just a website")
- **The chalk look, done cheaply** (mockups: one grain for the whole board, clipart redrawn once): nothing is
  recalculated while the board shows. The grain tile and each redrawn picture are made once at the screen's real
  resolution and kept. Live SVG filters are not used.
- **A speed budget, checked in CI:** a size limit for the wall's code, the time to first show the board on a
  Raspberry Pi 4, and close to 0% processor use while nothing changes.
- **Walls don't load the editor:** the build has no code splitting today, so a view-only wall downloads the
  drawing tools and the picture library too. Split them out.
- **A week-long soak test:** run a wall with a faked clock for days and check that memory and the page's size stay
  flat. Screens stay up for weeks, and the stray-dots bugs showed that long uptime finds problems.
- **Test in more browsers:** Playwright runs only Chromium. Add Firefox and WebKit (Safari), matching the build's
  targets (`chrome100`, `safari15`, `firefox100`).
- **Pause when nobody's looking:** in off hours, while hidden, or with the screen off, stop drawing, GIFs, weather
  motion and checking for photos. Check every animation loop and timer (there are 17 to go through).
- **Pictures sized for the screen:** photos and polaroids fetched at the size shown (WebP), cached for good on
  the screen.
- **A `?perf` overlay** like the other `?` test switches: frame rate, memory, and what's being drawn.
- **Looking after the screen:**
  - Burn-in protection: move everything by a pixel or two now and then.
  - Reload the page if it stops getting updates, and a quiet nightly reload during off hours.
- **Security:** the board page's security rules still allow inline scripts (`script-src 'unsafe-inline'`,
  `access.ts:135`). See whether that can be removed as the other pages did.

### Alongside the release
- **A demo that runs in the browser** with sample data and no server. It needs a home on Josh's site (still to
  decide: what the site is built on and its domain).
- **Docs on the site,** with a "Try it" button.
- **Screenshots taken from the demo by a Playwright script,** re-run each release to refresh `docs/images`.
- **For the repo:** a description, topics (self-hosted, family-calendar, wall-calendar, kiosk, home-assistant,
  immich, skylight-alternative, dakboard-alternative) and a social preview image. No rename.

### For every feature
- Spanish words (`shared/i18n-es.ts`).
- Test on older wall browsers (older than Chrome 105, as for 1.2.3), the Apolosign and Firefox.
- Playwright tests: the swipe rules, and off-hours switching with a faked clock.
- Works in the Home Assistant add-on.
- User guide, changelog, screenshots.
- Existing boards don't change after updating unless someone turns a feature on. The exception is the arrows
  fading on family screens.

## 1.4.0 — Photos (with Colby)

- **Several photo sources:** Immich albums, Immich people, favorites and searches, plus iCloud and Google shared
  albums. Only the albums the admin approves ever show.
- **📷 Albums page:** in the page list, shown only when there are two or more albums. Album covers as polaroids;
  tap one to play it and swipe to change photos.
- **Different sources for the polaroids and the slideshow.**
- **An off-hours Slideshow window can pick its album.**
- **Albums for a season or month** (for example, a Christmas album in December).
- **Big-day mode uses the person's Immich photos.**
- **Later:** Immich Memories, and videos or Live Photos.
- **When it's out:** post on r/selfhosted and r/homeassistant and in the Home Assistant forum, and ask to be listed in
  awesome-selfhosted and on Immich's community projects page, with the demo link.

## Later
- Home Assistant alerts on the board (doorbell snapshot, garage, laundry) and who's home.
- "Dinner tonight" shown big from the meal plan.
- School lunch menu or bus times on each day.
- A day, 3-day or agenda view for small screens.
- Brightness that follows the time of day; sunrise, sunset and moon phase with the weather.
- Sharing a month's decorations or a border set between families.
- A "compared with Skylight / DAKboard" page.

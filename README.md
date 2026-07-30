# Judder support

The support and privacy page for [Judder](https://apps.apple.com/app/id6796445047), a haptic
pattern editor for iPhone. One static page, served by GitHub Pages, with no build step and no
dependencies.

It exists because the App Store requires a reachable Support URL and a Privacy Policy URL, and
because two questions account for nearly all of Judder's support load. Both are answered on the
page rather than by email: the Simulator has no Taptic Engine, and iOS silences every app's
haptics when Settings, Accessibility, Touch, Vibration is off.

## What goes in App Store Connect

| Field | Value |
| --- | --- |
| Support URL | `https://craigtrim.github.io/ios-judder-support/` |
| Privacy Policy URL | `https://craigtrim.github.io/ios-judder-support/#privacy` |
| Marketing URL | leave blank |

Both URLs can be changed at any time without shipping a new build, so a custom domain can
replace them later without touching the app.

## Publishing

Settings, then Pages, then set Source to `main` and the folder to `/ (root)`. The page is live a
minute or so later.

To move it to a custom domain, add a `CNAME` file containing the bare hostname and point the
domain's DNS at GitHub Pages.

## Editing

`index.html` is self-contained: the CSS is inline and there are no fonts, scripts or images to
fetch. It renders in light and dark according to the reader's system setting. Open it in a
browser to check a change, since there is nothing to compile.

Anything asserted here about privacy has to stay true of the shipped app. The claims are that
Judder has no network code, no analytics, no accounts, no crash reporting and no persistence.
If any of that changes in the app, this page changes in the same release, and the App Privacy
answers in App Store Connect change with it.

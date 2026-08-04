# judder-haptics.com

The public site for Judder, a haptic pattern editor for iPhone. Static files, no build step and
no dependencies.

| Path | Serves | What it is |
| --- | --- | --- |
| `html/index.html` | `/` | The landing page. Explains the app, cites its sources, and embeds a live copy of the web prototype. |
| `html/support.html` | `/support.html` | Support and privacy, with the privacy section anchored at `#privacy`. |
| `html/app/index.html` | `/app/index.html` | The web prototype of the editor, loaded by the landing page in an iframe. Not linked directly. |

The support page exists because the App Store requires a reachable Support URL and a Privacy
Policy URL, and because two questions account for nearly all of Judder's support load. Both are
answered on the page rather than by email: the Simulator has no Taptic Engine, and iOS silences
every app's haptics when Settings, Accessibility, Touch, Vibration is off.

## What goes in App Store Connect

| Field | Value |
| --- | --- |
| Support URL | `https://judder-haptics.com/support.html` |
| Privacy Policy URL | `https://judder-haptics.com/support.html#privacy` |
| Marketing URL | `https://judder-haptics.com/` |

All three can be changed at any time without shipping a new build.

## Publishing

Every push to `master` deploys `html/` through
[`.github/workflows/pages.yml`](.github/workflows/pages.yml). The workflow uploads the directory
as it sits in the repository, so what is committed is what is served.

It is an Actions deployment rather than the older deploy-from-a-branch setting because that
setting can only publish the repository root or `/docs`, and the site lives in `html/`.

`html/CNAME` holds the custom domain. It has to stay inside the published directory: with an
Actions deployment GitHub reads the custom domain from the artifact, so losing the file drops the
domain off the site.

### DNS

The domain is registered through Cloudflare and the zone is served by Cloudflare nameservers, so
the records are added there. GitHub Pages needs four `A` records and four `AAAA` records on the
apex, plus a `CNAME` on `www` pointing at `craigtrim.github.io`. Leave them unproxied (grey cloud)
until GitHub has issued the certificate and Enforce HTTPS is available, since that check has to
reach GitHub rather than Cloudflare's edge.

Cloudflare Email Routing already publishes MX for the domain. Mail is independent of the site and
is not affected by any of this.

## Editing

Both pages are self-contained: the CSS is inline and there are no fonts, scripts or images to
fetch. `html/index.html` renders dark and carries its own resolver in inline JavaScript, mirroring
`HapticTimeline.resolve` so the numbers on the page match the app. `html/support.html` follows the
reader's system setting for light or dark. Open either in a browser to check a change, since there
is nothing to compile.

To preview the site the way it will actually be served, run a static server from `html/` instead
of opening the files directly, because the landing page loads the prototype from a relative path:

```bash
cd html && python3 -m http.server 8000
```

Anything asserted on the support page about privacy has to stay true of the shipped app. The
claims are narrow on purpose: no network code, no analytics, no accounts, no tracking, and
patterns that stay on the device.

Note what is deliberately **not** claimed. The page does not say the app stores nothing, because
on-device persistence is planned before the first release and that claim would go stale. Storing
data locally is not collection under Apple's definition, so "Data Not Collected" survives it.
What would break the page is anything that transmits: an analytics SDK, a crash reporter, a sync
feature, or an account. If one of those ever lands, this page changes in the same release and so
do the App Privacy answers in App Store Connect.

# judder-haptics.com

The public site for Judder, a haptic pattern editor for iPhone. Static files, no build step and
no dependencies.

| Path | Serves | What it is |
| --- | --- | --- |
| `html/index.html` | `/` | The landing page. Explains the app, cites its sources, and embeds a live copy of the web prototype. |
| `html/support.html` | `/support.html` | Common questions, requirements, and the contact address. |
| `html/privacy.html` | `/privacy.html` | The privacy policy. |
| `html/app/index.html` | `/app/index.html` | The web prototype of the editor, loaded by the landing page in an iframe. Not linked directly. |
| `html/favicon.svg` | `/favicon.svg` | The app mark. Same bar heights and corner radius as the nav glyph, with the bars cut thicker. |
| `html/favicon.ico` | `/favicon.ico` | 16, 32 and 48 px fallback for browsers that will not take an SVG icon. |
| `html/apple-touch-icon.png` | `/apple-touch-icon.png` | 180 px, full bleed. iOS applies its own corner mask, so this one is not pre-rounded. |

The support and privacy pages exist because the App Store requires a reachable Support URL and a
Privacy Policy URL. Two questions account for nearly all of Judder's support load, and both are
answered on the support page rather than by email: the Simulator has no Taptic Engine, and iOS
silences every app's haptics when Settings, Accessibility, Touch, Vibration is off.

## What goes in App Store Connect

| Field | Value |
| --- | --- |
| Support URL | `https://judder-haptics.com/support.html` |
| Privacy Policy URL | `https://judder-haptics.com/privacy.html` |
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

Every page is self-contained: the CSS is inline and there are no fonts or scripts to fetch. The
only files a page pulls are the three icons, and they are the reason the nav mark is duplicated as
`favicon.svg` rather than referenced: a favicon cannot be an inline SVG. Its bars are cut thicker
than the nav glyph's because the nav's 44-unit minor bars land on 0.69 px at a 16 px favicon and
smear into the amber. Bar heights and the corner radius are the nav glyph's exactly, so the two
read as one mark at every size above about 20 px.

All three pages share one palette, taken from the app itself. Amber is haptic energy, blue is the
audio bed, and everything else is an iOS dark system surface. A third accent colour is a
regression. `html/index.html` also carries its own resolver in inline JavaScript, mirroring
`HapticTimeline.resolve` so the numbers on the page match the app.

The sub-pages duplicate the shared tokens and the nav rather than importing them, which keeps each
file openable on its own with no build step. Changing the palette means changing it in three
places.

One layout trap is worth knowing before editing the bullet lists. Each row is a flex box holding
exactly two children, the bullet and one `<span>`. Putting bare text next to a `<b>` inside the row
makes each text node its own anonymous box, and the row gap then pushes the words apart.

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

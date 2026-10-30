# joomiz.github.io

The published files of www.joomiz.com: the website, the documentation and the design system. GitHub Pages serves this repository with the custom domain in `CNAME`. Readers reach it only at https://www.joomiz.com.

**Most of this repository is built output.** The source is in the private `platform` repository. Its workflow `.github/workflows/publish-site.yml` builds the sites, and a bot commits the result here on every change to `main`. Do not edit a built file by hand: the next publish replaces it.

## What is built, and what is kept by hand

| Path | What it is | Source |
|---|---|---|
| The files that `_www-manifest.txt` lists: `index.html`, `use-cases/`, `faq.html`, `css/`, `assets/`, `og-image.png` | The website: the home page, the use cases and the FAQ | `web/sites/www` in `platform` |
| `_www-manifest.txt` | The list of the files that the website owns | Written by the publish |
| `docs/` | The documentation | `docs/` and `web/sites/docs` in `platform` |
| `design-system/` | The design system | `web/design-system` and `web/sites/design-system` in `platform` |
| `CNAME` | The custom domain | Kept by hand |
| `favicon.ico`, `favicon.svg` | The icon that browsers ask for at the root | Kept by hand |
| `_src/` | The source of the image for shared links (not published) | Kept by hand |
| `README.md` | This file | Kept by hand |

The publish replaces `docs/` and `design-system/` as whole folders. At the root it replaces only the files that `_www-manifest.txt` lists, and it removes a file that it made before and no longer makes. It never touches `CNAME`, `docs/`, `design-system/`, `_src/` or a file that the manifest does not list. ADR 0018 in `platform` explains this.

## Change the website

Change the source in `platform`, not the files here. The rules of the pages are tests there:

- The home page stays under 10,000 bytes gzipped, with its CSS and script inside. Then it fits in the first flight of packets, so it loads in one round trip.
- No page loads a font, an image, an analytics script or anything from another host.
- The one call to action is a `mailto:` link to `sales@joomiz.com`, with a button that copies the address. Every page works without JavaScript.
- The pages follow the light or dark setting of the device.

To see the website, the docs and the design system together before a merge, run `npm run preview` in `web/` of `platform`.

## Cloudflare

Cloudflare serves the site. Its *Email Address Obfuscation* feature rewrites every e-mail address in the HTML. It turns the address into `[email protected]`, changes the links, and adds a script. That breaks the page for a reader without JavaScript and adds a second request.

Each page of the website has the two comments `<!--email_off-->` and `<!--/email_off-->` around its body. They tell Cloudflare to leave the page alone, and Cloudflare removes them from what it serves. The build keeps them, and a test in `platform` checks them. The address is public on purpose: it is the call to action, so hiding it would only lose messages.

After a publish, check that Cloudflare left the address alone:

```sh
curl -s -H 'Accept-Encoding: identity' https://www.joomiz.com/ | grep -c -E 'cdn-cgi|email-protection'   # must be 0
```

## The image for shared links

When someone shares a link on WhatsApp, LinkedIn, X or another service, the service fetches `og-image.png` (1200 × 630 px) and shows it with the title. The pages name it with its full address, in their `og:` tags, because those services do not accept a relative one.

The published PNG comes from `web/sites/www/static/og-image.png` in `platform`. Its source is `_src/og-image.html` here. To make the PNG again after a change to the source or to the wordmark in `_src/`:

```sh
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=1 --window-size=1200,630 --screenshot=og-image.png "file://$PWD/_src/og-image.html"
```

Then copy the new PNG to `web/sites/www/static/og-image.png` in `platform`. The next publish puts it here.

Keep the file under 300 KB, because WhatsApp ignores a larger image. A service keeps what it fetched, so after a change use its own tool to fetch it again (the Facebook Sharing Debugger and the LinkedIn Post Inspector do that). WhatsApp has no such tool, and it forgets after a while.

## The favicons

- `favicon.svg` is the icon at the root.
- `favicon.ico` is for old browsers, which ask for it by default. It is made from `favicon.svg`, so the two match: four sizes (16, 32, 48 and 64 px) with transparent corners. Make it again from the SVG whenever the SVG changes.

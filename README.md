# joomiz.github.io

The Joomiz home page. GitHub Pages serves it at https://www.joomiz.com (see `CNAME`).

## The rules of the page

- **One file.** `index.html` holds the HTML, the CSS, the script and the logo.
- **One round trip.** The compressed response, headers included, fits in the first flight that a server can send before it waits for the browser: about 10 packets, which is 12,000 bytes. The headers take about 1,100 bytes, so the compressed file stays **under 10,000 bytes**. Today Cloudflare sends about 5,000 bytes of Brotli, which is 6,000 bytes with the headers. The raw file has no limit of its own. Keep it small anyway, because every byte still costs the browser some work.
- **One external resource.** The page loads only `favicon.svg`. It has no fonts, no images, no analytics and no third-party script.
- **One call to action.** A `mailto:` link to `sales@joomiz.com`, with a button that copies the address. The page works without JavaScript. Then the link and the visible address are all there is, and the copy button stays hidden.
- **Light and dark.** The page follows the setting of the device (`prefers-color-scheme`).

## Where the look comes from

The colours, the type and the spacing come from the Joomiz design system in the `platform` repository (`design-system/`) and from the brand guide (`docs/branding.md`). They are copied into the page, not linked. Only what the page uses is copied.

- The fonts are the system fonts. The brand guide has not chosen a body typeface.
- The logo is the full Joomiz wordmark from the brand guide, with the redundant points of its paths removed (about 1.9 KB instead of 18 KB). Check any change to it against `docs/images/branding/wordmark-full.on-light.svg` in the `platform` repository.

## Cloudflare

Cloudflare serves the page. Its *Email Address Obfuscation* feature rewrites every e-mail address in the HTML. It turns the address into `[email protected]`, changes the links, and adds a script. That breaks the page for a visitor without JavaScript, adds a second request, and makes the page larger than this file.

The two comments, `<!--email_off-->` after `<body>` and `<!--/email_off-->` before `</body>`, tell Cloudflare to leave the page alone. Keep them. Cloudflare removes them from what it serves. The address is public on purpose: it is the call to action, so hiding it would only lose messages.

## Check a change

```sh
gzip -9 -c index.html | wc -c                     # must be under 10000
grep -oE '(src|href)="[^"]*"' index.html          # only favicon.svg, the canonical URL and mailto links
grep -c 'email_off' index.html                    # must be 2
```

Open `index.html` in a browser. There is nothing to build.

After a merge, compare the live page with the file. The two must be the same, except for the two comments:

```sh
curl -s -H 'Accept-Encoding: identity' https://www.joomiz.com/ | grep -c -E 'cdn-cgi|email-protection'   # must be 0
```

## The image for shared links

When someone shares the link on WhatsApp, LinkedIn, X or another service, it fetches `og-image.png` (1200 × 630 px) and shows it with the title. The page names it in its `<meta property="og:image">` tags, with the full address, because those services do not accept a relative one. `twitter:card` is `summary_large_image`, and X takes the rest from the `og:` tags. The page itself never loads the image, so it does not count against the rules above.

The source is `_src/og-image.html`. A folder that starts with an underscore is not published. To make the PNG again, after a change to the source or to the wordmark in `_src/`:

```sh
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=1 --window-size=1200,630 --screenshot=og-image.png "file://$PWD/_src/og-image.html"
```

Keep the file under 300 KB, because WhatsApp ignores a larger image. A service keeps what it fetched, so after a change use its own tool to fetch it again (the Facebook Sharing Debugger and the LinkedIn Post Inspector do that). WhatsApp has no such tool, and it forgets after a while.

## Other files

- `favicon.svg` is the favicon that the page links.
- `favicon.ico` is for old browsers, which ask for it by default. It is made from `favicon.svg`, so the two match: four sizes (16, 32, 48 and 64 px) with transparent corners, 2.4 KB. Make it again from the SVG whenever the SVG changes.
- `og-image.png` is the image for shared links. See above.
- `_src/` holds the source of that image. It is not published.
- `design-system/` is **built output**, published from the `platform` repository by a workflow (`.github/workflows/publish-site.yml` there), and a bot commits it here. Do not edit it by hand: the next publish replaces it.

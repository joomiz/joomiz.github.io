# joomiz.github.io

The Joomiz home page. GitHub Pages serves it at https://www.joomiz.com (see `CNAME`).

## The rules of the page

- **One file.** `index.html` holds the HTML, the CSS, the script and the logo. It stays under **14,000 bytes**, uncompressed.
- **One external resource.** The page loads only `favicon.svg`. It has no fonts, no images, no analytics and no third-party script.
- **One call to action.** A `mailto:` link to `sales@joomiz.com`, with a button that copies the address. The page works without JavaScript. Then the link and the visible address are all there is, and the copy button stays hidden.
- **Light and dark.** The page follows the setting of the device (`prefers-color-scheme`).

## Where the look comes from

The colours, the type and the spacing come from the Joomiz design system in the `platform` repository (`design-system/`) and from the brand guide (`docs/branding.md`). They are copied into the page, not linked. Only what the page uses is copied.

- The fonts are the system fonts. The brand guide has not chosen a body typeface.
- The logo is the Joomiz mark, with the redundant points of its path removed (about 1 KB instead of 2.3 KB). The full wordmark is 18 KB, which is more than the page can afford.

## Check a change

```sh
wc -c index.html                                  # must be under 14000
grep -oE '(src|href)="[^"]*"' index.html          # only favicon.svg, the canonical URL and mailto links
```

Open `index.html` in a browser. There is nothing to build.

## Other files

- `favicon.svg` is the favicon that the page links.
- `favicon.ico` is for old browsers, which ask for it by default.
- `hello-world.png` is a leftover from the first page. Nothing uses it.

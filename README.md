# Unmark

A small page that takes a photo, throws away everything hidden inside it, trims a couple of pixels off the edges, and (if you ask) squeezes it under a file size. It's one HTML file. No build step, no server, nothing to install.

![icon](icon-512.png)

Live: https://tryunmark.netlify.app/

## Why this exists

Government and visa upload forms love to reject photos for reasons they don't explain. Too big, wrong shape, or the file carries a pile of data you never meant to share: the GPS spot where you took it, the phone model, a timestamp. Most "free online" tools solve this by asking you to upload the picture to their server, which is a funny way to protect your privacy. So I made one that never leaves your browser.

## What it does

- **Strips all metadata.** EXIF, GPS, XMP, IPTC, ICC profile, text chunks, comments. The image is redrawn onto a blank canvas and saved again, so nothing from the original file can come along.
- **Shows you what it found.** Before cleaning it reads the file and lists what's inside (and how many KB that is). After cleaning it scans the result again so you can see it say 0 B.
- **Renames the file** to `Photograph` by default. You can change the name. The extension stays whatever you uploaded (`.jpg` stays `.jpg`).
- **Trims 1 or 2 px off every edge.** The height is trimmed in proportion so the aspect ratio doesn't change, and by default the result is scaled back to the original pixel size.
- **Crops to a passport ratio**: 35×45 or 51×51 (2×2 in). A frame on the preview shows what will be cut.
- **Hits a file size.** Type a number of KB and it finds the best quality that fits.

Several photos at once works. Paste from the clipboard works. `Ctrl/Cmd + Enter` runs it.

## About the passport sizes

I went looking for the current Indian passport photo size and the sources don't agree. A number of 2026 guides say 35×45 mm is the print size now. Others, and plenty of e-services and visa portals, still ask for 51×51 mm (2×2 in). So both are in there. Read what your own form says and pick that one. I'm not going to pretend I know which one your portal wants.

## How the size target works

Browsers can't say "make this JPEG exactly 200 KB", so the page searches for it:

1. Encode at a few qualities and binary-search for the highest one that still fits.
2. Never go below 75% quality. If it still doesn't fit, make the image slightly smaller and search again.
3. Once something fits, nudge the size back up to find the largest dimensions that still fit.

PNG ignores the quality setting entirely, so for PNG the only lever is the pixel size. If you don't set a target, JPEG and WebP are saved at up to 95% quality but never larger than the file you started with.

None of this is lossless. A file can't get smaller without losing something. The goal is to lose as little as possible and tell you what happened (the result row shows the quality used and any resize).

## Things that don't work, or work badly

- HEIC (iPhone's default in some modes) and AVIF often can't be read by the browser. Convert to JPEG first.
- The browser's built-in JPEG encoder is okay, not great. A tool using MozJPEG would produce files 10–30% smaller at the same quality. I couldn't use it here because it needs to fetch a separate WebAssembly file.
- Safari can't encode WebP from a canvas. If you give it a WebP you'll get an error instead of a wrongly named file.
- iOS Safari caps canvas size at about 16.7 megapixels, so very large photos are scaled down to fit.
- Everything runs on the main thread, so a big batch may make the tab feel stuck for a moment.
- I have not tested it in every browser. Chrome and Safari were my targets.

## Privacy

Your photos are never uploaded. The page makes one network request: the stylesheet for the Geist fonts from Google Fonts. If that bothers you, download the fonts, host them next to `index.html`, and delete the three `<link>` tags near the top.

## Running it

Open `index.html` in a browser. That's it.

To put it online with GitHub Pages: push this folder to a repo, go to Settings → Pages, choose the `main` branch and the root folder, save. The `.nojekyll` file is there so GitHub doesn't try to process anything.

## What's in the folder

```
index.html            the whole app (HTML, CSS, JS)
icon.svg              favicon
favicon.ico           fallback for old browsers
apple-touch-icon.png  iOS home screen icon
icon-512.png          big version of the icon
LICENSE               MIT
```

## License

MIT. Do what you like with it.

Made by [Hazrat Mosaddique Ali](https://github.com/mosaddiqdev).

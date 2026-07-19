# Publishing Checklist

Create a folder under `content/posts/<section>/<slug>/` with an `index.md` and
its images, then:

## 1. Prep images

| Asset | Rule |
|---|---|
| Diagrams / screenshots > ~150 KB | Convert to WebP: `cwebp -q 85 img.png -o img.webp` |
| Photos (JPG) | Convert to WebP: `cwebp -q 80 photo.jpg -o photo.webp` |
| Small images (< ~150 KB) | Leave as-is |
| draw.io diagrams | Prefer exporting SVG — no conversion needed |
| Screen recordings | Never GIF. Use mp4/webm and embed with `<video autoplay loop muted playsinline>` |

- Keep images ≤ 1600px wide.
- Reference the converted filename (`.webp`) in both the `cover.image` front
  matter and any `<img src>` in the body.

## 2. Cover front matter

Omit `responsiveImages: false` from the `cover:` block so Hugo generates
mobile-sized variants automatically.

## 3. Build and deploy

From the repo root:

```bash
hugo --cleanDestinationDir
```

Never commit output from `hugo server` (it bakes `localhost:1313` into every
URL). Spot-check that `docs/sitemap.xml` contains `https://www.avni.sh/` URLs,
then commit and merge to `gh-pages` as usual.

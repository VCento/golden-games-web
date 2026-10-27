# golden-games-web

One-page static landing site for **Golden Games**, a free Android app with 5 classic games
(Solitario, Mahjong, Crucigrama, Memoria, Sopa de Letras) for adults 55+.
Spanish (neutral Latin American), mobile-first, plain HTML + CSS.

- Play listing: https://play.google.com/store/apps/details?id=com.centomatic.goldengames
- Preview URL (GitHub Pages): https://vcento.github.io/golden-games-web/
- Final domain (pending): https://goldengamesapp.com/

Privacy by design, matching the app's Data Safety ("no data collected"): no cookies, no
localStorage/sessionStorage, no analytics, no pixels, no third-party scripts, no external fonts,
no external requests at page load. A Content-Security-Policy meta tag enforces this
(`default-src 'none'`, only same-origin images and CSS, and the one inline script pinned by hash).

## File map

| Path | What |
|---|---|
| `index.html` | The landing page. Domain-switch comment block at the top. |
| `404.html` | Self-contained "not found" page (inline CSS, no assets, absolute home link). |
| `assets/styles.css` | All styles. Palette "Casino Elegante" only. |
| `assets/golden-games-logo.svg` | App logo (copied from `golden-games/app/assets/`). |
| `assets/google-play-badge-es-419.png` | Official Google Play badge, Spanish (Latin America). |
| `assets/og-image.jpg` | 1200×630 Open Graph / WhatsApp card (generated from the app icon). |
| `assets/favicon-32.png`, `assets/icon-192.png`, `assets/apple-touch-icon.png` | Icons resized from `golden-games/app/assets/images/icon.png`. |
| `assets/screenshots/*.jpg` | Real app screenshots for the "Así se ve en tu teléfono" section (see below). |
| `.nojekyll` | Tells GitHub Pages to serve files as-is (no Jekyll build). |
| `.gitattributes` | Forces LF line endings (the CSP script hash depends on exact bytes). |
| `.gitignore` | Ignores `assets/screenshots/originales/` (raw screenshot drops). |

All internal paths are relative, so the site works under `/golden-games-web/` (github.io preview)
and at a domain root without changes.

## Local preview

From the parent folder of this repo run `py -m http.server 8765` and open
http://localhost:8765/golden-games-web/ (mimics the github.io subpath).
Note: Python's server does not serve `404.html` for missing paths; GitHub Pages does.

## Deploy (GitHub Pages)

1. Create the repo `VCento/golden-games-web` and push `main`.
2. Repo **Settings → Pages → Build and deployment**: Source "Deploy from a branch",
   Branch `main`, folder `/ (root)`. Save.
3. Wait for the Pages build, then check https://vcento.github.io/golden-games-web/.

## Connect the domain goldengamesapp.com

1. Add a file named `CNAME` at the repo root containing exactly one line:
   `goldengamesapp.com`
   (or set it in **Settings → Pages → Custom domain**, which commits the same file).
2. At the DNS provider:
   - Apex `goldengamesapp.com`, four **A** records:
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - `www` **CNAME** → `vcento.github.io`
3. Verify the domain for the account: GitHub **Settings (profile) → Pages → Add a domain**,
   add the TXT record GitHub shows, then click Verify. This protects against domain takeover.
4. Back in the repo **Settings → Pages**, wait for the DNS check to pass, then tick
   **Enforce HTTPS** (it can take a while for the certificate to be issued).
5. Apply the domain switch list below and push.

## Domain switch list

Replace `https://vcento.github.io/golden-games-web/` with `https://goldengamesapp.com/` in:

| File | Line / element |
|---|---|
| `index.html` | `<link rel="canonical" href="...">` |
| `index.html` | `<meta property="og:url" content="...">` |
| `index.html` | `<meta property="og:image" content=".../assets/og-image.jpg">` |
| `404.html` | `<a href="...">Volver al inicio</a>` |

After switching, re-scrape the card so WhatsApp/Facebook drop the old cache:
https://developers.facebook.com/tools/debug/ (paste the new URL, "Scrape again").

## Google Play badge

- File: `assets/google-play-badge-es-419.png` (646×250 PNG, 18,145 bytes). The PNG has no side padding and
  29px top/bottom (about 11px at the rendered 250px width), so the page layout adds the rest of the
  clear space (`.store` gap 14px in `styles.css`).
- Source URL (downloaded 2026-10-26):
  https://play.google.com/intl/es-419/badges/static/images/badges/es-419_badge_web_generic.png
- The old badge page `https://play.google.com/intl/es-419/badges/` now redirects to Google's
  Partner Marketing Hub (https://partnermarketinghub.withgoogle.com/brands/google-play/google-play/lockups-icons-badges/#badges).
  Rules from that page: badge height at least 28px on screen, clear space of one quarter of the
  badge height, do not modify/recolor the badge. On the page it renders 250px wide (about 97px tall).
- The es-419 badge artwork reads **"DESCARGAR EN Google Play"**.
- Trademark line used in the footer: "Google Play, el logotipo de Google Play y Android son marcas de Google LLC."

## Store link and UTM tracking

Every Play link on the page (hero badge and bottom badge, both marked `data-play-link`) uses:

```
https://play.google.com/store/apps/details?id=com.centomatic.goldengames&utm_source=landing&utm_medium=web&utm_campaign=sitio&referrer=utm_source%3Dlanding%26utm_medium%3Dweb%26utm_campaign%3Dsitio
```

**Both formats in one link** (decision 2026-10-27): plain `utm_*` parameters on the store URL
**and** the same tags URL-encoded inside `referrer=`. Reason: Play Console Help does not say which
one "Tracked channels (UTM)" reads (checked 2026-10-27):

- https://support.google.com/googleplay/android-developer/answer/9859173 defines UTM source / UTM
  campaign as "the value of the utm_source URL parameter in deep links to your store listing"
  (same for utm_campaign). No example URL, no mention of `referrer=`.
- https://support.google.com/googleplay/android-developer/answer/6263332 defines "Tracked channels
  (UTM)" as users who reached the listing from a UTM-tagged link. No format given.
- The only Google example of a tagged Play link uses the encoded referrer (`referrer` string
  URL-encoded, 512 characters or less):
  https://developer.android.com/games/playgames/user-acquisition

Once real visits show up in Play Console > Store listing acquisition > Tracked channels, check
which format was read and drop the other if wanted.

Notes:
- Play Console reports only **utm_source** and **utm_campaign**. utm_medium and utm_content are
  carried along but do not show in the Console report.
- The app does not read the install referrer (it collects no data); `referrer=` is only there for
  Play's attribution.

**Pass-through:** if the page URL carries `utm_source`, `utm_medium`, `utm_campaign` or
`utm_content`, the inline script removes all default UTM tags from every Play link, sets only the
incoming ones (values trimmed, max 60 characters, cut by code point so emoji never break) in both
formats, and replaces `referrer=` with
the same tags. Replacing the whole set keeps ad traffic from being mixed with the landing defaults
in Play Console. Ads must therefore always carry at least `utm_source` and `utm_campaign`.
Play caps the referrer string at 512 characters: if the encoded tags exceed it, the script drops
`utm_content`, then `utm_medium` from `referrer=` (the plain `utm_*` tags stay complete).
Nothing is stored. Examples:

- no UTM in the page URL → `...&utm_source=landing&utm_medium=web&utm_campaign=sitio&referrer=utm_source%3Dlanding%26utm_medium%3Dweb%26utm_campaign%3Dsitio`
- `/?utm_source=meta&utm_campaign=test55` → `...&referrer=utm_source%3Dmeta%26utm_campaign%3Dtest55&utm_source=meta&utm_campaign=test55`
- `/?utm_source=meta` → `...&referrer=utm_source%3Dmeta&utm_source=meta` (campaign shows as not set in the Console)

## Content Security Policy

`index.html` has a CSP meta tag whose `script-src` holds the sha256 of the inline script.
If you edit that script, recompute the hash and paste it into the meta tag, or the UTM
pass-through stops running (the default tagged link still works). One way:

```powershell
py -c "import re,hashlib,base64;h=open('index.html',encoding='utf-8',newline='').read();s=re.findall(r'<script>(.*?)</script>',h,re.S)[0];print(base64.b64encode(hashlib.sha256(s.encode()).digest()).decode())"
```

## Screenshots

Cento's Play listing screenshots (2026-10-27) are used in the "Así se ve en tu teléfono" section:
`inicio.jpg`, `solitario.jpg`, `sopa-de-letras.jpg`, `crucigrama.jpg`. The game cards above them
are text only, because there are no screenshots of Mahjong or Memoria.

Processing: the 720×1640 originals were cropped (top 80px Android status bar, bottom 56px gesture
bar), resized to 540×1128 and saved as JPEG quality 82 (62–77 KB each). The `<img>` tags declare
`width="540" height="1128"`. The raw PNGs are kept in `assets/screenshots/originales/`
(git-ignored, not published).

To add or replace one (for example Mahjong or Memoria), drop the raw PNG in `originales/`, process
it the same way and add a `<figure>` to the gallery in `index.html`:

```powershell
Add-Type -AssemblyName System.Drawing
function Resize-Shot($src, $dst, $top = 80, $bottom = 56) {
  $img = [System.Drawing.Image]::FromFile($src)
  $cropH = $img.Height - $top - $bottom
  $w = 540; $h = [int][math]::Round($cropH * $w / $img.Width)
  $bmp = New-Object System.Drawing.Bitmap $w, $h
  $g = [System.Drawing.Graphics]::FromImage($bmp)
  $g.InterpolationMode = 'HighQualityBicubic'
  $g.DrawImage($img, (New-Object System.Drawing.Rectangle 0, 0, $w, $h), (New-Object System.Drawing.Rectangle 0, $top, $img.Width, $cropH), 'Pixel')
  $codec = [System.Drawing.Imaging.ImageCodecInfo]::GetImageEncoders() | Where-Object MimeType -eq 'image/jpeg'
  $ep = New-Object System.Drawing.Imaging.EncoderParameters 1
  $ep.Param[0] = New-Object System.Drawing.Imaging.EncoderParameter ([System.Drawing.Imaging.Encoder]::Quality), 82L
  $bmp.Save($dst, $codec, $ep); $g.Dispose(); $bmp.Dispose(); $img.Dispose()
  "{0}: {1}x{2}, {3:N0} bytes" -f $dst, $w, $h, (Get-Item $dst).Length
}
# Resize-Shot "assets\screenshots\originales\05-mahjong.png" "assets\screenshots\mahjong.jpg"
```

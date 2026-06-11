# CSS support matrix — major email clients

Curated from caniemail.com data, client release notes, and production testing.
Clients grouped by rendering engine, because engine determines behavior.

Legend: ✅ full · ⚠️ partial (see note) · ❌ none

## The engines that matter

| Engine group | Clients | Share of opens (typical B2C/B2B mix) |
|---|---|---|
| WebKit | Apple Mail (macOS/iOS/iPadOS) | ~50% B2C (Apple MPP inflates this) |
| Gmail web/apps | Gmail web, Gmail Android/iOS, Gmail IMAP ("GANGA") | ~25–30% |
| Word engine | Outlook 2016/2019/2021/365 **classic** desktop on Windows | ~5% B2C, **15–30% B2B** |
| Blink/webview | Outlook "New" (OWA-based), Outlook.com, Outlook mobile, Yahoo, AOL, Samsung Mail | ~10–15% |
| Misc | Thunderbird, ProtonMail, HEY, mail.ru, Orange, GMX | low single digits |

> Always ask who the audience is. B2B = design for Word-engine Outlook first.
> B2C = Apple Mail + Gmail dominate, but Outlook still cannot be ignored.

## Layout

| Property | Apple Mail | Gmail | Outlook (Word) | Outlook.com/New | Yahoo/AOL | Notes & fallback |
|---|---|---|---|---|---|---|
| `<table>` layout | ✅ | ✅ | ✅ | ✅ | ✅ | The only universal layout primitive |
| `max-width` | ✅ | ✅ | ❌ | ✅ | ✅ | Pair with `<!--[if mso]>` ghost table at fixed width |
| `display:flex` / `grid` | ✅ | ⚠️ flex partial | ❌ | ⚠️ | ⚠️ | Never structural; decorative only |
| `float` | ✅ | ✅ | ⚠️ images only | ✅ | ✅ | Use `align` attribute on tables instead |
| `position` | ⚠️ | ❌ | ❌ | ❌ | ❌ | Do not use |
| `padding` on `<td>` | ✅ | ✅ | ✅ | ✅ | ✅ | The universal spacing tool |
| `padding` on `<div>`/`<p>` | ✅ | ✅ | ❌ | ✅ | ✅ | Word engine drops it — use td |
| `margin` | ✅ | ✅ | ⚠️ unreliable | ⚠️ strips on some elems | ✅ | Avoid; use td padding / spacer rows |
| `width` attribute on table/td | ✅ | ✅ | ✅ | ✅ | ✅ | Required for Outlook column widths |
| `height` (CSS) | ✅ | ✅ | ⚠️ | ✅ | ✅ | Outlook prefers `height` attr or font-size spacer |

## Typography

| Property | Apple Mail | Gmail | Outlook (Word) | Outlook.com/New | Yahoo/AOL | Notes |
|---|---|---|---|---|---|---|
| Web fonts (`@font-face`) | ✅ | ❌ | ❌ | ❌ | ❌ | Apple-only; always design the fallback |
| `line-height` | ✅ | ✅ | ⚠️ | ✅ | ✅ | Add `mso-line-height-rule:exactly` first |
| `letter-spacing` | ✅ | ✅ | ⚠️ | ✅ | ✅ | Outlook rounds oddly; keep subtle |
| `text-shadow` | ✅ | ✅ | ❌ | ⚠️ | ⚠️ | Decorative only |
| `font-size` px | ✅ | ✅ | ✅ | ✅ | ✅ | Use px; rem/em inconsistent in Outlook |

## Color & decoration

| Property | Apple Mail | Gmail | Outlook (Word) | Outlook.com/New | Yahoo/AOL | Notes |
|---|---|---|---|---|---|---|
| 6-digit hex | ✅ | ✅ | ✅ | ✅ | ✅ | The only safe color format |
| `rgba()` / opacity | ✅ | ⚠️ | ❌ | ⚠️ | ⚠️ | Never for must-render elements |
| `background-color` on td | ✅ | ✅ | ✅ | ✅ | ✅ | Also set `bgcolor` attribute as belt-and-braces |
| Background images | ✅ | ✅ | ❌ | ⚠️ | ✅ | Outlook needs VML `<v:rect>` fill (patterns §Backgrounds) |
| `border-radius` | ✅ | ✅ | ❌ | ✅ | ✅ | Outlook = square corners; VML roundrect for buttons |
| `box-shadow` | ✅ | ⚠️ | ❌ | ⚠️ | ⚠️ | Progressive enhancement only |
| `border` | ✅ | ✅ | ✅ | ✅ | ✅ | Safe everywhere |
| Gradients (CSS) | ✅ | ⚠️ | ❌ | ⚠️ | ⚠️ | Solid-color fallback first, gradient as enhancement |

## Responsive & interactive

| Feature | Apple Mail | Gmail | Outlook (Word) | Outlook.com/New | Yahoo/AOL | Notes |
|---|---|---|---|---|---|---|
| `@media` queries | ✅ | ✅ | ❌ | ✅ | ⚠️ Yahoo ok, AOL partial | Hybrid pattern covers Outlook |
| `<style>` in head | ✅ | ✅ kept, ⚠️ clipped msgs lose it | ⚠️ | ✅ | ✅ | Inline everything critical |
| `:hover` | ✅ | ⚠️ web only | ❌ | ⚠️ | ⚠️ | Enhancement only |
| Checkbox/`:checked` interactivity | ✅ | ❌ | ❌ | ❌ | ❌ | "Kinetic email" = Apple-only now; always fallback |
| `<video>` | ⚠️ plays inline | ❌ poster | ❌ | ❌ | ❌ | Use animated poster linking out |
| Animated GIF | ✅ | ✅ | ⚠️ first frame only (classic) | ✅ | ✅ | Put key message in frame 1 |
| SVG | ✅ | ❌ | ❌ | ❌ | ❌ | Use PNG @2x |
| `srcset` / `<picture>` | ✅ | ❌ | ❌ | ❌ | ❌ | Single @2x image with width attr |

## Dark mode

| Behavior | Clients | What to do |
|---|---|---|
| No change | Gmail web (most), Yahoo | Nothing |
| Full invert (forces dark) | Gmail Android/iOS on light emails, Outlook.com, Outlook mobile | Design colors that survive inversion; logo with padding-safe transparent PNG |
| Partial invert | Apple Mail (only fully light emails) | `color-scheme` meta + `prefers-color-scheme` query honored |
| Honors your dark styles | Apple Mail, Outlook macOS | `@media (prefers-color-scheme: dark)` + `[data-ogsc]` selectors for Outlook.com |

Dark-mode survival kit lives in `data/patterns.md` §Dark mode.

## Client-specific landmines

- **Gmail clips at 102KB** of HTML (not counting images). Clipped emails hide
  everything below the fold of the cut INCLUDING the unsubscribe link (CAN-SPAM
  risk) and the open-tracking pixel (analytics undercount).
- **Gmail removes the entire `<style>` block if any single rule errors.**
  Validate; no CSS comments with special chars inside style blocks.
- **Word-engine Outlook adds a 1px gap below images** unless `display:block`.
- **Word-engine Outlook ignores widths >1800px and auto-scales DPI** on
  high-DPI Windows: always pair CSS width with HTML `width` attribute.
- **Outlook.com strips `class` and `id` it doesn't like but keeps
  `data-ogsc`-prefixed rules** Microsoft rewrites for dark mode — use them.
- **Yahoo merges media queries wrongly when not wrapped**: wrap each query's
  rules in `{ }` per selector, no shorthand grouping across queries.
- **Apple MPP (privacy protection) preloads images**: open rates inflated;
  never gate logic on opens.
- **Samsung Mail ignores `<style>` in dark mode** but supports inline.
- **ProtonMail proxies images and strips trackers**; pixel-based analytics
  unreliable there.

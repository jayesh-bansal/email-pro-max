# Bulletproof patterns

Copy these verbatim, then modify. Every pattern here is production-tested
against Word-engine Outlook, Gmail (web + apps), Apple Mail, Outlook.com,
and Yahoo.

## Boilerplate head

```html
<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:v="urn:schemas-microsoft-com:vml" xmlns:o="urn:schemas-microsoft-com:office:office">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="x-apple-disable-message-reformatting">
  <meta name="color-scheme" content="light dark">
  <meta name="supported-color-schemes" content="light dark">
  <title></title>
  <!--[if mso]>
  <noscript>
    <xml>
      <o:OfficeDocumentSettings>
        <o:PixelsPerInch>96</o:PixelsPerInch>
      </o:OfficeDocumentSettings>
    </xml>
  </noscript>
  <![endif]-->
  <style>
    /* Resets every client needs */
    body, table, td, a { -webkit-text-size-adjust: 100%; -ms-text-size-adjust: 100%; }
    table, td { mso-table-lspace: 0pt; mso-table-rspace: 0pt; }
    img { -ms-interpolation-mode: bicubic; border: 0; height: auto; line-height: 100%; outline: none; text-decoration: none; }
    body { margin: 0; padding: 0; width: 100% !important; height: 100% !important; }
    /* iOS auto-link colors */
    a[x-apple-data-detectors] { color: inherit !important; text-decoration: none !important; }
    /* Gmail u + .body hack target */
    u + .body a { color: inherit; text-decoration: none; }
    /* Media queries + dark mode go here (see sections below) */
  </style>
</head>
<body class="body" style="margin:0; padding:0; background-color:#f4f4f7;">
```

## Preheader (preview text)

The snippet inbox UIs show after the subject. Hidden in the body, first element:

```html
<div style="display:none; max-height:0; overflow:hidden; mso-hide:all;">
  Your order #1234 has shipped — arriving Thursday.
</div>
<!-- whitespace hack so trailing body text doesn't leak into the preview -->
<div style="display:none; max-height:0; overflow:hidden; mso-hide:all;">
  &nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;&nbsp;&zwnj;
</div>
```

## Layout — the hybrid (a.k.a. spongy) skeleton

Fluid on modern clients via `max-width`, fixed for Outlook via the mso ghost
table. This is THE core layout pattern; everything sits inside it.

```html
<center style="width:100%; background-color:#f4f4f7;">
  <!--[if mso]>
  <table role="presentation" cellpadding="0" cellspacing="0" border="0" width="600" align="center"><tr><td>
  <![endif]-->
  <table role="presentation" cellpadding="0" cellspacing="0" border="0" align="center"
         style="width:100%; max-width:600px; margin:0 auto;">
    <tr>
      <td style="padding:24px;">
        <!-- content rows go here -->
      </td>
    </tr>
  </table>
  <!--[if mso]>
  </td></tr></table>
  <![endif]-->
</center>
```

### Two columns that stack on mobile (no media query required)

```html
<!--[if mso]>
<table role="presentation" width="552" cellpadding="0" cellspacing="0" border="0"><tr><td width="276" valign="top">
<![endif]-->
<div style="display:inline-block; width:100%; max-width:276px; vertical-align:top;">
  <table role="presentation" cellpadding="0" cellspacing="0" border="0" width="100%">
    <tr><td style="padding:8px;"><!-- column A --></td></tr>
  </table>
</div>
<!--[if mso]>
</td><td width="276" valign="top">
<![endif]-->
<div style="display:inline-block; width:100%; max-width:276px; vertical-align:top;">
  <table role="presentation" cellpadding="0" cellspacing="0" border="0" width="100%">
    <tr><td style="padding:8px;"><!-- column B --></td></tr>
  </table>
</div>
<!--[if mso]>
</td></tr></table>
<![endif]-->
```

Wrap both divs in a parent td with `font-size:0;` to kill inline-block gaps,
and restore font-size inside the columns.

## Buttons — bulletproof CTA

Padded td (works everywhere) + VML for rounded corners in Outlook:

```html
<table role="presentation" cellpadding="0" cellspacing="0" border="0" align="center">
  <tr>
    <td>
      <!--[if mso]>
      <v:roundrect xmlns:v="urn:schemas-microsoft-com:vml" xmlns:w="urn:schemas-microsoft-com:office:word"
        href="https://example.com/confirm" style="height:48px; v-text-anchor:middle; width:220px;"
        arcsize="13%" stroke="f" fillcolor="#2563eb">
        <w:anchorlock/>
        <center style="color:#ffffff; font-family:Arial,sans-serif; font-size:16px; font-weight:bold;">
          Confirm email
        </center>
      </v:roundrect>
      <![endif]-->
      <!--[if !mso]><!-->
      <a href="https://example.com/confirm" target="_blank"
         style="display:inline-block; background-color:#2563eb; color:#ffffff;
                font-family:Arial,Helvetica,sans-serif; font-size:16px; font-weight:bold;
                line-height:48px; mso-line-height-rule:exactly; text-align:center;
                text-decoration:none; width:220px; border-radius:6px;">
        Confirm email
      </a>
      <!--<![endif]-->
    </td>
  </tr>
</table>
```

Rules: tap target ≥44px tall; button text repeats the action verb; never an
image-only button (blocked images = invisible CTA).

## Images

```html
<img src="https://cdn.example.com/hero@2x.png" width="552" height="240"
     alt="New dashboard: spend by category at a glance"
     style="display:block; width:100%; max-width:552px; height:auto;
            border:0; color:#444444; font-family:Arial,sans-serif; font-size:16px;">
```

- Export @2x, set displayed width via attribute (Outlook) AND css (everyone).
- Styled alt text = the email still communicates with images blocked.
- `display:block` kills the Outlook/Gmail descender gap.

## Spacers & dividers

```html
<!-- vertical space: spacer row, not margin -->
<tr><td style="height:24px; line-height:24px; font-size:0;">&nbsp;</td></tr>

<!-- divider -->
<tr><td style="padding:16px 0;">
  <table role="presentation" width="100%" cellpadding="0" cellspacing="0" border="0">
    <tr><td style="border-top:1px solid #e5e7eb; font-size:0; line-height:0;">&nbsp;</td></tr>
  </table>
</td></tr>
```

## Backgrounds with image (Outlook VML)

```html
<td background="https://cdn.example.com/bg.jpg" bgcolor="#1f2937" valign="top"
    style="background-image:url('https://cdn.example.com/bg.jpg'); background-size:cover;">
  <!--[if mso]>
  <v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false"
          style="mso-width-percent:1000; height:300px;">
  <v:fill type="frame" src="https://cdn.example.com/bg.jpg" color="#1f2937"/>
  <v:textbox inset="0,0,0,0">
  <![endif]-->
  <div><!-- content over the background --></div>
  <!--[if mso]>
  </v:textbox></v:rect>
  <![endif]-->
</td>
```

Always set `bgcolor` solid fallback so text stays readable if the image fails.

## Dark mode survival kit

In the `<style>` block:

```css
@media (prefers-color-scheme: dark) {
  .dm-bg     { background-color: #1f2937 !important; }
  .dm-card   { background-color: #111827 !important; }
  .dm-text   { color: #f3f4f6 !important; }
  .dm-muted  { color: #9ca3af !important; }
}
/* Outlook.com / Outlook apps rewrite: target their prefixed clones */
[data-ogsc] .dm-text  { color: #f3f4f6 !important; }
[data-ogsb] .dm-card  { background-color: #111827 !important; }
```

Plus:
- Logo: transparent PNG with a thin white **stroke/outline** baked in, so a
  dark logo survives forced-dark inversion (or swap via the classes above).
- Never pure #000000 text on #ffffff — forced inversion turns it harsh;
  use #111827-on-#ffffff style pairs that invert gracefully.
- Test the forced-invert clients (Gmail apps, Outlook mobile) — they ignore
  your media query and recolor heuristically.

## Footer (compliance)

Every commercial email needs, visible without interaction:
physical mailing address, functioning unsubscribe link (one click to a working
page — no login), and sender identification. Transactional email may omit
unsubscribe but include the address anyway.

```html
<tr>
  <td align="center" style="padding:24px; font-family:Arial,sans-serif; font-size:12px;
      line-height:18px; mso-line-height-rule:exactly; color:#6b7280;">
    Acme Inc · 100 Main St, Springfield, ST 00000<br>
    You received this because you signed up at acme.com.<br>
    <a href="{{unsubscribe_url}}" style="color:#6b7280; text-decoration:underline;">Unsubscribe</a>
    &nbsp;·&nbsp;
    <a href="{{preferences_url}}" style="color:#6b7280; text-decoration:underline;">Email preferences</a>
  </td>
</tr>
```

## Typography stacks

```text
Neutral:    Arial, Helvetica, sans-serif
Modern:     'Segoe UI', Roboto, Helvetica, Arial, sans-serif
Humanist:   Verdana, Geneva, sans-serif
Serif:      Georgia, 'Times New Roman', serif
Mono (OTP): 'Courier New', Courier, monospace
```

Web font enhancement (Apple Mail only — wrap so Outlook never sees it):

```html
<!--[if !mso]><!-->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
<!--<![endif]-->
```

Then `font-family:'Inter', 'Segoe UI', Roboto, Arial, sans-serif`.

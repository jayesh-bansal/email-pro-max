# Pre-send QA checklist

Run every item before declaring an email done. Items marked 🔧 are checks the
skill performs on the markup; items marked 👤 require the user's external tools.

## Markup (🔧 verify in code)

- [ ] Total HTML size < 90KB (buffer under Gmail's 102KB clip)
- [ ] Every layout element is a `role="presentation"` table
- [ ] All critical styles inlined; `<style>` only carries media queries / dark mode
- [ ] Every color is 6-digit hex
- [ ] Every `line-height` preceded by `mso-line-height-rule:exactly`
- [ ] Every image: width+height attrs, `display:block`, styled `alt`
- [ ] CTA uses the bulletproof button pattern (VML branch present, `href` identical in both branches)
- [ ] Hybrid ghost tables: every `<!--[if mso]>` open has its matching close
- [ ] Preheader present, 40–100 chars, followed by the whitespace hack
- [ ] `lang` attribute on `<html>`; `<title>` set
- [ ] Dark-mode classes applied to bg/card/text elements; `color-scheme` metas present
- [ ] Footer: physical address + working unsubscribe (marketing) / address (transactional)
- [ ] All links absolute `https://`, `target="_blank"`, no bare tracking-domain links
- [ ] No `<script>`, no forms (stripped or flagged as phishing by most clients)
- [ ] Merge fields ({{...}}) match the user's ESP syntax — ask which ESP if unknown

## Content (🔧 review, 👤 decide)

- [ ] Subject ≤ ~50 chars; preheader complements, not repeats, the subject
- [ ] One primary CTA; it appears in the top 600px of the email
- [ ] Email still makes sense with all images blocked (alt-text walkthrough)
- [ ] Text-to-image ratio: never a single-big-image email (spam signal + invisible when blocked)
- [ ] Plain-text version generated and supplied alongside the HTML

## External (👤 user must run)

- [ ] Real-client screenshots: Litmus / Email on Acid / Testi.at across
      Outlook Windows, Gmail app Android (dark), Apple Mail (dark), Outlook.com, Yahoo
- [ ] Send to a seed Gmail account: confirm no "[Message clipped]"
- [ ] Spam scoring (mail-tester.com / GlockApps) ≥ 9/10
- [ ] Click every link in the delivered email, including unsubscribe
- [ ] SPF / DKIM / DMARC aligned for the sending domain (ESP dashboard)

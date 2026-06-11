---
title: Why your AI writes HTML email that breaks in Outlook (and how I fixed it)
published: false
description: Email clients aren't browsers. Outlook still renders with Word's engine. Here's why AI-generated email shatters — and a Claude skill that fixes it.
tags: webdev, ai, html, productivity
cover_image:
---

Ask any AI coding assistant for an email template and you'll get something beautiful: flexbox layout, `border-radius`, `rgba()` colors, a clean `<button>`. Paste it into your ESP, send a test, open it in Gmail on your phone — perfect.

Then a customer opens it in Outlook on Windows and it looks like a ransom note.

This isn't the AI being dumb. It's the AI being *too smart* — applying everything it knows about modern web development to a medium where most of that knowledge is actively wrong.

## Email clients are not browsers

Here's the fact that breaks everyone's mental model: **Outlook for Windows renders HTML email with Microsoft Word's engine.** Not a browser engine. Word. The same thing that renders your `.docx` files.

That one fact cascades into a pile of constraints:

- **No flexbox, no grid, no floats.** Layout has to be done with `<table>` elements, like it's 2005.
- **No `max-width`.** Your responsive container doesn't constrain anything in Outlook.
- **No `border-radius`.** Your rounded button is a sharp rectangle.
- **No background images** without special VML markup.
- **`padding` on a `<div>` is ignored** — only `<td>` padding is reliable.
- **`rgba()` and 3-digit hex fail.** Six-digit hex only.

And Outlook isn't even the only landmine:

- **Gmail clips your email at 102KB** of HTML. Everything past the cut — including your unsubscribe link and tracking pixel — just vanishes behind a "[Message clipped]" link.
- **Gmail drops your entire `<style>` block** if a single CSS rule has a syntax error.
- **Gmail and Outlook mobile force dark mode** by inverting your colors, ignoring your media queries, often turning carefully chosen palettes into mud.
- **Images are blocked by default** in Outlook — so your email has to communicate as styled text first.

None of this is in the training data's "best practices for HTML/CSS," because none of it is true for browsers. So the AI confidently generates browser-correct code that fails in the one place corporate email actually gets read.

## The patterns that actually work

Email developers have spent fifteen years figuring out the workarounds. A few examples:

### Bulletproof buttons

A CSS button with `border-radius` is square in Outlook. The fix is a hybrid: a normal anchor for modern clients, plus a VML `<v:roundrect>` that *only* Outlook sees, hidden from everyone else inside conditional comments:

```html
<!--[if mso]>
<v:roundrect xmlns:v="urn:schemas-microsoft-com:vml"
  href="https://example.com/confirm" arcsize="13%" fillcolor="#2563eb" stroke="f"
  style="height:48px; v-text-anchor:middle; width:220px;">
  <center style="color:#ffffff; font-family:Arial; font-size:16px; font-weight:bold;">
    Confirm email
  </center>
</v:roundrect>
<![endif]-->
<!--[if !mso]><!-->
<a href="https://example.com/confirm"
   style="display:inline-block; background:#2563eb; color:#ffffff; border-radius:6px;
          line-height:48px; width:220px; text-align:center; text-decoration:none;
          font-family:Arial; font-weight:bold;">Confirm email</a>
<!--<![endif]-->
```

Rounded corners in Outlook, clean anchor everywhere else, and it works with images blocked because it's real text, not an image.

### The hybrid (spongy) layout

You want fluid width on modern clients but Outlook needs a fixed pixel width. The trick is a "ghost table" wrapped in `<!--[if mso]>` that only Outlook sees:

```html
<!--[if mso]>
<table role="presentation" width="600" align="center"><tr><td>
<![endif]-->
<table role="presentation" align="center" style="width:100%; max-width:600px;">
  <tr><td style="padding:24px;"><!-- content --></td></tr>
</table>
<!--[if mso]>
</td></tr></table>
<![endif]-->
```

Modern clients honor `max-width` and go fluid; Outlook reads the fixed 600px ghost table and renders correctly.

### Dark mode survival

You can't stop Gmail/Outlook mobile from force-inverting colors, but you can design palettes that survive it, and you can target Outlook.com's dark-mode rewrite with its proprietary `[data-ogsc]` selectors:

```css
[data-ogsc] .text { color: #f3f4f6 !important; }
[data-ogsb] .card { background-color: #111827 !important; }
```

## I packaged all of it into a Claude skill

I kept re-explaining these rules to Claude on every email task, so I turned them into a [Claude skill](https://github.com/jayesh-bansal/email-pro-max) — a reusable instruction pack the agent loads automatically when it detects an email task.

It's just a `SKILL.md` with the non-negotiable rules plus data files:

- a **CSS support matrix** for 25+ clients grouped by rendering engine,
- **copy-paste bulletproof patterns** (the button, hybrid layout, dark-mode kit, preheader, compliance footer),
- a **pre-send QA checklist** split into what the agent verifies vs. what you test externally,
- and **production templates** (verification/OTP to start).

Install is one line:

```bash
git clone https://github.com/jayesh-bansal/email-pro-max.git ~/.claude/skills/email-pro-max
```

After that, when you ask Claude for an email, the first draft uses table layout, VML buttons, styled alt text, and dark-mode-safe colors — instead of browser-correct code that breaks where it matters.

It's MIT, zero dependencies, just markdown. Repo here, with a before/after Outlook comparison in the README:

**→ https://github.com/jayesh-bansal/email-pro-max**

If you do email development professionally, I'd genuinely love a second pair of eyes on the client-support matrix — open an issue if you catch something it gets wrong.

---

*Bonus context for the curious: the reason Outlook uses Word's engine at all is that Microsoft moved Outlook from Internet Explorer's (already bad) engine to Word's in 2007, and enterprise inertia has kept it there ever since. "New Outlook" uses a modern Blink-based engine — but classic desktop Outlook still ships, still renders with Word, and still holds a big chunk of B2B opens. So the workarounds aren't going away yet.*

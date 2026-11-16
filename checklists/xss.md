# XSS Checklist

Cross-Site Scripting: attacker-controlled input executed as script
in a victim's browser.

## Setup (before you start)

- [ ] Target and feature are in scope.
- [ ] You know where the input goes (search box, profile field,
      comment, file name, URL parameter) and where it is displayed.
- [ ] You have a test account; never test stored XSS against another
      user's view.
- [ ] For blind XSS, you have your *own* listener/collaborator
      endpoint (your own server). Never use a third-party listener
      that would receive other people's data.

## Reflected XSS

- [ ] Input reflected in the HTML body: test a basic probe and note
      how the output is encoded.
- [ ] Input reflected inside an HTML attribute (`value="..."`,
      `href="..."`, `title="..."`): test breaking out of the attribute.
- [ ] Input reflected inside a JavaScript string or block: test
      breaking out of the string context.
- [ ] Input reflected in a URL (`href`, redirect target, `Location`
      header): test `javascript:` / `data:` contexts where applicable.
- [ ] Error pages and 404 pages that echo the path or a parameter.
- [ ] Every parameter, not only the obvious one — including hidden
      fields and headers the app reflects (for example `Referer`-based
      pages, if any).

## Stored XSS

- [ ] Profile/display-name fields rendered on other pages.
- [ ] Comments, reviews, messages rendered to other users — test
      with your own second account as the *viewer*, never a real user.
- [ ] File names rendered in download lists or admin views.
- [ ] Rich-text fields (editors): test the allowed-tag list — which
      tags/attributes survive, and do any of them execute script
      (`onerror`, `onload`, `href="javascript:"`, SVG, `style`)?
- [ ] Stored input that is only rendered in an admin/moderation view.

## DOM XSS

- [ ] Sources: `location.hash`, `location.search`, `document.referrer`,
      `postMessage` data, `localStorage`/`sessionStorage` values.
- [ ] Sinks: `innerHTML`, `document.write`, `eval`,
      `insertAdjacentHTML`, jQuery `.html()`, `location` assignment,
      `setTimeout`/`setInterval` with a string.
- [ ] Test with a benign marker first to confirm data reaches the
      sink before trying to execute anything.
- [ ] `postMessage` handlers: check the `origin` validation and
      what the handler does with the message.

## Filters and encodings

- [ ] Note exactly what is filtered or encoded (tags stripped? quotes
      encoded? event handlers removed?).
- [ ] Test the filter per context — a filter that is safe in HTML
      body may be unsafe in an attribute or in JS.
- [ ] Check for double encoding / decoding steps (input decoded
      twice, stored decoded, rendered raw).
- [ ] Check whether the Content Security Policy (CSP) limits impact
      — and whether the policy is actually enforced (missing header
      on the vulnerable page, `unsafe-inline`, broad hosts).

## Evidence — what proves it

- [ ] Script executed in the page context (a benign marker such as
      changing the document title or a `console.log` you can show).
      A raw `alert()` screenshot is acceptable proof of execution,
      but prefer a marker that does not disrupt the page.
- [ ] For stored XSS: the execution happened in the *viewer's*
      session (your own second account), with steps to reproduce.
- [ ] For DOM XSS: the exact URL/input that triggers it, and the
      source-to-sink path.
- [ ] Impact statement: what a victim's session allows (actions the
      script could take in that context — be specific to the app,
      and note `HttpOnly` cookies if they limit session theft).

## What does NOT count

- **Self-XSS**: script that executes only for the attacker
  themselves, with no way to make a victim execute it.
- Input reflected but fully encoded in every context (no execution).
- Execution inside a sandboxed iframe with no access to the app.
- A finding that needs the victim to paste code into devtools.

## Impact notes (for the report)

- State the context (reflected/stored/DOM) and the exact input point.
- State who the victim is (any visitor, logged-in user, admin) and
  what the script could do in their session.
- If CSP or `HttpOnly` limits the impact, say so honestly — it
  changes the severity, it does not erase the finding.

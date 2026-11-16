# CSRF Checklist

Cross-Site Request Forgery: a victim's browser is tricked into
making a state-changing request the victim did not intend.

## Setup (before you start)

- [ ] Target is in scope; you are testing only your own account.
- [ ] You know which requests change state (update profile, change
      email/password, transfer, purchase, delete, add admin).
- [ ] You have a page you control (your own site or a local HTML
      file) to host the proof-of-concept form.

## State-changing actions

- [ ] List every state-changing action in the app (settings, orders,
      payments, sharing, admin actions).
- [ ] For each: does the request carry an anti-CSRF token?
- [ ] If a token exists: is it validated (remove it — does the
      request still succeed? replace it with another user's token,
      with a stale token, with a token from another session)?
- [ ] Is the token tied to the session, or is it a static/per-account
      value that never changes?
- [ ] GET-based state changes: does a state change happen on a GET
      request (action via link or image load)?

## Token and header defences

- [ ] `Origin` / `Referer` validation: does the server reject
      cross-origin requests, or accept missing/foreign origins?
- [ ] `SameSite` on the session cookie: `Lax`, `Strict`, or missing?
      (Note: `SameSite=Lax` still allows top-level GETs.)
- [ ] Custom headers: does the API require a header a simple form
      cannot set (for example `X-Requested-With`)? Is it validated?
- [ ] Content-Type checks: does the endpoint accept a simple
      `application/x-www-form-urlencoded` / `multipart` / `text/plain`
      request (the kind a cross-site form can send)?
- [ ] CORS: is the endpoint's CORS policy permissive enough to let a
      cross-site page read the response (that turns CSRF into
      something worse — note it separately)?

## Proof of concept

- [ ] Build the PoC on your own page, targeting your own account.
- [ ] Confirm the action executes when the page is loaded in a
      logged-in session (your own), and does *not* execute when
      logged out.
- [ ] Record the before/after state (setting changed, order placed).
- [ ] Undo the change afterwards if the app allows it.

## Evidence — what proves it

- [ ] The PoC page, the request it sends, and the before/after state.
- [ ] Proof the token check is absent or bypassable (request without
      token succeeding, or with a wrong token succeeding).
- [ ] The victim requirement stated plainly (victim must be logged
      in and visit the page).

## What does NOT count

- CSRF on actions with no state change (search, view, read).
- CSRF on logout alone (usually informational).
- A missing token on an endpoint that does not change anything.
- A PoC that only works when the victim is also the attacker
  (self-CSRF).

## Impact notes (for the report)

- State the action and what it changes (email change → account
  takeover path; transfer → financial loss; admin add → privilege
  escalation). Be concrete.
- State the victim interaction required (visit a link, one click).
- If the impact needs a second step (for example email change also
  needs confirmation), say so — it changes the severity.
- Do not inflate: a CSRF that changes a display name is not the
  same as one that changes an email address.

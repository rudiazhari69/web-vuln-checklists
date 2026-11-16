# Authentication Checklist

Authentication flaws: login, sessions, password reset, MFA, and
OAuth — the front door and its spare keys.

## Setup (before you start)

- [ ] Target is in scope; you are testing only accounts you created.
- [ ] Never use another person's credentials, and never brute-force.
- [ ] Note the normal flows first (register, login, logout, reset,
      MFA enrolment) before testing edge cases.

## Login

- [ ] Wrong password returns the same generic error as a wrong
      username (no user enumeration through the message or timing —
      and if it does differ, record it, but note enumeration alone is
      usually low impact).
- [ ] Account lockout / rate limiting: does the app slow or block
      repeated failures? Do **not** brute-force — a handful of
      attempts to observe the behaviour is enough.
- [ ] Session created on login: is the session token new (not the
      pre-login anonymous token)? Old token still valid after login
      = session fixation candidate.
- [ ] "Remember me" token: long-lived, single-use rotation, revocable?
- [ ] Login CSRF: can a login form be submitted cross-site (usually
      low impact — note it, do not inflate it).
- [ ] Concurrent sessions: does logging in elsewhere invalidate the
      old session, or do both stay live?

## Sessions

- [ ] Session token entropy and length (is it a guessable value?).
- [ ] Session transmitted only over HTTPS, with `Secure` and
      `HttpOnly` flags on the cookie.
- [ ] Logout invalidates the session server-side (reuse the old
      cookie after logout and see if it still works).
- [ ] Session expiry: absolute timeout and idle timeout behaviour.
- [ ] Password change: does it invalidate other sessions?
- [ ] Session token in URL, logs, or referrer (token leakage).

## Password reset

- [ ] Reset token: random, long, single-use, short expiry.
- [ ] Token tied to the account that requested it (request a reset
      for Account A, try to use the token on Account B).
- [ ] Token sent only to the registered channel (no token in the
      response body, no token in the URL of a page that leaks it).
- [ ] Token reuse: can the same token be used twice?
- [ ] Token expiry: does an old token still work after a new one is
      issued?
- [ ] Reset flow does not reveal whether an account exists (or if it
      does, record it as enumeration, low impact).

## MFA

- [ ] MFA enrolment: is the secret shown once, and is a code required
      to confirm enrolment before it activates?
- [ ] TOTP code reuse: can the same code be submitted twice?
- [ ] MFA bypass: can a step be skipped (direct navigation to the
      post-MFA page, removing a parameter, replaying an old session)?
- [ ] Recovery codes: single-use, shown once, revocable.
- [ ] MFA reset / disable flow: does it require re-authentication?
- [ ] "Remember this device": how long, revocable, tied to what?

## OAuth / social login basics

- [ ] `redirect_uri` validation: exact match, or open to other hosts?
- [ ] `state` parameter present and validated (CSRF on the OAuth flow).
- [ ] Authorization code: single-use, short expiry, tied to client.
- [ ] Account linking: can an attacker link their social account to
      a victim's account (email match without verification)?
- [ ] Email verification: does the OAuth flow trust an unverified
      email from the provider?

## Evidence — what proves it

- [ ] Request/response pairs for each step, with your own two accounts.
- [ ] Before/after state (session valid, then invalid after logout).
- [ ] For reset/MFA issues: the exact token lifecycle you observed.
- [ ] You performed no brute force and used no other user's account.

## What does NOT count

- Username/email enumeration alone (usually informational).
- Missing lockout with no demonstrated account-takeover path.
- Logout CSRF, login CSRF, and other interaction-heavy issues
  presented as high severity without a realistic victim path.

## Impact notes (for the report)

- State the exact flow and the step that breaks.
- State the victim requirement (none, one click, victim must be
  logged in).
- State the outcome (account takeover, session hijack, MFA bypass)
  — and be honest when the outcome is only enumeration.

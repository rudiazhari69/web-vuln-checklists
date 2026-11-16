# SSRF Checklist

Server-Side Request Forgery: the server fetches a URL you control,
and you can point it somewhere it should not reach.

## Setup (before you start)

- [ ] Target feature and parameters are in scope.
- [ ] You have your *own* listener server (your own VPS or a
      collaborator-style endpoint you control). Never point SSRF at a
      third-party service that would receive the target's traffic.
- [ ] You know the feature's normal behaviour (what it fetches, what
      it returns, what it blocks).

## Where to look

- [ ] URL parameters: link preview, image fetch, avatar import,
      "fetch URL", webhook URL, file import from URL.
- [ ] File-upload features that fetch or parse a remote file.
- [ ] PDF / document generation that renders a URL or HTML.
- [ ] Redirect parameters and open-redirect-adjacent flows that the
      server follows internally.
- [ ] Webhook / callback configuration (URL you supply, server calls).
- [ ] API parameters that accept a URL, a path, or a host (including
      inside JSON bodies and nested objects).
- [ ] Image processing / thumbnail features that fetch the source.

## Verification (responsible)

- [ ] Point the feature at your own listener first. Confirm the
      server makes the request (your listener logs it) and note what
      it sends (headers, method, IP).
- [ ] Point it at your own listener on a non-standard port and on a
      private-range address you control, to test the filter without
      touching real internal services.
- [ ] Test the URL validation: blocklist vs allowlist behaviour,
      redirect following (open redirect chained into SSRF), URL
      parsing quirks (user-info, `@`, encoded characters, 0x/decimal
      IP forms, DNS names that resolve to private ranges).
- [ ] Test DNS rebinding only against a domain you own, and only to
      prove the server re-resolves — do not use it to reach real
      internal hosts.
- [ ] Cloud metadata (`169.254.169.254` and equivalents): **do not
      request it.** The correct proof is that the server will fetch
      an internal/private address you control in your own test range.
      Requesting real cloud metadata is out of bounds for a test.

## Evidence — what proves it

- [ ] Your listener received the server's request (log excerpt with
      timestamp, source, and the path you supplied).
- [ ] The response the app returns reflects the fetched content or
      its status (or an error that proves the fetch happened).
- [ ] The filter bypass, if any, is shown step by step (blocked URL
      vs accepted URL).
- [ ] You did not fetch any real internal service, metadata endpoint,
      or third-party host.

## What does NOT count

- A request the server makes to a public URL you supplied, with no
  access to internal addresses (fetching public URLs may be the
  feature working as designed).
- A blind SSRF claim with no listener log.
- Fetching a third party's server and calling it impact.

## Impact notes (for the report)

- State the feature and the parameter, and what the server fetches.
- State what the server's request proves (it reaches private ranges
  you control, it follows redirects, it sends internal headers).
- State the realistic next step an attacker would need (internal
  service discovery) without performing it yourself.
- If the response returns fetched content to you, say exactly what
  content and whether it includes anything sensitive.

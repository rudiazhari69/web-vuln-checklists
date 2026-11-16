# Web Vulnerability Checklists

Per-vulnerability-class testing checklists for web bug bounty hunting.
Each checklist is a working list, not a tutorial: pick one class, work
through it against an in-scope target, and check a box only when you
have evidence.

## How to use

1. **Read the program scope first.** A checklist item is only worth
   testing on an asset the program covers. Out-of-scope testing is
   not a finding, it is a violation.
2. **One class at a time.** Do not jump between classes mid-target;
   you will miss the subtle cases each class hides.
3. **Check a box only with evidence.** A checked item means you have
   a saved request/response pair, a screenshot, or a short screen
   recording that reproduces it. "Looked at it" is not evidence.
4. **Use your own accounts only.** Every checklist that needs a
   second account means a second account *you* created. Never test
   against another user's data.
5. **Stop at proof of impact.** Prove the boundary is crossed (you
   reached data or an action that is not yours), then stop. Do not
   dump data, do not enumerate other users, do not persist changes
   you cannot undo.
6. **File it properly.** A checked list becomes a report only when
   you can state the impact on a real user. See
   [bug-bounty-report-templates](https://github.com/rudiazhari69/bug-bounty-report-templates)
   for the report shape.

> **Scope warning:** these checklists are test guidance, not exploit
> scripts and not permission. Program rules always win over this
> repo. If a program lists a class or a technique as out of scope,
> skip it — even if it is in a checklist here.

## Index

| Checklist | What it covers |
| --------- | -------------- |
| [IDOR / BOLA](checklists/idor-bola.md) | Object references, second-account tests, nested objects, bulk endpoints |
| [XSS](checklists/xss.md) | Reflected, stored and DOM XSS; contexts, filters, proof of impact |
| [SQL Injection](checklists/sqli.md) | Detection, confirmation, data-access boundaries |
| [SSRF](checklists/ssrf.md) | URL parameters, redirects, webhooks, cloud metadata |
| [Authentication](checklists/auth.md) | Login, sessions, password reset, MFA, OAuth basics |
| [CSRF](checklists/csrf.md) | State-changing actions, token validation, SameSite, impact |
| [File Upload](checklists/file-upload.md) | Type checks, extension bypasses, storage and serving |
| [Business Logic](checklists/business-logic.md) | Price/quantity tampering, workflow skips, races, coupons |

## What these checklists are not

- **Not a scanner.** There is nothing to run here. The checklists
  describe what to look at and what counts as evidence.
- **Not a guarantee.** A target can pass every box and still be
  vulnerable, or fail a box in a way that is not a finding (public
  data, own data, documented behaviour).
- **Not a bypass guide.** Items describe the check to perform, not a
  payload to paste. Where a technique is named (for example a
  filter-bypass context), it is named so you recognise it, not so
  you can copy it blindly.

## License

[MIT](LICENSE)

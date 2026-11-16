# SQL Injection Checklist

SQL Injection: attacker-controlled input changes the meaning of a
database query.

## Setup (before you start)

- [ ] Target and parameter are in scope.
- [ ] You know the parameter's normal behaviour (valid value, error
      on invalid value, empty result).
- [ ] You are testing against your own data / test rows only.

## Detection

- [ ] Error-based: submit a single quote / broken syntax and note
      whether the app returns a database error, a generic error, or
      the same page. Save the response.
- [ ] Boolean-based: submit a condition that is always true and one
      that is always false (`OR 1=1` vs `OR 1=2` style probes, adapted
      to the parameter) and compare the two responses.
- [ ] Time-based: submit a delay probe and measure the response time
      against a baseline of several normal requests. One slow response
      is not proof — repeat it.
- [ ] Numeric vs string parameters: test each parameter in its own
      context; a numeric parameter behaves differently from a string.
- [ ] Second-order: input stored in one place and used in a query
      elsewhere (profile fields, settings) — test the read path too.
- [ ] Every parameter on the request, including sort/order/filter
      fields and IDs — not only search boxes.

## Confirmation

- [ ] The behaviour changes consistently and only when the injection
      probe is present (repeat the true/false pair at least twice).
- [ ] You can identify the database type from the error or the
      behaviour (do not rely on guessing).
- [ ] You can extract one benign value you already know (for
      example the database version, or a row you own) — this proves
      data access without touching anyone else's data.

## Data-access boundaries — read this before going further

- [ ] **Never dump real user data.** No `SELECT *` over user tables,
      no credential tables, no bulk extraction.
- [ ] **Never run destructive queries.** No `UPDATE`, `DELETE`,
      `DROP`, no writes of any kind.
- [ ] **Never extract more than the minimum proof**: one known value
      (version string, or a value from a row you created) is enough.
- [ ] Stop at confirmation. The finding is "injection confirmed with
      data access", not a database dump.

## Evidence — what proves it

- [ ] Paired true/false responses (or a repeatable time delay) with
      request/response saved.
- [ ] One benign extracted value (database version, or your own row).
- [ ] The exact parameter, request, and probe that triggers it.
- [ ] A note of whether the injection is in a read path, a write
      path, or an authentication path (login bypass changes impact).

## What does NOT count

- A single database error message with no way to change query
  behaviour (error disclosure alone is usually informational).
- A delay that does not repeat (network noise, slow backend).
- Behaviour that also happens with benign invalid input.

## Impact notes (for the report)

- State the database type and the injection point.
- State what data the query can reach (which tables the query
  touches — from the feature's normal behaviour, not from dumping).
- State whether authentication can be bypassed through it.
- Be honest about what you did *not* extract: "confirmed with
  version string only, no user data accessed" is a strength, not a
  weakness.

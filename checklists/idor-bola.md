# IDOR / BOLA Checklist

Broken Object Level Authorization: the server checks *who you are*
but not *whether the object is yours*.

## Setup (before you start)

- [ ] Target and endpoint are in scope (confirmed from the program page).
- [ ] Two accounts created by you: Account A (victim) and Account B (attacker).
- [ ] Account A owns at least one object of each type you will test
      (order, address, payment method, file, message, profile).
- [ ] You have recorded the object IDs as Account A sees them.

## Direct object references

- [ ] Numeric IDs: take an object ID from Account A (`/orders/123`)
      and request it as Account B. Record status + body.
- [ ] Same test with the ID in the request body (POST/PUT/PATCH),
      not only in the URL.
- [ ] Same test with the ID in a header or query parameter, if the
      app accepts it there.
- [ ] UUID / random IDs: repeat the test — a long ID is not proof of
      authorization; the server must still check ownership.
- [ ] Predictable IDs in a different feature (receipts, invoices,
      tickets, downloads) — test each one separately.
- [ ] IDs that appear in URLs of emails/links the app sends (magic
      links, shared links): request them unauthenticated and as the
      other account.

## Nested and related objects

- [ ] Nested objects: `/users/<id>/addresses/<addressId>` — change
      the inner ID, then the outer ID, then both.
- [ ] Objects referenced inside another object's response (IDs
      embedded in JSON): request each embedded ID directly.
- [ ] Actions on an object (cancel, delete, edit, share): test the
      action endpoint as Account B, not only the read endpoint.
- [ ] State transitions: change an order status, a booking, a
      subscription as the other account.

## Bulk and list endpoints

- [ ] List endpoints: does Account B's list leak Account A's items
      (pagination, search, filters)?
- [ ] Bulk endpoints (multi-ID update/delete/export): include one
      ID belonging to Account A among Account B's own IDs.
- [ ] Search/filter parameters (`user_id=`, `owner=`, `account=`):
      change them to Account A's values.
- [ ] Export/download endpoints: request Account A's export by ID.
- [ ] Pagination / cursor parameters: walk past your own page of
      results and see whose records appear.

## Mass assignment (adjacent check)

- [ ] Add an extra field to an update request (`role`, `is_admin`,
      `owner_id`, `price`, `status`) and see whether the server
      applies it. One field at a time, on your own object first.

## Evidence — what proves it

- [ ] Account B received Account A's object data (not an error page,
      not a redirect to login, not an empty object).
- [ ] The same request as Account A returns the same data, so you
      can show the two responses side by side.
- [ ] For actions: the action took effect (status changed, record
      deleted) on Account A's object — verify by reading it back as
      Account A, then restore it if possible.
- [ ] You stopped after the first proof. You did not enumerate IDs
      or read other users' records.

## What does NOT count

- Accessing your **own** objects (any ID you own is expected to work).
- Accessing **public** objects (shared/public listings, public profiles).
- An error message that reveals an object exists, without returning
  its data.
- A different status code with an empty body and no data.

## Impact notes (for the report)

- Name the object type and the data it exposes (name, address,
  payment reference, order contents — be specific).
- State whether it is read-only or also allows modification/deletion.
- State whether it needs one victim ID (guessable / enumerable) or
  works with any ID.
- If the IDs are sequential, say so — it changes the severity.

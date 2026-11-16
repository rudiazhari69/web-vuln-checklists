# File Upload Checklist

File upload flaws: type checks that can be bypassed, dangerous
content that gets stored, and files served in a way that executes.

## Setup (before you start)

- [ ] Target feature and file types are in scope.
- [ ] You know the intended file types (images, documents, avatars)
      and the size limit.
- [ ] Use benign test files only. Never upload real malware; an
      EICAR-style benign test string is the limit for content tests.

## Type checks

- [ ] Extension allowlist vs denylist: which extensions are accepted
      and which are rejected? Test each boundary (`.php`, `.phtml`,
      `.html`, `.svg`, `.exe`, `.zip`).
- [ ] Case variations (`.PHP`, `.PhP`) and double extensions
      (`file.php.jpg`, `file.jpg.php`).
- [ ] MIME type check: is it based on the `Content-Type` header you
      send (trivially spoofed) or on the file content?
- [ ] Magic bytes / content sniffing: does the server inspect the
      file header, or trust the extension?
- [ ] Trailing characters and separators (`file.php%00.jpg`,
      `file.php;`, `file.php `, `file.php/.` — legacy parsers only;
      record the server behaviour, do not assume it works).
- [ ] Archive uploads: does the server extract archives, and does it
      validate the contents (zip slip / path traversal in entries is
      a separate finding — note it, do not exploit it beyond proof).

## Content checks

- [ ] SVG upload: is an uploaded SVG served with an image content
      type, or as `image/svg+xml` that can execute script in the
      viewer's browser (stored XSS via SVG)?
- [ ] HTML file upload: is an uploaded `.html` served from the app's
      domain (stored XSS / phishing on the app origin)?
- [ ] Polyglot files: a file that is a valid image *and* valid HTML/JS.
- [ ] Image parsing: does the app re-encode/resize images (which
      usually strips embedded content) or store them as-is?
- [ ] Metadata (EXIF): is metadata stripped, or preserved and rendered?

## Storage and serving

- [ ] Upload path: predictable (sequential, timestamp, original name)
      or random? Can you guess another user's file URL?
- [ ] Access control: is the uploaded file publicly reachable without
      authentication, or checked per user?
- [ ] Execution: is the upload directory executable (can an uploaded
      script run if the extension check is bypassed)?
- [ ] Content-Type on serving: does the server send the uploaded
      file with a safe type and `Content-Disposition: attachment`,
      or inline with an executable type?
- [ ] CORS / embedding: can the file be embedded cross-origin?

## Size and abuse

- [ ] Size limit enforced (server-side, not only client-side)?
- [ ] Filename handling: path traversal in the filename
      (`../../`), overly long names, special characters.
- [ ] Do not test denial of service. One file at the size limit is
      enough to confirm the limit; do not flood the endpoint.

## Evidence — what proves it

- [ ] The uploaded file, the request that uploaded it, and the URL
      where it is served.
- [ ] The served response headers (Content-Type, Content-Disposition)
      and, for SVG/HTML, proof of script execution in your own
      browser session.
- [ ] For access-control issues: your second account reaching the
      first account's file.
- [ ] Benign test files only — no malware, no real payloads.

## What does NOT count

- Uploading a disallowed extension and getting an error (the check
  working as designed).
- An SVG/HTML file that is served as a download (`attachment`) from
  a separate, sandboxed domain with no script execution.
- A predictable URL for a file the app intentionally makes public.

## Impact notes (for the report)

- State the bypass (which check failed) and the file type that got
  through.
- State the serving behaviour (inline on app origin = XSS; download
  = lower impact; execution = remote code execution).
- State the access control on the stored file (public, any logged-in
  user, owner only).

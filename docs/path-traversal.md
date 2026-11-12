# Path Traversal (CWE-22) — a field guide

> Shared theory for the **Path Traversal** lab series. Each lab's `GUIDE.md`
> links back here for the concepts and the manual-testing methodology, then adds
> only what is specific to that level. Read this first; it is tool- and
> lab-agnostic on purpose.

<br>

## What it is

**Path traversal** (also called *directory traversal*, **CWE-22**) happens when
an application builds a filesystem path from user input and an attacker can make
that path point *outside* the directory the app intended to expose.

The classic ingredient is the `../` sequence ("dot-dot-slash"), which means
"go up one directory". If a request like:

```
GET /download?file=report.pdf        ->  /var/app/files/report.pdf
```

can be turned into:

```
GET /download?file=../../../../etc/passwd   ->  /etc/passwd
```

then the boundary of `files/` was never really enforced. The same bug lets an
attacker read application source, configuration, secrets, or — depending on the
feature — *write* files where they should not be able to.

<br>

## Why it happens

Almost every path traversal comes from the same root cause: **trusting user
input to build a path, and checking the string instead of the resolved path.**

Typical broken patterns:

- **No check at all** — concatenate user input onto a base directory and open it.
- **A "forced" base directory** — prepend a safe folder and assume that confines
  the result. It does not: `base + "/" + "../secret"` still escapes.
- **Blocklist filtering** — strip or reject `../`. Blocklists are notoriously
  bypassable: non-recursive strips (`....//`), encodings (`%2e%2e%2f`,
  double-encoding `%252e%252e%252f`), backslashes, absolute paths, Unicode.
- **Validating the wrong thing** — checking a file *extension*, or that a string
  *starts with* an allowed prefix, without canonicalising the path first.

The common theme: the code reasons about the **raw string** the user sent, not
about the **real path the filesystem will resolve**. The only robust fix works
on the canonical, resolved path (see *Remediation*).

<br>

## Impact

Why it matters, beyond "reading a text file":

- **Information disclosure** — source code, `/etc/passwd`, configuration files,
  cloud metadata, private keys.
- **Credential theft** — `.env` files, database connection strings, API tokens,
  session stores.
- **Escalation** — leaked secrets often unlock other systems; traversal is
  frequently step one of a larger chain.
- **Integrity / RCE** — when the sink *writes* (uploads, log paths, templates),
  traversal can overwrite files or drop code that later executes.

<br>

## Manual analysis methodology

Scanners can flag *some* traversals, but logic-specific bypasses (the whole
point of this series) are found by hand. The workflow is the same whatever
proxy you use:

1. **Map** the app and find every place a filename, path, id or "download"
   parameter is reflected into a file operation.
2. **Capture** a known-good request (download a legitimate file).
3. **Probe** the boundary: does a plain `../` escape? If not, *why* — what is
   the defence, and what class of bypass defeats that specific defence?
4. **Confirm** by reading something you should not be able to (the lab's flag,
   or a file above the served directory).

> 💡 Always send traversal payloads with the **path kept as-is** — tell your
> tooling *not* to normalise `../` before it leaves the client. In `curl` that
> is `--path-as-is`; in a proxy you edit the raw request directly.

### With Burp Suite

1. **Proxy** your browser through Burp (`127.0.0.1:8080`) and browse the app so
   it populates **Proxy → HTTP history**. Trigger the download/view feature.
2. Find the request carrying the file parameter and **send it to Repeater**
   (`Ctrl+R`). Repeater is where you iterate payloads by hand.
3. Establish the baseline: resend the legitimate value and confirm a 200 with
   the expected file.
4. Probe the boundary: try `../`, then adapt to the defence (e.g. encode with
   **Ctrl+U** for `%2e%2e%2f`, encode twice for `%252e%252e%252f`, or try
   `....//`). Watch status code and body change.
5. Scale it with **Intruder**: set the parameter as the payload position and
   load a traversal wordlist from
   [SecLists](https://github.com/danielmiessler/SecLists)
   (`Fuzzing/LFI/` and `Fuzzing/Path-Traversal/`). Sort by response length /
   status to spot the hit.
6. **Decoder** helps build and verify encoded payloads step by step.

### With OWASP ZAP

A fully free alternative that maps one-to-one:

1. Launch ZAP and use **Manual Explore** (Quick Start → *Launch Browser*) to
   browse through ZAP's proxy and record the app in the **Sites** tree.
2. Find the request in the **History** tab and open it in the **Request
   Editor** (right-click → *Open/Resend with Request Editor*) — ZAP's
   equivalent of Repeater.
3. Baseline the legitimate request, then edit the file parameter by hand and
   resend, adapting to the defence just like above.
4. Scale it with the **Fuzzer** (right-click the request → *Attack → Fuzz*),
   mark the parameter, and add a *File* payload from a SecLists traversal list
   (or ZAP's built-in fuzzing files). Sort results by size / code to find the
   escape.
5. ZAP's **Encoder/Decoder/Hash** add-on builds the `%2e` / double-encoded
   variants.

> Both tools also run an **active scanner**. Use it to *learn*, but remember it
> will not reliably catch the logic-specific bypasses in these labs — that is
> exactly why you are doing this by hand.

<br>

## 🧠 Teaching note: scanners ≠ logic bugs

Dependency and image scanners (Dependabot, Trivy, etc.) look for **known CVEs**
in third-party code. They will **not** flag the path traversals in this series,
because those are **business-logic flaws in first-party code** — our own broken
validation, not a vulnerable library. Catching that class of bug needs **SAST**,
**manual code review**, or **DAST** with human insight. Different tools, different
coverage. Internalising this distinction is half the value of these labs.

<br>

## Remediation (the general fix)

Reason about the **resolved** path, never the raw string:

1. **Canonicalise, then contain.** Resolve the real path and verify it still
   lives inside the allowed base directory.

   ```python
   # Python
   import os
   full = os.path.realpath(os.path.join(base_dir, user_input))
   base = os.path.realpath(base_dir)
   if os.path.commonpath([full, base]) != base:
       abort(403)
   ```

   ```php
   // PHP
   $base = realpath($baseDir);
   $full = realpath($baseDir . '/' . $userInput);
   if ($full === false || !str_starts_with($full, $base . DIRECTORY_SEPARATOR)) {
       http_response_code(403);
       return;
   }
   ```

2. **Prefer an allow-list / indirection.** Best of all, never accept raw paths:
   map an opaque id to a known file server-side, so user input never touches the
   filesystem path.
3. **Decode before you validate**, once and fully — never check a value and then
   decode it again afterwards.
4. **Defence in depth:** run the app as a non-root user with a read-only root
   filesystem and least privilege, so even a successful traversal reads as
   little as possible.

<br>

## References

- [OWASP — Path Traversal](https://owasp.org/www-community/attacks/Path_Traversal)
- [CWE-22 — Improper Limitation of a Pathname to a Restricted Directory](https://cwe.mitre.org/data/definitions/22.html)
- [OWASP Web Security Testing Guide — Testing Directory Traversal / File Include](https://owasp.org/www-project-web-security-testing-guide/)
- [PortSwigger Web Security Academy — Path traversal](https://portswigger.net/web-security/file-path-traversal)
- [SecLists — payload wordlists](https://github.com/danielmiessler/SecLists)

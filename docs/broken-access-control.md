# Broken Access Control / IDOR (CWE-639 / CWE-284) — a field guide

> Shared theory for the **Broken Access Control** lab series. Each lab's
> `GUIDE.md` links back here for the concepts and the manual-testing
> methodology, then adds only what is specific to that level. Read this first;
> it is tool- and lab-agnostic on purpose.

<br>

## What it is

**Broken access control** is the failure to enforce *what an authenticated user
is allowed to do*. It sits at the very top of the OWASP Top 10 (A01:2021).
Logging a user in answers **authentication** — "who are you?". Deciding whether
*this* user may touch *this* resource or perform *this* action is
**authorization** — a separate decision the app must make on every request. When
it skips that decision, access control is broken.

The most common concrete form is an **IDOR** (Insecure Direct Object Reference,
**CWE-639**): the app takes an object identifier straight from the request and
returns or mutates that object without checking it belongs to the caller.

```
GET /api/invoices/1042        ->  your invoice      (intended)
GET /api/invoices/1043        ->  someone else's    (IDOR: no owner check)
```

Access control breaks in two directions:

- **Horizontal** — reaching resources of *another user at your privilege level*
  (read or modify someone else's invoice, profile, order, tenant). **CWE-639.**
- **Vertical** — gaining *higher privileges* than your role grants (a regular
  user performing admin actions). **CWE-284 / CWE-269.**

<br>

## Why it happens

Almost every broken access control comes from the same root cause: **the app
authenticates the user but never authorizes the specific object or action.**

Typical broken patterns:

- **No ownership check** — fetch the object by its id and return it, trusting
  that "logged in" is enough. (Authentication ≠ authorization.)
- **Trusting client-supplied identity** — reading the acting user from a request
  field (`?userId=`, a hidden form value, a body field) instead of the
  server-side session/token.
- **Mass assignment** — binding the whole request body to the model, so the
  client can set fields it should never control (`role`, `ownerId`, `isAdmin`).
- **Missing tenant scoping** — filtering by resource id but not by the caller's
  organisation, so ids leak across tenants.
- **Enforcing authorization only in the UI** — hiding a button or route in the
  frontend while the API endpoint behind it checks nothing.
- **Trusting a manipulable token claim** — believing a `role`/`sub` claim in a
  JWT that isn't properly verified (weak secret, `alg:none`, no signature
  check).

The common theme: the server reasons about **who the user says they are** or
**what the client asks for**, instead of independently deciding **what this
authenticated principal is allowed to do with this exact object**.

<br>

## Impact

Why it matters, beyond "reading one record":

- **Mass data exposure** — a single unprotected id endpoint, enumerated in a
  loop, dumps *every* user's private data. Many real-world breaches are exactly
  this.
- **Account takeover / tampering** — write IDOR and mass assignment let an
  attacker modify others' data, change emails, or grant themselves roles.
- **Cross-tenant compromise** — in multi-tenant SaaS, missing org scoping leaks
  one customer's data to another.
- **Privilege escalation** — vertical flaws turn a normal account into an admin,
  unlocking destructive actions across the system.

Broken access control is both **common** and **high-impact**: it needs no exotic
tooling, leaves ordinary-looking logs, and one missing check can expose
everyone.

<br>

## Manual analysis methodology

Scanners rarely find these: whether a given object *should* be reachable by a
given user is business logic a tool can't infer. The reliable method is to test
**with (at least) two identities** and compare.

1. **Create/seed two accounts** — e.g. user A (you) and user B (the victim), and
   where relevant an admin. Keep a session for each.
2. **Map** every endpoint that takes an object id, a user/tenant reference, or
   performs a privileged action. Note how identity is carried (cookie, token).
3. **Baseline as the owner** — as A, perform the legitimate action on A's own
   object and capture the request.
4. **Swap the reference** — replay that request *as A* but pointing at **B's**
   object id (or add/forge a client-controlled identity field). If B's data
   comes back, or B's object changes, that's a horizontal IDOR.
5. **Escalate** — as a low-privilege user, call an admin-only endpoint directly
   (bypassing the UI), or tamper with a role claim, and see if it's honoured.
   That's vertical escalation.
6. **Confirm** by reading or altering something you should not be able to (the
   lab's flag, which lives on another identity's resource).

> 💡 The core move is **request replay across identities**: take a request that
> works for its rightful owner and send it, unchanged except for the object
> reference, from a different account. Authorization is broken whenever the
> answer isn't a clean "denied".

### With Burp Suite

1. **Proxy** your browser through Burp (`127.0.0.1:8080`) and log in as user A;
   browse so **Proxy → HTTP history** fills up. Trigger the object/action.
2. Find the request carrying the object id (or identity field) and **send it to
   Repeater** (`Ctrl+R`).
3. Baseline it as A against A's own id and confirm the expected 200.
4. Change *only* the id to B's and resend. A different owner's data (or a
   successful write) confirms the IDOR. For vertical flaws, replay an
   admin-only request from A's session.
5. Scale with **Intruder**: mark the id as the payload position and run a
   numeric range (or a UUID list). Sort by status / length to harvest every
   reachable object.
6. **Burp's "Autorize" extension** (BApp Store) automates exactly this: it
   repeats each request with a second account's session and flags responses that
   aren't properly denied — the fastest way to sweep for access-control gaps.

### With OWASP ZAP

A fully free alternative that maps one-to-one:

1. Use **Manual Explore** (Quick Start → *Launch Browser*) to browse as user A
   through ZAP's proxy and record the app in the **Sites** tree.
2. Find the request in **History** and open it in the **Request Editor**
   (right-click → *Open/Resend with Request Editor*) — ZAP's Repeater.
3. Baseline as A, then edit the object id to B's (or add the identity field) and
   resend. Compare responses.
4. Scale with the **Fuzzer** (right-click → *Attack → Fuzz*): mark the id and
   add a numeric/UUID payload set; sort by size / code to find reachable
   records.
5. For session swapping, configure a second **context/user** in ZAP and use
   **Access Control testing** (Contexts → *Access Control*) to replay requests
   under each user and flag authorization failures.

> Both tools also run an **active scanner**. Use it to *learn*, but remember it
> will not reliably decide *who should* access *what* — that is business logic,
> and exactly why you test by hand with multiple identities.

<br>

## 🧠 Teaching note: scanners ≠ logic bugs

Dependency and image scanners (Dependabot, Trivy, etc.) look for **known CVEs**
in third-party code. They will **not** flag the access-control flaws in this
series, because those are **business-logic flaws in first-party code** — a
missing ownership check, a trusted client field — not a vulnerable library.
Catching that class of bug needs **SAST**, **manual code review**, or **DAST**
with multiple identities and human insight. Different tools, different coverage.
Internalising this distinction is half the value of these labs.

<br>

## Remediation (the general fix)

Decide authorization on the server, for every object and every action:

1. **Authorize every access, not just authenticate.** After resolving the
   object, verify the caller may act on it — using the **server-side** identity,
   never a client-supplied one.

   ```ts
   // Node / TypeScript — object-level ownership check
   const invoice = await repo.findById(id);
   if (!invoice || invoice.ownerId !== req.session.userId) {
     // 404 (not 403) so you don't even reveal the id exists
     return res.status(404).json({ error: 'not found' });
   }
   ```

2. **Scope at the source.** Put the identity into the query itself
   (`WHERE id = :id AND owner_id = :me`, or `AND org_id = :tenant`) so a
   forgotten check in the handler can't leak data.
3. **Never trust the client for identity or privilege.** Derive the acting user
   and their role from the session/verified token — not from a request field,
   hidden input, or an unverified JWT claim. Verify token signatures and reject
   `alg:none`.
4. **Guard writes against mass assignment.** Accept an explicit allow-list of
   fields; never bind the raw body to the model.
5. **Enforce on the server, not the UI.** Hiding a control is UX, not security —
   the endpoint behind it must check the role.
6. **Deny by default & centralise.** Make the default answer "no", and put
   authorization in middleware / a policy layer so it is applied uniformly
   rather than re-implemented (and forgotten) per route. Add tests that replay
   requests across identities so a regression fails the build.

<br>

## References

- [OWASP — A01:2021 Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
- [CWE-639 — Authorization Bypass Through User-Controlled Key (IDOR)](https://cwe.mitre.org/data/definitions/639.html)
- [CWE-284 — Improper Access Control](https://cwe.mitre.org/data/definitions/284.html)
- [OWASP Web Security Testing Guide — Authorization Testing](https://owasp.org/www-project-web-security-testing-guide/)
- [PortSwigger Web Security Academy — Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control)
- [PortSwigger — IDOR](https://portswigger.net/web-security/access-control/idor)

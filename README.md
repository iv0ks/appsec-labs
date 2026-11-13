## AppSec Labs

![Docker](https://img.shields.io/badge/Lab-Docker-2496ED?style=flat&logo=docker&logoColor=white)
![CWE-22](https://img.shields.io/badge/CWE--22-Path%20Traversal-red?style=flat)
![CWE-284](https://img.shields.io/badge/CWE--284-Improper%20Access%20Control-red?style=flat)
![CWE-639](https://img.shields.io/badge/CWE--639-IDOR-red?style=flat)

A growing series of **intentionally vulnerable web apps** for hands-on web security practice. Each lab is a realistic, named "product" — not an obvious
exercise — shipped as its own public repo: **dockerized** and published to **GHCR**.

<br>

> [!WARNING]
> Run them **only locally and isolated** — never expose them to the internet, and **never use these techniques against systems without explicit**, written **authorization**.

<br>

The goal is to practice finding and exploiting a specific vulnerability class in an environment that looks and behaves like real software, then read the writeup
to understand both the exploit **and** the fix.

<br>

### 🧪 Series 1 — Path Traversal (CWE-22)

Six apps, one vulnerability class, increasing difficulty. Each hides a path
traversal behind a progressively more convincing (but still broken) defence.

Each repo ships with a `SOLUTION.md` walkthrough (spoilers) and a flag of the form `FLAG{...}` to capture.

<br>

<div align="center">

| #   | Lab                                             |                                                       Stack                                                       | Difficulty | The broken defence                             |
| --- | ----------------------------------------------- | :--------------------------------------------------------------------------------------------------------------: | ---------- | ---------------------------------------------- |
| 1   | [fetchly](https://github.com/iv0ks/fetchly)     |         ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)                |     ⭐     | No filter at all — direct traversal            |
| 2   | [snaptrail](https://github.com/iv0ks/snaptrail) |         ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)                |    ⭐⭐    | A forced base-path prefix                      |
| 3   | [deskmint](https://github.com/iv0ks/deskmint)   |         ![PHP](https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white)                      |   ⭐⭐⭐   | A non-recursive `../` strip (bypass: `....//`) |
| 4   | [medialoft](https://github.com/iv0ks/medialoft) |         ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)                |   ⭐⭐⭐   | A file-extension check (`.png`)                |
| 5   | [ziphaven](https://github.com/iv0ks/ziphaven)   |         ![PHP](https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white)                      |  ⭐⭐⭐⭐  | A filter that runs **before** URL-decoding     |
| 6   | [brewdocs](https://github.com/iv0ks/brewdocs)   |         ![PHP](https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white)                      |  ⭐⭐⭐⭐  | A start-of-path check without normalisation    |

</div>

<br>

> [!TIP]
> Start with the [Path Traversal](docs/path-traversal.md) field guide — what the bug is, why it happens, and how to test for it manually with `Burp Suite` and `OWASP ZAP`. Each lab's `GUIDE.md` builds on it.

<br>

### 🧪 Series 2 — Broken Access Control / IDOR (CWE-639 / CWE-284)

Ten apps, one vulnerability class, increasing difficulty — from reading another
user's record to full role escalation. Broken access control has **two
independent dimensions**, and the series covers both: *what* the broken access
lets you do (read, write, cross-tenant, impersonate, escalate) **and** *how* the
object is referenced (sequential, UUID, base64-encoded, hashids — the id format
is the attack technique, never the control). Built in the **Node** ecosystem
(TypeScript), each lab seeds a set of identities so "accessing what isn't yours"
is a concrete, capturable goal.

The IDOR labs are **CWE-639**; the vertical privilege-escalation labs
(opsdeck, vaultpass) are **CWE-284**. Each repo ships a `SOLUTION.md` walkthrough
and a `FLAG{...}` to capture from a resource that belongs to someone else.

<br>

<div align="center">

| #   | Lab                                             |                                                        Stack                                                        | Difficulty | The flaw                                                   |
| --- | ----------------------------------------------- | :-----------------------------------------------------------------------------------------------------------------: | ---------- | ---------------------------------------------------------- |
| 1   | [ledgerly](https://github.com/iv0ks/ledgerly)   |       ![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)               | ⭐         | Read IDOR — no ownership check on `GET /invoices/:id` (sequential id) |
| 2   | [pingboard](https://github.com/iv0ks/pingboard) |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐       | Write IDOR / mass assignment on user update (sequential id) |
| 3   | [keyvault](https://github.com/iv0ks/keyvault)   |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐       | Read IDOR on **UUID**-keyed resources — unguessable ≠ authorized |
| 4   | [snapvault](https://github.com/iv0ks/snapvault) |       ![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)               | ⭐⭐⭐     | Write IDOR on **base64**-encoded handles — decode, alter, re-encode |
| 5   | [couponly](https://github.com/iv0ks/couponly)   |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐     | IDOR on **hashids**-obfuscated ids — forge valid references |
| 6   | [tasknest](https://github.com/iv0ks/tasknest)   |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐     | Missing tenant isolation (cross-org access)                |
| 7   | [cartora](https://github.com/iv0ks/cartora)     |       ![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white)             | ⭐⭐⭐     | Server trusts a client-supplied identity (body/cookie)     |
| 8   | [opsdeck](https://github.com/iv0ks/opsdeck)     |       ![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white)             | ⭐⭐⭐⭐   | Vertical escalation — role check only in the UI            |
| 9   | [vaultpass](https://github.com/iv0ks/vaultpass) |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐⭐   | Broken auth trust — a manipulable JWT claim                |
| 10  | [mirrorbox](https://github.com/iv0ks/mirrorbox) |       ![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white)             | ⭐⭐⭐⭐   | Capstone — same resource under four id formats; the disguise was never the control |

</div>

<br>

> [!TIP]
> Start with the [Broken Access Control field guide](docs/broken-access-control.md) — what
> the bug is, authentication vs authorization, and how to test for it
> methodically with two accounts using Burp Suite and OWASP ZAP. Each lab's
> `GUIDE.md` builds on it.

<br>

### 🧪 Series 3 — SQL Injection (CWE-89)

Eight apps, one vulnerability class, increasing difficulty. SQL injection has
**two independent dimensions**, and the series covers both: the *technique* you
use to extract data or act (in-band, UNION, blind boolean, time-based, second
order, out-of-band) **and** the *context* where the input lands in the query
(string, numeric, `ORDER BY`/identifier, login, stored). Built in the **Node**
ecosystem on **PostgreSQL**; schema and seed use Drizzle, while the vulnerable
endpoints deliberately drop to raw concatenated SQL — because an ORM does not
save you once you hand-build query strings.

Each repo ships a `SOLUTION.md` walkthrough and a `FLAG{...}` to extract from a
row the low-privilege user should never be able to read.

<br>

<div align="center">

| #   | Lab                                               |                                                        Stack                                                        | Difficulty | The flaw                                                   |
| --- | ------------------------------------------------- | :-----------------------------------------------------------------------------------------------------------------: | ---------- | ---------------------------------------------------------- |
| 1   | [shelfwise](https://github.com/iv0ks/shelfwise)   |       ![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)               | ⭐         | In-band / error-based — break the quote in a `WHERE` string |
| 2   | [tuneloop](https://github.com/iv0ks/tuneloop)     |       ![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)               | ⭐⭐       | UNION-based — reconstruct data with `UNION SELECT`         |
| 3   | [gatecheck](https://github.com/iv0ks/gatecheck)   |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐       | Authentication bypass — the classic `' OR 1=1 --` login    |
| 4   | [ordernest](https://github.com/iv0ks/ordernest)   |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐     | Injection in `ORDER BY` — where prepared statements don't help |
| 5   | [pulsepoll](https://github.com/iv0ks/pulsepoll)   |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐     | Boolean-blind — infer data bit-by-bit from true/false      |
| 6   | [chronark](https://github.com/iv0ks/chronark)     |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐⭐   | Time-based blind — infer via `pg_sleep` delays             |
| 7   | [echobin](https://github.com/iv0ks/echobin)       |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐⭐   | Second-order — stored payload detonates in a later query   |
| 8   | [tunnelcast](https://github.com/iv0ks/tunnelcast) |       ![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)                  | ⭐⭐⭐⭐   | Capstone — out-of-band exfiltration to a bundled listener  |

</div>

<br>

> [!TIP]
> Start with the [SQL Injection field guide](docs/sql-injection.md) — what the
> bug is, why parameterization is the fix, the technique ladder, the injection
> contexts (including where prepared statements can't help), and per-engine
> syntax notes. Each lab's `GUIDE.md` builds on it.

<br>

> The series below are **planned** — the design, stacks and lab names are fixed
> (see [`APPSEC_LABS_ROADMAP.md`](APPSEC_LABS_ROADMAP.md)); the repos ship
> progressively. Links will go live as each lab is published.

<br>

### 🧭 Series 4 — Cross-Site Scripting (XSS) · _planned_

Eight apps covering **all four canonical XSS types** — reflected
(non-persistent), stored (persistent), DOM-based and blind — across the sink
contexts where each lives (HTML body, attribute, JavaScript, client DOM, and a
framework that auto-escapes until you bypass it). Built on **Express** for the
classic server-rendered cases and **Next.js/React** for the DOM and
`dangerouslySetInnerHTML` labs; the blind lab ships a mini-listener to catch the
out-of-band callback. The capstone chains a stored payload into admin session
theft.

<br>

<div align="center">

| #   | Lab        | Stack   | Difficulty | The flaw                                                   |
| --- | ---------- | :-----: | ---------- | ---------------------------------------------------------- |
| 1   | searchlit  | Express | ⭐         | Reflected — the search term reflects raw into the HTML body |
| 2   | profilard  | Express | ⭐⭐       | Reflected — value in an HTML attribute; break out of the quote |
| 3   | commentor  | Express | ⭐⭐       | Stored — a comment served unsanitised to every visitor     |
| 4   | boardly    | Next.js | ⭐⭐⭐     | DOM-based — client writes `location.hash` into `innerHTML` |
| 5   | scriptor   | Express | ⭐⭐⭐     | Reflected into a JavaScript string — break the script context |
| 6   | markhive   | Next.js | ⭐⭐⭐⭐   | Bypass — naive sanitiser + `dangerouslySetInnerHTML`       |
| 7   | inkwell    | NestJS  | ⭐⭐⭐⭐   | Blind — injected blindly, detonates in an unseen admin panel |
| 8   | relaybox   | Next.js | ⭐⭐⭐⭐   | Capstone — stored XSS → admin session theft                |

</div>

<br>

### 🧭 Series 5 — Command Injection · _planned_

Six apps where user input reaches an OS command. Two axes: the **technique**
(in-band, blind time-based, out-of-band) and the **context** (direct
concatenation, a quoted argument, a metacharacter blocklist you bypass). Built
on **Flask (Python)** and **PHP** — the stacks where this flaw is most
idiomatic and most faithful to how it appears in the wild (`subprocess`,
`os.system`, `shell_exec`); the blind lab ships a mini-listener for the
out-of-band callback.

<br>

<div align="center">

| #   | Lab        | Stack   | Difficulty | The flaw                                             |
| --- | ---------- | :-----: | ---------- | ---------------------------------------------------- |
| 1   | pingpanel  | Flask   | ⭐         | Direct concatenation into a shell command (`; id`)   |
| 2   | convertr   | PHP     | ⭐⭐       | Quoted argument — break out of the quote             |
| 3   | backuply   | Flask   | ⭐⭐⭐     | Blind, time-based (`sleep`) — no command output      |
| 4   | netprobe   | Flask   | ⭐⭐⭐     | Blind, out-of-band to a bundled listener             |
| 5   | zipcraft   | PHP     | ⭐⭐⭐⭐   | `shell_exec` behind a metacharacter blocklist bypass |
| 6   | shellbox   | PHP     | ⭐⭐⭐⭐   | Capstone — chained bypass to full RCE                |

</div>

<br>

### 🧭 Series 6 — File Inclusion (LFI/RFI) · _planned_

Five apps on local and remote file inclusion — the close cousin of path
traversal, but where the included file is **executed**, not just read. Built in
**PHP**, where the wrappers (`php://filter`, `data://`, `php://input`) and log
poisoning are idiomatic, with one **Laravel** lab for a dynamic-view include.

<br>

<div align="center">

| #   | Lab          | Stack   | Difficulty | The flaw                                            |
| --- | ------------ | :-----: | ---------- | --------------------------------------------------- |
| 1   | docwell      | PHP     | ⭐         | Basic local file inclusion                          |
| 2   | loglect      | PHP     | ⭐⭐       | LFI + `php://filter` to read source                 |
| 3   | includr      | PHP     | ⭐⭐⭐     | LFI → RCE via `data://` / `php://input`             |
| 4   | templor      | Laravel | ⭐⭐⭐     | Dynamic view/include resolves attacker input        |
| 5   | remotefetch  | PHP     | ⭐⭐⭐⭐   | Log poisoning → RCE / remote file inclusion          |

</div>

<br>

### 🧭 Series 7 — Server-Side Request Forgery (SSRF) · _planned_

Seven apps where the server fetches a URL you control. Two axes: the **impact**
(reach an internal endpoint, scan the internal network, steal cloud metadata and
its IAM token) and the **defence bypass** (redirect, IP-encoding, DNS). Covers
both **regular** and **blind** SSRF. Built on **NestJS** — the flaw is entirely
server-side — with extra internal containers in compose (an internal service
holding real data, a metadata mock) and a mini-listener for the blind capstone.

<br>

<div align="center">

| #   | Lab         | Stack   | Difficulty | The flaw                                            |
| --- | ----------- | :-----: | ---------- | --------------------------------------------------- |
| 1   | linkpeek    | NestJS  | ⭐         | Fetches an arbitrary user-supplied URL              |
| 2   | thumblr     | NestJS  | ⭐⭐       | Reach an internal-only service not exposed publicly |
| 3   | portpeek    | NestJS  | ⭐⭐       | Internal network reconnaissance / port scan         |
| 4   | metastore   | NestJS  | ⭐⭐⭐     | Cloud metadata theft + IAM token (169.254.169.254)  |
| 5   | webhookr    | NestJS  | ⭐⭐⭐     | Allow-list bypass via an open redirect              |
| 6   | fetchgate   | NestJS  | ⭐⭐⭐⭐   | Bypass via IP-encoding / DNS tricks                 |
| 7   | tunnelgate  | NestJS  | ⭐⭐⭐⭐   | Capstone — blind SSRF to a bundled listener         |

</div>

<br>

### 🧭 Series 8 — Cross-Site Request Forgery (CSRF) · _planned_

Six apps on forging state-changing requests from another origin, each targeting
a concrete high-impact feature — change email, preferences, **update password**,
**update/alter payment info**, edit profile. Built mainly on **Laravel** — which
ships CSRF protection by default — to teach exactly how it breaks when you except
a route, leave a token unvalidated or predictable, weaken `SameSite`, or trust
the `Referer`; plus **Express** for the no-framework extremes. Each ships a
`poc.html` attacker page on a separate origin, using varied vectors (GET `<img>`,
POST `<form>`, cross-site `fetch`).

<br>

<div align="center">

| #   | Lab        | Stack   | Difficulty | The flaw                                            |
| --- | ---------- | :-----: | ---------- | --------------------------------------------------- |
| 1   | tokenless  | Express | ⭐         | Change email — no CSRF token at all (GET `<img>`)   |
| 2   | prefsync   | Laravel | ⭐⭐       | Preferences — token present but the route is excepted |
| 3   | passpivot  | Laravel | ⭐⭐⭐     | Update password — token not validated → takeover    |
| 4   | fundflow   | Laravel | ⭐⭐⭐     | Update payment info — predictable / global token    |
| 5   | samesiter  | Laravel | ⭐⭐⭐     | Edit profile — weak `SameSite` cookie configuration |
| 6   | originbend | Express | ⭐⭐⭐⭐   | Capstone — alter payment info via `Referer`/`Origin` bypass |

</div>

<br>

### 🧭 Series 9 — Broken Authentication · _planned_

Eight apps on failures to **establish** identity — brute force, user
enumeration, broken password reset, insecure "remember me", unverified JWTs, and
**two kinds of MFA bypass** (an unenforced second step, and a brute-forceable
OTP). Built on **Laravel** (a realistic login we then break) and **NestJS**. The
capstone chains enumeration and a reset flaw into a full account takeover.

<br>

<div align="center">

| #   | Lab        | Stack   | Difficulty | The flaw                                            |
| --- | ---------- | :-----: | ---------- | --------------------------------------------------- |
| 1   | gatewayr   | Laravel | ⭐         | No rate-limit — brute force / credential stuffing   |
| 2   | enumerate  | NestJS  | ⭐⭐       | User enumeration via response/timing oracle         |
| 3   | forgepass  | Laravel | ⭐⭐⭐     | Broken password reset (predictable / no-expiry token) |
| 4   | remembr    | NestJS  | ⭐⭐⭐     | Insecure "remember me" token                        |
| 5   | nonejwt    | NestJS  | ⭐⭐⭐⭐   | JWT `alg:none` / signature not verified             |
| 6   | secondgate | Laravel | ⭐⭐⭐⭐   | MFA bypass — second factor not enforced server-side |
| 7   | otpflood   | NestJS  | ⭐⭐⭐⭐   | MFA bypass — OTP brute force (no rate-limit)         |
| 8   | takeovr    | Laravel | ⭐⭐⭐⭐   | Capstone — enumeration → reset → account takeover   |

</div>

<br>

### 🧭 Series 10 — Session Management · _planned_

Five apps on failures to **maintain** an established session — missing cookie
flags, fixation, predictable identifiers, and broken invalidation on
logout/expiry. Built on **Express/NestJS** (which expose cookie config and id
rotation explicitly) and **Laravel**. The capstone chains them into a full
session hijack.

<br>

<div align="center">

| #   | Lab         | Stack   | Difficulty | The flaw                                            |
| --- | ----------- | :-----: | ---------- | --------------------------------------------------- |
| 1   | cookiejar   | Express | ⭐         | Missing cookie flags (`HttpOnly`/`Secure`/`SameSite`) |
| 2   | fixate      | Express | ⭐⭐       | Session fixation — id not rotated on login          |
| 3   | predictid   | NestJS  | ⭐⭐⭐     | Predictable session identifier                      |
| 4   | staleport   | Laravel | ⭐⭐⭐     | Logout / expiry does not invalidate the session     |
| 5   | ghostlogin  | NestJS  | ⭐⭐⭐⭐   | Capstone — full session-hijack chain                |

</div>

<br>

> [!NOTE]
> Series 9 (*establishing* identity) and 10 (*maintaining* the session) are kept
> separate on purpose. Stacks are chosen per vulnerability — PHP/Laravel where
> the flaw is idiomatic to that ecosystem, Node elsewhere.

<br>

## 🚀 Running a lab

Every lab is a self-contained Docker Compose stack. In short:

```bash
git clone https://github.com/iv0ks/<lab>.git
cd <lab>
docker network create iv0ks-dev   # once, shared across labs
docker compose up -d --build <lab>
```

See each repo's README for the exact port and the challenge brief.

<br>
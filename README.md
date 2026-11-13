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
| 5   | [pulsepoll](https://github.com/iv0ks/pulsepoll)   |       ![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white)             | ⭐⭐⭐     | Boolean-blind — infer data bit-by-bit from true/false      |
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

## 🗺️ Roadmap

Seven more series are **planned**, each a separate vulnerability class under the
same `appsec-lab` topic (full detail and reserved lab names in
[`APPSEC_LABS_ROADMAP.md`](APPSEC_LABS_ROADMAP.md)):

| #   | Series                         | Root CWE          | Stacks                       | Labs |
| --- | ------------------------------ | ----------------- | ---------------------------- | ---- |
| 4   | **Cross-Site Scripting (XSS)** | CWE-79            | Express + Next.js            | 7    |
| 5   | **Command Injection**          | CWE-78 / CWE-77   | Node (+ PHP)                 | 6    |
| 6   | **File Inclusion (LFI/RFI)**   | CWE-98            | Laravel + plain PHP          | 5    |
| 7   | **SSRF**                       | CWE-918           | NestJS / Next.js + listener  | 6    |
| 8   | **CSRF**                       | CWE-352           | Laravel (+ Express)          | 5    |
| 9   | **Broken Authentication**      | CWE-287           | Laravel + Node               | 6    |
| 10  | **Session Management**         | CWE-384 / CWE-613 | Node + Laravel               | 5    |

Series 9 (*establishing* identity) and 10 (*maintaining* the session) are kept
separate on purpose. Stacks are chosen per vulnerability — PHP/Laravel where the
flaw is idiomatic to that ecosystem (file inclusion wrappers, framework CSRF
tokens), Node elsewhere.

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
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

## 🗺️ Roadmap

New series land as separate labs under the same `appsec-lab` topic:

- **SQL Injection**
- **Cross-Site Scripting (XSS)**
- **Authentication & Session flaws**

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
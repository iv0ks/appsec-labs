## AppSec Labs

![Docker](https://img.shields.io/badge/Lab-Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![CWE-22](https://img.shields.io/badge/CWE--22-Path%20Traversal-red?style=flat-square)
![CWE-284](https://img.shields.io/badge/CWE--284-Improper%20Access%20Control-red?style=flat-square)
![CWE-639](https://img.shields.io/badge/CWE--639-IDOR-red?style=flat-square)

A growing series of **intentionally vulnerable web apps** for hands-on web security practice. Each lab is a realistic, named "product" — not an obvious
exercise — shipped as its own public repo: **dockerized** and published to **GHCR**.

The goal is to practice finding and exploiting a specific vulnerability class in an environment that looks and behaves like real software, then read the writeup
to understand both the exploit **and** the fix.

<br>

> [!NOTE]
> Find them all on GitHub under the **[`appsec-lab`](https://github.com/topics/appsec-lab)** topic.

<br>

### 🧪 Series 1 — Path Traversal (CWE-22)

Six apps, one vulnerability class, increasing difficulty. Each hides a path
traversal behind a progressively more convincing (but still broken) defence.

Each repo ships with a `SOLUTION.md` walkthrough (spoilers) and a flag of the form `FLAG{...}` to capture.

<br>

<div align="center">

| #   | Lab                                             | Stack | Difficulty | The broken defence                             |
| --- | ----------------------------------------------- | ----- | ---------- | ---------------------------------------------- |
| 1   | [fetchly](https://github.com/iv0ks/fetchly)     | Flask | ⭐         | No filter at all — direct traversal            |
| 2   | [snaptrail](https://github.com/iv0ks/snaptrail) | Flask | ⭐⭐       | A forced base-path prefix                      |
| 3   | [deskmint](https://github.com/iv0ks/deskmint)   | PHP   | ⭐⭐⭐     | A non-recursive `../` strip (bypass: `....//`) |
| 4   | [medialoft](https://github.com/iv0ks/medialoft) | Flask | ⭐⭐⭐     | A file-extension check (`.png`)                |
| 5   | [ziphaven](https://github.com/iv0ks/ziphaven)   | PHP   | ⭐⭐⭐⭐   | A filter that runs **before** URL-decoding     |
| 6   | [brewdocs](https://github.com/iv0ks/brewdocs)   | PHP   | ⭐⭐⭐⭐   | A start-of-path check without normalisation    |

</div>

<br>

> [!TIP]
> Start with the [Path Traversal](docs/path-traversal.md) field guide — what the bug is, why it happens, and how to test for it manually with `Burp Suite` and `OWASP ZAP`. Each lab's `GUIDE.md` builds on it.

<br>

## 🧪 Series 2 — Broken Access Control / IDOR (CWE-639 / CWE-284)

Six apps, one vulnerability class, increasing difficulty — from reading another user's record to full role escalation. Horizontal IDOR first (levels 1–4), then
vertical privilege escalation (levels 5–6). Built in the **Node** ecosystem (TypeScript), each with a seeded set of identities so "accessing what isn't
yours" is a concrete, capturable goal.

> 📖 **New to the topic?** Start with the
> **[Broken Access Control field guide](docs/broken-access-control.md)** — what
> the bug is, authentication vs authorization, and how to test for it
> methodically with two accounts using Burp Suite and OWASP ZAP. Each lab's
> `GUIDE.md` builds on it.

| #   | Lab                                             | Stack          | Difficulty | The flaw                                              |
| --- | ----------------------------------------------- | -------------- | ---------- | ----------------------------------------------------- |
| 1   | [ledgerly](https://github.com/iv0ks/ledgerly)   | Node · Express | ⭐         | read IDOR — no ownership check on `GET /invoices/:id` |
| 2   | [pingboard](https://github.com/iv0ks/pingboard) | Node · NestJS  | ⭐⭐       | write IDOR / mass assignment on user update           |
| 3   | [tasknest](https://github.com/iv0ks/tasknest)   | Node · NestJS  | ⭐⭐⭐     | missing tenant isolation (cross-org access)           |
| 4   | [cartora](https://github.com/iv0ks/cartora)     | Next.js        | ⭐⭐⭐     | server trusts a client-supplied identity              |
| 5   | [opsdeck](https://github.com/iv0ks/opsdeck)     | Next.js        | ⭐⭐⭐⭐   | vertical escalation — role check only in the UI       |
| 6   | [vaultpass](https://github.com/iv0ks/vaultpass) | Node · NestJS  | ⭐⭐⭐⭐   | broken auth trust — a manipulable JWT claim           |

Levels 1–4 are **horizontal** IDOR (CWE-639); levels 5–6 are **vertical**
privilege escalation (CWE-284). Each repo ships a `SOLUTION.md` walkthrough and
a `FLAG{...}` to capture from a resource that belongs to someone else.

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

## ⚠️ Disclaimer

These applications are **intentionally vulnerable** and built for education.
Run them only locally and isolated — **never** expose them to the internet, and
never use these techniques against systems without explicit, written
authorization.

<br>

## 📄 License

[MIT](LICENSE)

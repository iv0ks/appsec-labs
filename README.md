# AppSec Labs

A growing series of **intentionally vulnerable web apps** for hands-on web
security practice. Each lab is a realistic, named "product" — not an obvious
exercise — shipped as its own public repo: **dockerized**, published to
**GHCR**, and wrapped in a full **DevSecOps** pipeline (CI/CD, dependency and
image scanning, linters, SemVer releases).

The goal is to practice finding and exploiting a specific vulnerability class in
an environment that looks and behaves like real software, then read the writeup
to understand both the exploit **and** the fix.

> 🔎 Find them all on GitHub under the **[`appsec-lab`](https://github.com/topics/appsec-lab)** topic.

<br>

## 🧪 Series 1 — Path Traversal (CWE-22)

Six apps, one vulnerability class, increasing difficulty. Each hides a path
traversal behind a progressively more convincing (but still broken) defence.

| # | Lab | Stack | Difficulty | The broken defence |
|---|-----|-------|------------|--------------------|
| 1 | [fetchly](https://github.com/iv0ks/fetchly)     | Flask | ⭐ | no filter at all — direct traversal |
| 2 | [snaptrail](https://github.com/iv0ks/snaptrail) | Flask | ⭐⭐ | a forced base-path prefix |
| 3 | [deskmint](https://github.com/iv0ks/deskmint)   | PHP   | ⭐⭐⭐ | a non-recursive `../` strip (bypass: `....//`) |
| 4 | [medialoft](https://github.com/iv0ks/medialoft) | Flask | ⭐⭐⭐ | a file-extension check (`.png`) |
| 5 | [ziphaven](https://github.com/iv0ks/ziphaven)   | PHP   | ⭐⭐⭐⭐ | a filter that runs **before** URL-decoding |
| 6 | [brewdocs](https://github.com/iv0ks/brewdocs)   | PHP   | ⭐⭐⭐⭐ | a start-of-path check without normalisation |

Each repo ships with a `SOLUTION.md` walkthrough (spoilers) and a flag of the
form `FLAG{...}` to capture.

<br>

## 🗺️ Roadmap

New series land as separate labs under the same `appsec-lab` topic:

- **IDOR / Broken Access Control**
- **SQL Injection**
- **Cross-Site Scripting (XSS)**
- **Authentication & Session flaws**

<br>

## 🧰 What every lab has in common

- A **believable product UI** (its own identity, not a generic test page) so the
  bug sits in realistic code.
- Decoy data in the "allowed" area and a target (`flag.txt` / `flag.png`)
  **outside** it — the flag is baked into the Docker image at build time, never
  committed.
- **Hardened container**: non-root, OS patch layer, healthcheck.
- **CI/CD** via reusable workflows: secret scan → dependency/IaC scan → lint →
  tests on PRs; build & push to GHCR on release; scheduled CVE scans.
- A `SOLUTION.md` that explains the exploit **and** the correct fix.

> 💡 **Teaching note:** dependency/image scanners catch *known CVEs*. They do
> **not** flag these vulnerabilities, because they are business-logic flaws in
> first-party code. Catching that class of bug needs SAST / manual review / DAST
> — which is exactly what these labs are for.

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

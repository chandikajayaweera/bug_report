# better-auth CLI Bug Report Repo

This repository is a minimal reproduction case for a bug in the `@better-auth/cli` `generate` command, where the CLI fails to import internal exports from `better-auth/db` across versions `1.4.6` through `1.4.20+`.

> 🐛 **Bug filed:** [better-auth GitHub Issues](https://github.com/better-auth/better-auth/issues)

---

## Overview

Running `pnpm dlx @better-auth/cli@[version] generate ...` fails with a `SyntaxError` for any CLI version from `1.4.6` onwards. The CLI attempts to import internal exports (`getAdapter`, `getMigrations`) from `better-auth/db` that no longer exist in the installed version of `better-auth`.

The last known working CLI version is **`1.4.5`**.

---

## Reproduction Steps

**Prerequisites:** Node.js v22, pnpm v10

1. Clone this repo:

   ```sh
   git clone https://github.com/chandikajayaweera/bug_report
   cd bug_report
   ```

2. Install dependencies:

   ```sh
   pnpm install
   ```

3. Run the schema generation with a broken CLI version:

   ```sh
   pnpm dlx @better-auth/cli@1.4.20 generate --config src/lib/server/auth.ts --output src/lib/server/db/auth.schema.ts --yes
   ```

4. Observe the `SyntaxError`. To confirm the regression, run the same command with `@1.4.5` — it succeeds.

---

## Version Matrix

| CLI Version          | Result   | Error                                              |
| -------------------- | -------- | -------------------------------------------------- |
| `1.4.5`              | ✅ Works | —                                                  |
| `1.4.6` – `1.4.13`   | ❌ Fails | `getAdapter` not exported from `better-auth/db`    |
| `1.4.14` – `1.4.20+` | ❌ Fails | `getMigrations` not exported from `better-auth/db` |

---

## Error Output

### Versions 1.4.14 – 1.4.20

```
SyntaxError: The requested module 'better-auth/db' does not provide an export named 'getMigrations'
    at ModuleJob._instantiate (node:internal/modules/esm/module_job:226:21)
```

### Versions 1.4.6 – 1.4.13

```
SyntaxError: The requested module 'better-auth/db' does not provide an export named 'getAdapter'
    at ModuleJob._instantiate (node:internal/modules/esm/module_job:226:21)
```

---

## Environment

- **Node.js:** v22.21.0
- **pnpm:** v10.19.0
- **better-auth:** `^1.4.18` (installed: `1.4.18`)
- **OS:** Windows 11
- **Framework:** SvelteKit (Cloudflare adapter)

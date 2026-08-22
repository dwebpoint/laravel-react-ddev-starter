# Laravel 13 + React

A Laravel + React application skeleton, built on Laravel's official [React starter kit](https://laravel.com/docs/starter-kits) and run locally via [DDEV](https://ddev.com).

## Stack

- **Laravel 13** — PHP framework
- **Inertia.js 3** + **React 19** + **TypeScript** — SPA-style frontend without a separate API layer
- **Tailwind CSS 4** + [shadcn/ui](https://ui.shadcn.com) / [radix-ui](https://www.radix-ui.com) — component styling
- **Vite 8** — asset bundling and dev server (HMR)
- **Laravel Fortify** — authentication backend (login, registration, password reset, email verification, two-factor auth, passkeys)
- **Laravel Wayfinder** — generates typed TS functions for routes/controller actions, used instead of Ziggy
- **spatie/laravel-permission** — roles and permissions (`HasRoles` trait on `User`, `role` / `permission` / `role_or_permission` middleware)
- **Laravel Boost** — dev-only package exposing an MCP server plus AI coding guidelines/skills for Claude Code (`CLAUDE.md`, `.claude/skills/`)
- **MariaDB 11.4** — database

## Local environment (DDEV)

The project runs entirely inside DDEV containers — PHP 8.4, Node 22, MariaDB 11.4, nginx-fpm.

```bash
ddev start                 # boot the containers (also starts the Vite dev server)
ddev composer install      # PHP dependencies
ddev exec npm install       # JS dependencies
ddev artisan migrate        # run migrations
```

The app is then available at **https://laravel-react.ddev.site**.

### Vite dev server

`web_extra_exposed_ports` in `.ddev/config.yaml` exposes port 5173 through the DDEV router for HMR, and `vite.config.ts` reads `DDEV_PRIMARY_URL` so the dev server's HMR client connects back to the right host over HTTPS.

`web_extra_daemons` runs `npm run dev` as a supervised process inside the web container, so it starts automatically on `ddev start`/`ddev restart` — no manual step needed. If you ever see a 502 on port 5173 (e.g. right after `npm install` wipes and rebuilds `node_modules`), just restart the daemon:

```bash
ddev restart
```

For a production-like build instead: `ddev exec npm run build`.

### node_modules and Mutagen

`node_modules` is excluded from Mutagen sync via `.ddev/mutagen/mutagen.yml` (`**/node_modules` ignore rule) and lives **only inside the container** — it does not exist on the host filesystem. This is a deliberate Windows workaround: `node_modules/.bin` is full of Unix symlinks that NTFS can't hold without Developer Mode privileges, and syncing (or bind-mounting) the folder to the host breaks those symlinks.

Practical implications:
- Always run `npm install`/`npm run <script>` through `ddev exec`, not on the host directly.
- A host-native editor (not running inside the container) won't have `node_modules` for TypeScript/ESLint IntelliSense — use an editor integration that runs inside the DDEV container, or accept the limitation.
- After `ddev mutagen reset`, `node_modules` needs a fresh `ddev exec npm install` before the Vite daemon can start successfully.

## Everyday commands

| Task | Command |
|---|---|
| Artisan | `ddev artisan <command>` |
| Composer | `ddev composer <command>` |
| Tinker | `ddev artisan tinker` |
| Run tests | `ddev artisan test` |
| Lint (PHP) | `ddev exec vendor/bin/pint` |
| Lint (JS/TS) | `ddev exec npm run lint` |
| Format check | `ddev exec npm run format:check` |
| Type check | `ddev exec npm run types:check` |
| SSH into web container | `ddev ssh` |

## Roles & permissions

`App\Models\User` uses Spatie's `HasRoles` trait. Typical usage:

```php
$user->assignRole('admin');
$user->hasRole('admin');
$user->can('edit articles');
```

Route protection via the registered middleware aliases:

```php
Route::middleware('role:admin')->group(...);
Route::middleware('permission:edit articles')->group(...);
```

No roles/permissions are seeded by default — define them for your app's needs (e.g. in a seeder).

## AI tooling (Laravel Boost)

This project has `laravel/boost` installed as a dev dependency, which:

- Registers an MCP server (`.mcp.json`) exposing Tinker execution, route/schema introspection, and browser logs to AI coding agents
- Ships Laravel/Inertia/React/Fortify/Wayfinder guidelines in `CLAUDE.md`
- Adds Claude Code skills under `.claude/skills/`

None of this ships to production — it's dev-only tooling for working on the codebase.

# Apnea

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
ddev start                 # boot the containers
ddev composer install      # PHP dependencies
ddev exec npm install       # JS dependencies
ddev artisan migrate        # run migrations
ddev exec npm run dev       # start the Vite dev server (HMR)
```

The app is then available at **https://apnea.ddev.site**.

### Vite dev server

`web_extra_exposed_ports` in `.ddev/config.yaml` exposes port 5173 through the DDEV router for HMR, and `vite.config.ts` reads `DDEV_PRIMARY_URL` so the dev server's HMR client connects back to the right host over HTTPS.

The dev server is *not* supervised — it doesn't restart automatically after `ddev restart` or a container rebuild. If you see a 502 on port 5173, just start it again:

```bash
ddev exec "cd /var/www/html && npm run dev"
```

For a production-like build instead: `ddev exec npm run build`.

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

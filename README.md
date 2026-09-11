# Laravel + React Starter Kit

## Introduction

Our React starter kit provides a robust, modern starting point for building Laravel 13 applications with a React frontend using [Inertia](https://inertiajs.com).

Inertia allows you to build modern, single-page React applications using classic server-side routing and controllers. This lets you enjoy the frontend power of React combined with the incredible backend productivity of Laravel and lightning-fast Vite compilation.

This React starter kit utilizes PHP 8.4, React 19, TypeScript, Tailwind, and the [shadcn/ui](https://ui.shadcn.com) and [radix-ui](https://www.radix-ui.com) component libraries. It also includes Laravel Fortify for registration, password reset, email verification, login throttling, and password-protected security settings.

## Included Features

- Laravel 13 with PHP 8.4
- React 19 with TypeScript and Inertia 3
- Tailwind CSS with shadcn/ui and Radix UI
- Laravel Fortify authentication
- Role and permission support with [Spatie Laravel Permission](https://spatie.be/docs/laravel-permission)

Passkeys and two-factor authentication are not enabled in this starter kit.

## DDEV Development

This repository includes a DDEV configuration for local development. Install [DDEV](https://ddev.com/get-started/) and Docker, then run the following commands from the project root:

```bash
ddev start
ddev composer install
npm install
php artisan migrate
npm run dev
```

The application is available at [https://react-starter-kit.ddev.site](https://react-starter-kit.ddev.site). Stop the project with `ddev stop` when you are finished.

## Official Documentation

Documentation for all Laravel starter kits can be found on the [Laravel website](https://laravel.com/docs/starter-kits).

## Contributing

Thank you for considering contributing to our starter kit! The contribution guide can be found in the [Laravel documentation](https://laravel.com/docs/contributions).

All contributions to the Starter Kits from now on should be made through [Maestro](https://github.com/laravel/maestro).

## Code of Conduct

In order to ensure that the Laravel community is welcoming to all, please review and abide by the [Code of Conduct](https://laravel.com/docs/contributions#code-of-conduct).

## License

The Laravel + React starter kit is open-sourced software licensed under the MIT license.

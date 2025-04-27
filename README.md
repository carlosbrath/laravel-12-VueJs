# 🚀 Laravel 12 + Vue.js + Breeze Setup Guide (with Yarn)

This guide helps you set up **Laravel 12** with **Vue 3** and **Laravel Breeze** using **Yarn** and **Vite**.

---

## ✅ Requirements

Make sure you have the following installed:

- PHP 8.2 or higher
- Composer
- Node.js (v16.10+ recommended)
- Yarn
- Web server (e.g., Apache, Laravel Valet, XAMPP)

---

## 📦 Step 1: Create Laravel 12 Project

```bash
composer create-project laravel/laravel:^12.0 laravel-vue-app
cd laravel-vue-app
```

✅ Now you have a fresh Laravel 12 application.

---

## 🧶 Step 2: Install Yarn

### Option 1: Via Corepack (Recommended)
```bash
corepack enable
corepack prepare yarn@stable --activate
```

### Option 2: Via npm
```bash
npm install --global yarn
```

Verify Yarn:
```bash
yarn -v
```

---

## 🔑 Step 3: Install Laravel Breeze (with Vue)

```bash
composer require laravel/breeze --dev
php artisan breeze:install vue
```

Then install dependencies:

```bash
yarn install
```

Build assets:

```bash
yarn dev
```

Run migrations:

```bash
php artisan migrate
```

---

## ⚙️ Step 4: Serve Your Application

Start the Laravel dev server:
```bash
php artisan serve
```

In a separate terminal, run:
```bash
yarn dev
```

Visit: [http://localhost:8000](http://localhost:8000)

You’ll see the default Breeze login/register scaffolding with Vue components.

---

## 🧩 Optional Enhancements

- Vue Router: `yarn add vue-router`
- State Management (Pinia): `yarn add pinia`
- Axios for API calls: `yarn add axios`

---

## 📁 Project Structure Highlights (after Breeze)

- `resources/js/Pages` — Vue pages like Login, Register, Dashboard
- `resources/js/Layouts` — Main layout files
- `routes/web.php` — Web routes
- `routes/api.php` — API routes
- `resources/views` — Blade views (auth fallback or SSR)

---

## 🎉 Done!

You now have a Laravel 12 project with Breeze authentication and Vue 3 (via Vite), all powered by Yarn.

---

**Happy Coding!** 🔥


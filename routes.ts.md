# Sekcija: routes.ts

## `routes.ts`

```ts
// routes.ts - FIXED VERSION

/**
 * An array of routes that are accessible to the public
 * These routes do not require authentication
 * @type {string[]}
 */
export const publicRoutes = [
  "/", // Home page
  "/auth/new-verification"
  // REMOVED "/dashboard/" - dashboard should be protected!
];

/**
 * An array of routes that are used for authentication
 * These routes will redirect logged in users to /complaints
 * @type {string[]}
 */
export const authRoutes = [
  "/auth/login",
  "/auth/register",
  "/auth/error",
  "/auth/reset",
  "/auth/new-password",
];

/**
 * Rute koje su dostupne samo korisnicima sa admin ulogom
 * @type {string[]}
 */
export const adminRoutes = [
  "/admin",
  "/complaints/admin",
  "/operators/new", // Dodano
  "/providers/new", // Dodano ako postoji
  "/services/new",  // Dodano ako postoji
];

/**
 * Rute koje su dostupne samo autentifikovanim korisnicima
 * @type {string[]}
 */
export const protectedRoutes = [
  "/settings",
  "/server",
  "/client",
```


# Sekcija: middleware.ts

## `middleware.ts`

```ts
// middleware.ts - FIXED VERSION
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";
import { auth } from "@/auth";

export default auth((req) => {
  const { nextUrl } = req;
  const pathname = nextUrl.pathname;
  
  console.log(`🔍 Middleware called for: ${pathname}`);
  
  // Get user info from auth
  const isLoggedIn = !!req.auth?.user?.id;
  const isAdmin = req.auth?.user?.role === "ADMIN";
  const isActive = req.auth?.user?.isActive !== false;
  const image = req.auth?.user?.image;

  console.log(`👤 Session data:`, {
    isLoggedIn,
    userId: req.auth?.user?.id,
    role: req.auth?.user?.role,
    isActive: req.auth?.user?.isActive,
    image: req.auth?.user?.image,

  });

  const protectedPaths = ["/admin", "/dashboard", "/operators", "/providers", "/complaints", "/parking-services", "/bulk-services", "/contracts", "/services", "/humanitarian-orgs", "/reports" ];
  const adminOnlyPaths = ["/admin"];

  const isProtected = protectedPaths.s
```


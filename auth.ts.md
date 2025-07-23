# Sekcija: auth.ts

## `auth.ts`

```ts
// auth.ts - Unified authentication configuration

import NextAuth from "next-auth";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { db } from "@/lib/db";
import Credentials from "next-auth/providers/credentials";
import GitHub from "next-auth/providers/github";
import Google from "next-auth/providers/google";
import bcrypt from "bcryptjs";
import { loginSchema } from "@/schemas/auth";
import type { NextAuthConfig } from "next-auth";

// Type extensions
declare module "next-auth" {
  interface User {
    id: string;
    role: string;
    isActive: boolean;
  }
  
  interface Session {
    user: {
      id: string;
      name?: string | null;
      email?: string | null;
      role: string;
      isActive: boolean;
      image?: string | null;
    };
  }
}

declare module "next-auth/jwt" {
  interface JWT {
    id: string;
    role: string;
    isActive: boolean;
  }
}

const config: NextAuthConfig = {
  adapter: PrismaAdapter(db),
  session: { 
    strategy: "jwt",
    m
```


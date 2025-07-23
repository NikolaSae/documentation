# Sekcija: auth.config.ts

## `auth.config.ts`

```ts
// auth.config.ts

import bcrypt from "bcryptjs";
import Credentials from "next-auth/providers/credentials";
import GitHub from "next-auth/providers/github";
import Google from "next-auth/providers/google";
import type { NextAuthConfig, Session } from "next-auth";
import { JWT } from "next-auth/jwt";

import { LoginSchema } from "@/schemas";
import { getUserByEmail } from "@/data/user";

// Add global type extensions
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
    } & DefaultSession["user"];
  }
}

declare module "next-auth/jwt" {
  interface JWT {
    id: string;
    role: string;
    isActive: boolean;
  }
}

export default {
  providers: [
    GitHub({
      clientId: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_
```


# Sekcija: data

## `account.ts`

```ts
import { db } from "@/lib/db";

export const getAccountByUserId = async (userId: string) => {
  try {
    const account = await db.account.findFirst({
      where: { userId },
    });

    return account;
  } catch {
    return null;
  }
};

```

## `complaint.ts`

```ts
// data/complaint.ts
import { db } from "@/lib/db";

export const getComplaintById = async (id: string) => {
  try {
    const complaint = await db.complaint.findUnique({
      where: { id },
      include: {
        user: true,
        product: true,
        assignedTo: {
          select: {
            id: true,
            name: true, // Fetch the name of the assigned user
          },
          },
        comments: {
          include: {
            user: true,
          },
          orderBy: {
            createdAt: "asc",
          },
        },
        attachments: true,
        history: {
          include: {
            user: true,
          },
          orderBy: {
            createdAt: "desc",
          },
        },
      },
    });

    console.log("Complaint fetched for ID:", id, complaint);
    return complaint;
  } catch (error) {
    console.error("[GET_COMPLAINT_BY_ID]", error);
    throw error;
  }
};

export const getComplaintsByUserId = async (userId: string) => {

```

## `password-reset-token.ts`

```ts
import { db } from "@/lib/db";

export const getPasswordResetTokenByToken = async (token: string) => {
  try {
    const passwordToken = await db.passwordResetToken.findUnique({
      where: { token },
    });

    return passwordToken;
  } catch {
    return null;
  }
};

export const getPasswordResetTokenByEmail = async (email: string) => {
  try {
    const passwordToken = await db.passwordResetToken.findFirst({
      where: { email },
    });

    return passwordToken;
  } catch {
    return null;
  }
};

```

## `two-factor-confirmation.ts`

```ts
import { db } from "@/lib/db";

export const getTwoFactorConfoirmationByUserId = async (userId: string) => {
  try {
    const twoFactorConfirmation = await db.twoFactorConfirmation.findUnique({
      where: { userId },
    });
    return twoFactorConfirmation;
  } catch {
    return null;
  }
};

```

## `two-factor-token.ts`

```ts
import { db } from "@/lib/db";

export const getTwoFactorTokenByToken = async (token: string) => {
  try {
    const twoFactorToken = await db.twoFactorToken.findUnique({
      where: { token },
    });
    return twoFactorToken;
  } catch {
    return null;
  }
};

export const getTwoFactorTokenByEmail = async (email: string) => {
  try {
    const twoFactorToken = await db.twoFactorToken.findFirst({
      where: { email },
    });
    return twoFactorToken;
  } catch {
    return null;
  }
};

```

## `user.ts`

```ts
// data/user.ts
import { db } from "@/lib/db";

export const getUserByEmail = async (email: string) => {
  try {
    const user = await db.user.findUnique({ where: { email } });
    return user;
  } catch {
    return null;
  }
};

export const getUserById = async (id: string) => {
  try {
    const user = await db.user.findUnique({ where: { id } });
    return user;
  } catch {
    return null;
  }
};

// New function to get all users
export const getAllUsers = async () => {
  try {
    const users = await db.user.findMany({
      select: {
        id: true,
        name: true,
      },
      orderBy: {
        name: 'asc'
      }
    });
    
    return users;
  } catch (error) {
    console.error("Failed to fetch users:", error);
    return [];
  }
};
// Function to fetch a user by assignedToId
export const getUserByAssignedToId = async (assignedToId: string) => {
  try {
    const user = await db.user.findUnique({
      where: { id: assignedToId },
      select: {
        id: true,
```

## `verification-token.ts`

```ts
import { db } from "@/lib/db";

export const getVerificationTokenByToken = async (token: string) => {
  try {
    const verificationToken = await db.verificationToken.findUnique({
      where: { token },
    });

    return verificationToken;
  } catch {
    return null;
  }
};

export const getVerificationTokenByEmail = async (email: string) => {
  try {
    const verificationToken = await db.verificationToken.findFirst({
      where: { email },
    });

    return verificationToken;
  } catch {
    return null;
  }
};

```


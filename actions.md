# Sekcija: actions

## `admin.ts`

```ts
//actions/admin.ts

"use server";

import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client";

export const admin = async () => {
  const role = await currentRole();

  if (role !== UserRole.ADMIN) {
    return { error: "Forbidden Server Action!" };
  }

  return { success: "Allowed Server Action!" };
};

```

## `login.ts`

```ts
// actions/login.ts - Fixed version
"use server";

import * as z from "zod";
import { AuthError } from "next-auth";

import { db } from "@/lib/db";
import { signIn } from "@/auth";
import { loginSchema } from "@/schemas/auth";
import { getUserByEmail } from "@/data/user";
import { getTwoFactorTokenByEmail } from "@/data/two-factor-token";
import { sendVerificationEmail, sendTwoFactorTokenEmail } from "@/lib/mail";
import { DEFAULT_LOGIN_REDIRECT } from "@/routes";
import {
  generateVerificationToken,
  generateTwoFactorToken,
} from "@/lib/tokens";
import { getTwoFactorConfoirmationByUserId } from "@/data/two-factor-confirmation";

// Extended schema that includes optional code for 2FA
const loginWithCodeSchema = loginSchema.extend({
  code: z.string().optional(),
});

export const login = async (
  values: z.infer<typeof loginWithCodeSchema>,
  callbackUrl?: string | null
) => {
  console.log("🔐 Login attempt:", { email: values.email, hasCode: !!values.code });

  const validatedFiel
```

## `logout.ts`

```ts
"use server";

import { signOut } from "@/auth";

export const logout = async () => {
  await signOut();
};

```

## `new-verification.ts`

```ts
"use server";

import { db } from "@/lib/db";
import { getUserByEmail } from "@/data/user";
import { getVerificationTokenByToken } from "@/data/verification-token";

export const newVerification = async (token: string) => {
  const existingToken = await getVerificationTokenByToken(token);

  if (!existingToken) {
    return { error: "Token does not exist!" };
  }

  const hasExpired = new Date(existingToken.expires) < new Date();

  if (hasExpired) {
    return { error: "Token has expired!" };
  }

  const existingUser = await getUserByEmail(existingToken.email);

  if (!existingUser) {
    return { error: "Email does not exist!" };
  }

  await db.user.update({
    where: { id: existingUser.id },
    data: {
      emailVerified: new Date(),
      email: existingToken.email,
    },
  });

  await db.verificationToken.delete({
    where: { id: existingToken.id },
  });

  return { success: "Email verified!" };
};

```

## `password.ts`

```ts
"use server";

import * as z from "zod";
import bcrypt from "bcryptjs";

import { NewPasswordSchema } from "@/schemas";
import { getPasswordResetTokenByToken } from "@/data/password-reset-token";
import { getUserByEmail } from "@/data/user";
import { db } from "@/lib/db";

export const newPassword = async (
  values: z.infer<typeof NewPasswordSchema>,
  token?: string | null
) => {
  if (!token) {
    return { error: "Missing token!" };
  }

  const validatedFields = NewPasswordSchema.safeParse(values);

  if (!validatedFields.success) {
    return {
      error: "Invalid fields",
    };
  }

  const { password } = validatedFields.data;

  const existingToken = await getPasswordResetTokenByToken(token);

  if (!existingToken) {
    return {
      error: "Invalid token!",
    };
  }

  const hasExpired = new Date(existingToken.expires) < new Date();

  if (hasExpired) {
    return {
      error: "Token has expired!",
    };
  }

  const exitingUser = await getUserByEmail(existingToken.e
```

## `register.ts`

```ts
"use server";

import * as z from "zod";
import bcrypt from "bcryptjs";

import { db } from "@/lib/db";
import { RegisterSchema } from "@/schemas";
import { getUserByEmail } from "@/data/user";
import { generateVerificationToken } from "@/lib/tokens";
import { sendVerificationEmail } from "@/lib/mail";

export const register = async (values: z.infer<typeof RegisterSchema>) => {
  const validatedFields = RegisterSchema.safeParse(values);

  if (!validatedFields.success) {
    return { error: "Invalid fields!" };
  }

  const { email, password, name } = validatedFields.data;

  const hashedPassword = await bcrypt.hash(password, 10);

  const existingUser = await getUserByEmail(email);

  if (existingUser) {
    return { error: "Email already in use!" };
  }

  await db.user.create({
    data: {
      name,
      email,
      password: hashedPassword,
    },
  });

  const verificationToken = await generateVerificationToken(email);

  await sendVerificationEmail(verificationToken.email, v
```

## `reset.ts`

```ts
"use server";

import * as z from "zod";

import { ResetSchema } from "@/schemas";
import { getUserByEmail } from "@/data/user";
import { sendPasswordResetEmail } from "@/lib/mail";
import { generatePasswordResetToken } from "@/lib/tokens";

export const reset = async (values: z.infer<typeof ResetSchema>) => {
  const validatedFields = ResetSchema.safeParse(values);

  if (!validatedFields.success) {
    return { error: "Invalid email!" };
  }

  const { email } = validatedFields.data;

  const exitingUser = await getUserByEmail(email);

  if (!exitingUser) {
    return {
      error: "User not found!",
    };
  }

  const passwordResetToken = await generatePasswordResetToken(email);
  await sendPasswordResetEmail(
    passwordResetToken.email,
    passwordResetToken.token
  );

  return { success: "Reset email sent!" };
};

```

## `settings.ts`

```ts
"use server";

import * as z from "zod";
import bcrypt from "bcryptjs";

import { db } from "@/lib/db";
import { SettingsSchema } from "@/schemas";
import { getUserByEmail, getUserById } from "@/data/user";
import { currentUser } from "@/lib/auth";
import { generateVerificationToken } from "@/lib/tokens";
import { sendVerificationEmail } from "@/lib/mail";

export const settings = async (values: z.infer<typeof SettingsSchema>) => {
  const user = await currentUser();

  if (!user || !user.id) {
    return { error: "Unauthorized" };
  }

  const dbUser = await getUserById(user.id);

  if (user.isOAuth) {
    (values.email = undefined),
      (values.password = undefined),
      (values.newPassword = undefined),
      (values.isTwoFactorEnabled = undefined);
  }

  if (values.email && values.email !== user.email) {
    const existingUser = await getUserByEmail(values.email);

    if (existingUser && existingUser.id !== user.id) {
      return { error: "Email already in use!" };
    }

  
```

## `get-complaint-stats.ts`

```ts
///actions/analytics/get-complaint-stats.ts

"use server";

import { db } from "@/lib/db";
import { canViewComplaintData } from "@/lib/security/permission-checker";
import { ComplaintStatus } from "@prisma/client";

export type ComplaintStatsParams = {
  startDate?: Date;
  endDate?: Date;
  serviceIds?: string[];
  providerIds?: string[];
  statuses?: ComplaintStatus[];
  priorities?: number[];
  searchQuery?: string;
  sortBy?: string;
  sortOrder?: 'asc' | 'desc';
};

export type ComplaintStats = {
  totalComplaints: number;
  resolvedComplaints: number;
  openComplaints: number;
  averageResolutionTime: number;
  complaintsByStatus: {
    status: string;
    count: number;
    percentage: number;
  }[];
  complaintsByMonth: {
    month: string;
    total: number;
    resolved: number;
  }[];
  complaintsByService: {
    serviceName: string;
    count: number;
    percentage: number;
  }[];
  complaintsByProvider: {
    providerName: string;
    count: number;
    resolvedCount: num
```

## `get-financial-data.ts`

```ts
///actions/analytics/get-financial-data.ts

"use server";

import { db } from "@/lib/db";
import { currentUser } from "@/lib/auth";
import { canViewFinancialData } from "@/lib/security/permission-checker";
// Uklonjen import za revalidatePath jer se ne koristi u ovoj akciji na ovaj način
// import { revalidatePath } from "next/cache"; // <--- REMOVED

export type FinancialDataParams = {
  startDate?: Date;
  endDate?: Date;
  serviceType?: string;
  providerId?: string;
};

export type FinancialMetrics = {
  totalRevenue: number;
  outstandingAmount: number;
  collectedAmount: number;
  canceledAmount: number;
  revenueByMonth: {
    month: string;
    revenue: number;
    collected: number;
    outstanding: number;
  }[];
  serviceTypeBreakdown: {
    serviceType: string;
    revenue: number;
    percentage: number;
  }[];
  providerBreakdown: {
    providerName: string;
    revenue: number;
    percentage: number;
  }[];
};

export async function getFinancialData({
  startDate,
  end
```

## `get-sales-metrics.ts`

```ts
///actions/analytics/get-sales-metrics.ts

"use server";

import { db } from "@/lib/db";
import { canViewSalesData } from "@/lib/security/permission-checker";

export type SalesMetricsParams = {
  startDate?: Date;
  endDate?: Date;
  serviceType?: string;
  providerId?: string;
};

export type SalesMetrics = {
  totalTransactions: number;
  totalRevenue: number;
  averageTransactionValue: number;
  transactionsByMonth: {
    month: string;
    transactions: number;
    revenue: number;
  }[];
  transactionsByServiceType: {
    serviceType: string;
    transactions: number;
    percentage: number;
  }[];
  topProviders: {
    providerName: string;
    transactions: number;
    revenue: number;
  }[];
  growthRate: {
    transactionsGrowth: number;
    revenueGrowth: number;
  };
};

export async function getSalesMetrics({
  startDate,
  endDate,
  serviceType,
  providerId,
}: SalesMetricsParams = {}): Promise<SalesMetrics> {

  const hasPermission = await canViewSalesData;
  if (!hasP
```

## `check-blacklisted-senders.ts`

```ts
// actions/blacklist/check-blacklisted-senders.ts
"use server";

import { db } from "@/lib/db";

export async function checkBlacklistedSenders() {
  try {
    const blacklistEntries = await db.senderBlacklist.findMany({
      where: {
        isActive: true,
        effectiveDate: {
          lte: new Date()
        }
      }
    });

    const matches = [];
    
    for (const entry of blacklistEntries) {
      const matchingBulkServices = await db.bulkService.findMany({
        where: {
          sender_name: entry.senderName
        },
        include: {
          provider: {
            select: {
              id: true,
              name: true
            }
          }
        }
      });

      if (matchingBulkServices.length > 0) {
        // Extract provider names
        const providerNames = matchingBulkServices.map(
          service => service.provider.name
        );
        
        await db.senderBlacklist.update({
          where: { id: entry.id },
          data: {
   
```

## `create-blacklist-entry.ts`

```ts
// actions/blacklist/create-blacklist-entry.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { createAuditLog } from "@/lib/security/black-log";
import { LogBlackType } from "@prisma/client";

interface CreateBlacklistEntryData {
  senderName: string;
  effectiveDate: Date;
  description?: string;
  isActive?: boolean;
}

export async function createBlacklistEntry(data: CreateBlacklistEntryData) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { success: false, error: "Unauthorized" };
    }

    // Check for existing entry
    const existingEntry = await db.senderBlacklist.findFirst({
      where: { senderName: data.senderName }
    });

    if (existingEntry) {
      return { 
        success: false, 
        error: `Blacklist entry already exists for sender: ${data.senderName}` 
      };
    }

    // Create entry
    const blacklistEntry = await db.senderBlacklis
```

## `delete-blacklist-entry.ts`

```ts
// actions/blacklist/delete-blacklist-entry.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { createAuditLog } from "@/lib/security/black-log";
import { LogBlackType } from "@prisma/client";

export async function deleteBlacklistEntry(id: string) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { success: false, error: "Unauthorized" };
    }

    // Get existing data for audit log
    const oldData = await db.senderBlacklist.findUnique({
      where: { id }
    });

    if (!oldData) {
      return { success: false, error: "Entry not found" };
    }

    // Create audit log for deletion
    await createAuditLog({
      action: LogBlackType.DELETE,
      entityId: id,
      userId: session.user.id,
      oldData
    });

    // Delete the SenderBlacklist entry
    // Related BlacklistLog entries will automatically have their entityId set to NULL
    await db.senderB
```

## `get-blacklist-logs.ts`

```ts
// actions/blacklist/get-blacklist-logs.ts

"use server";

import { db } from "@/lib/db";

export async function getBlacklistLogs() {
  try {
    const logs = await db.blacklistLog.findMany({
      include: {
        user: {
          select: {
            id: true,
            name: true,
            email: true
          }
        },
        blacklistEntry: {
          select: {
            id: true,
            senderName: true
          }
        }
      },
      orderBy: {
        timestamp: "desc"
      },
      take: 100 // Limit to 100 most recent logs
    });

    return logs;
  } catch (error) {
    console.error("Failed to fetch blacklist logs:", error);
    return [];
  }
}
```

## `update-blacklist-entry.ts`

```ts
// actions/blacklist/update-blacklist-entry.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { createAuditLog } from "@/lib/security/black-log";
import { LogBlackType } from "@prisma/client";

interface UpdateBlacklistEntryData {
  id: string;
  isActive?: boolean;
  description?: string;
  effectiveDate?: Date;
}

export async function updateBlacklistEntry(data: UpdateBlacklistEntryData) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { success: false, error: "Unauthorized" };
    }

    // Get existing data for audit log
    const oldData = await db.senderBlacklist.findUnique({
      where: { id: data.id }
    });

    if (!oldData) {
      return { success: false, error: "Entry not found" };
    }

    // Update entry
    const updatedEntry = await db.senderBlacklist.update({
      where: { id: data.id },
      data: {
        ...(data.isActive !== undefined && {
```

## `create.ts`

```ts
//actions/bulk-services/create.ts

//actions/bulk-services/create.ts
"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { bulkServiceSchema } from "@/schemas/bulk-service";
import { getCurrentUser } from "@/lib/session";
import { ActivityLogService } from "@/lib/services/activity-log-service";
import { LogSeverity } from "@prisma/client";
import { revalidatePath } from "next/cache";

export async function createBulkService(data: unknown) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    // Validate the input data
    const validatedData = bulkServiceSchema.parse(data); // ✅ Ispravka: bulkServiceSchema

    // Create the bulk service
    const bulkService = await db.bulkService.create({
      data: {
        ...validatedData,
        createdAt: new Date(),
        updatedAt: new Date(),
      },
      include: {
        provider: true,
        service:
```

## `delete.ts`

```ts
///actions/bulk-services/delete.ts

"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { getCurrentUser } from "@/lib/session";
import { ActivityLogService } from "@/lib/services/activity-log-service";
import { LogSeverity } from "@prisma/client";
import { revalidatePath } from "next/cache";

export async function deleteBulkService(id: string) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    // Check if bulk service exists and get details for activity log
    const bulkService = await db.bulkService.findUnique({
      where: { id },
    });

    if (!bulkService) {
      throw new Error("Bulk service not found");
    }

    // Create a log entry first
    const logEntry = await db.logEntry.create({
      data: {
        entityType: "BULK_SERVICE",
        entityId: id,
        action: "DEACTIVATION",
        subject: `Bulk Service Deletion: ${bulkSer
```

## `export.ts`

```ts
//actions/bulk-services/export.ts

"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { getCurrentUser } from "@/lib/session";
import { formatBulkServiceCSV } from "@/lib/bulk-services/csv-processor"; // Ispravno ime exportovane funkcije
import { BulkServicesFilter } from "@/lib/types/bulk-service-types";

export async function exportBulkServices(filters?: BulkServicesFilter) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    // Build where conditions based on filters
    const where: any = {};
    
    if (filters?.providerId) {
      where.providerId = filters.providerId;
    }
    
    if (filters?.serviceId) {
      where.serviceId = filters.serviceId;
    }
    
    if (filters?.providerName) {
      where.provider_name = {
        contains: filters.providerName,
        mode: "insensitive"
      };
    }
    
    if (filters?.serviceName) {
     
```

## `getAllBulkServices.ts`

```ts
//actions/bulk-services/getAllBulkServices.ts

"use server";

import { db } from "@/lib/db";
import { BulkService } from "@prisma/client";

type BulkServiceWithRelations = BulkService & {
  provider: { name: string };
  service: { name: string };
};

export async function getAllBulkServices(): Promise<BulkServiceWithRelations[]> {
  try {
    const bulkServices = await db.bulkService.findMany({
      orderBy: {
        createdAt: "desc"
      },
      include: {
        provider: {
          select: {
            name: true
          }
        },
        service: {
          select: {
            name: true
          }
        }
      }
    });
    
    return bulkServices;
  } catch (error) {
    console.error("[GET_ALL_BULK_SERVICES_ERROR]", error);
    throw new Error("Failed to fetch bulk services");
  }
}
```

## `getBulkServiceById.ts`

```ts
//actions/bulk-services/getBulkServiceById.ts

"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { getCurrentUser } from "@/lib/session";

export async function getBulkServiceById(id: string) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    const bulkService = await db.bulkService.findUnique({
      where: { id },
      include: {
        provider: true,
        service: true,
      },
    });

    if (!bulkService) {
      throw new Error("Bulk service not found");
    }

    return bulkService;
  } catch (error) {
    console.error("[GET_BULK_SERVICE_BY_ID]", error);
    throw new ServerError("Failed to fetch bulk service");
  }
}
```

## `getBulkServices.ts`

```ts
//actions/bulk-services/getBulkServices.ts

"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { BulkServicesFilter } from "@/lib/types/bulk-service-types";
import { getCurrentUser } from "@/lib/session";

export async function getBulkServices({
  providerId,
  serviceId,
  providerName,
  serviceName,
  page = 1,
  limit = 10,
  sortBy = "createdAt",
  sortOrder = "desc",
}: BulkServicesFilter) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    const skip = (page - 1) * limit;
    
    // Build where conditions based on filters
    const where: any = {};
    
    if (providerId) {
      where.providerId = providerId;
    }
    
    if (serviceId) {
      where.serviceId = serviceId;
    }
    
    if (providerName) {
      where.provider_name = {
        contains: providerName,
        mode: "insensitive"
      };
    }
    
    if (serviceName) {
   
```

## `getBulkServicesByProviderId.ts`

```ts
//actions/bulk-services/getBulkServicesByProviderId.ts

"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { getCurrentUser } from "@/lib/session";

export async function getBulkServicesByProviderId(providerId: string) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    const bulkServices = await db.bulkService.findMany({
      where: { providerId: providerId },
      include: {
        service: true,
      },
      orderBy: {
        createdAt: "desc",
      },
    });

    return bulkServices;
  } catch (error) {
    console.error("[GET_BULK_SERVICES_BY_PROVIDER]", error);
    throw new ServerError("Failed to fetch provider bulk services");
  }
}
```

## `import.ts`

```ts
//actions/bulk-services/import.ts
"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { getCurrentUser } from "@/lib/session";
import { parseBulkServiceCSV, processBulkServiceCsv } from "@/lib/bulk-services/csv-processor";
import { ActivityLogService } from "@/lib/services/activity-log-service";
import { LogSeverity, ServiceType, BillingType } from "@prisma/client";
import { revalidatePath } from "next/cache";
import { BulkServiceImportResult } from "@/lib/types/bulk-service-types";

export async function importBulkServicesFromCsv(
  csvContent: string,
  importDate: Date
): Promise<BulkServiceImportResult> {
  const results: BulkServiceImportResult = {
    totalRows: 0,
    validRows: [],
    invalidRows: [],
    importErrors: [],
    error: null,
    createdCount: 0,
    updatedCount: 0,
    createdServices: [],
  };

  try {
    const currentUser = await getCurrentUser();

    if (!currentUser) {
      results.error = "Unauthorized";

```

## `index.ts`

```ts
//actions/bulk-services/index.ts

"use server";
import { createBulkService as create } from './create';
import { updateBulkService as update } from './update';
import { getBulkServiceById } from './getBulkServiceById';
import { getBulkServices } from './getBulkServices';
import { getBulkServicesByProviderId } from './getBulkServicesByProviderId';
import { deleteBulkService } from './delete';
import { importBulkServices } from './import';
import { exportBulkServices } from './export';
import { getAllBulkServices } from './getAllBulkServices';

export {
  create,
  update,
  getBulkServiceById,
  getBulkServices,
  getBulkServicesByProviderId,
  deleteBulkService,
  importBulkServices,
  exportBulkServices,
  getAllBulkServices,
};
```

## `update.ts`

```ts
//actions/bulk-services/update.ts

//actions/bulk-services/update.ts
"use server";

import { db } from "@/lib/db";
import { ServerError } from "@/lib/exceptions";
import { bulkServiceSchema } from "@/schemas/bulk-service";
import { getCurrentUser } from "@/lib/session";
import { ActivityLogService } from "@/lib/services/activity-log-service";
import { LogSeverity } from "@prisma/client";
import { revalidatePath } from "next/cache";

export async function updateBulkService(id: string, data: unknown) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    // Check if bulk service exists
    const existingBulkService = await db.bulkService.findUnique({
      where: { id },
    });

    if (!existingBulkService) {
      throw new Error("Bulk service not found");
    }

    // Validate the input data
    const validatedData = bulkServiceSchema.parse(data); // ✅ Ispravka: bulkServiceSchema

    // Update the bul
```

## `assign.ts`

```ts
// Path: actions/complaints/assign.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole, ComplaintStatus } from "@prisma/client";
import { revalidatePath } from "next/cache";

interface AssignComplaintParams {
    complaintId: string;
    userId: string | null; // null to unassign
}

/**
 * Assigns a complaint to a specific user (Agent or Admin).
 * Only accessible by Admin and Manager roles.
 * Updates complaint status to ASSIGNED if a user is assigned and status is NEW.
 * @param params - Object containing complaintId and userId (or null to unassign).
 * @returns A promise resolving to a success object or an error object.
 */
export async function assignComplaint({ complaintId, userId }: AssignComplaintParams) {
  try {
    const session = await auth();

    // Check if the user is authenticated and has the necessary role (Admin or Manager)
    if (!session || !session.user || (session.user.role !== UserRole.ADMIN && session.user.role !== 
```

## `change-status.ts`

```ts
// actions/complaints/change-status.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth"; // Proverite putanju za auth
import { z } from "zod";
import { revalidatePath } from "next/cache";
import { ComplaintStatus } from "@prisma/client"; // Uvezeno ComplaintStatus iz Prisma klijenta
import { createNotification } from "@/utils/complaint-notification"; // Proverite putanju za createNotification

// ISPRAVLJENO: Dodat opcionalni assignedAgentId u shemu
const statusChangeSchema = z.object({
  complaintId: z.string().min(1),
  status: z.nativeEnum(ComplaintStatus),
  notes: z.string().optional(),
  assignedAgentId: z.string().optional().nullable(), // Dodato opcionalno/nullable polje za ID agenta
});

export type StatusChangeFormData = z.infer<typeof statusChangeSchema>;

export async function changeComplaintStatus(data: StatusChangeFormData) {
  try {
    const session = await auth();
    if (!session?.user) {
      return {
        error: "Unauthorized. Please s
```

## `comment.ts`

```ts
// /actions/complaints/comment.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";
import { revalidatePath } from "next/cache";

const commentSchema = z.object({
  complaintId: z.string().min(1),
  text: z.string().min(1, "Comment text is required"),
  isInternal: z.boolean().default(false),
});

export type CommentFormData = z.infer<typeof commentSchema>;

export async function addComment(data: CommentFormData) {
  try {
    const session = await auth();
    if (!session?.user) {
      return {
        error: "Unauthorized. Please sign in to add a comment.",
      };
    }

    const validatedData = commentSchema.parse(data);

    // Check if complaint exists
    const complaint = await db.complaint.findUnique({
      where: { id: validatedData.complaintId },
    });

    if (!complaint) {
      return {
        error: "Complaint not found",
      };
    }

    // Check user permissions for internal comments
    if (validatedData.is
```

## `create.ts`

```ts
// /actions/complaints/create.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";
import { revalidatePath } from "next/cache";

const createComplaintSchema = z.object({
  title: z.string().min(5, "Title must be at least 5 characters").max(100, "Title cannot exceed 100 characters"),
  description: z.string().min(10, "Description must be at least 10 characters"),
  serviceId: z.string().optional().nullable(),
  productId: z.string().optional().nullable(),
  providerId: z.string().optional().nullable(),
  priority: z.number().min(1).max(5).default(3),
  financialImpact: z.number().optional().nullable(),
});

export type CreateComplaintFormData = z.infer<typeof createComplaintSchema>;

export async function createComplaint(data: CreateComplaintFormData) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return {
        error: "Unauthorized. Please sign in to create a complaint.",
        success: false
    
```

## `delete.ts`

```ts
// /actions/complaints/delete.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";

export async function deleteComplaint(id: string) {
  try {
    const session = await auth();
    if (!session?.user) {
      return {
        error: "Unauthorized. Please sign in to delete a complaint.",
      };
    }

    // Find the complaint
    const complaint = await db.complaint.findUnique({
      where: { id },
    });

    if (!complaint) {
      return {
        error: "Complaint not found",
      };
    }

    // Check if user has permission to delete
    const user = await db.user.findUnique({
      where: { id: session.user.id },
    });

    const isSubmitter = complaint.submittedById === session.user.id;
    const canDelete = ["ADMIN", "MANAGER"].includes(user?.role || "") || 
                      (isSubmitter && complaint.status === "NEW"); // Submitters can only delete N
```

## `export.ts`

```ts
// /actions/complaints/export.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { ComplaintStatus } from "@prisma/client";
import { formatDate } from "@/utils/date-filters";

export type ExportFormat = "csv" | "json" | "excel";

export type ExportOptions = {
  format: ExportFormat;
  dateRange?: {
    from: Date;
    to: Date;
  };
  statuses?: ComplaintStatus[];
  serviceId?: string;
  providerId?: string;
  productId?: string;
};

export async function exportComplaints(options: ExportOptions): Promise<string> {
  const session = await auth();
  if (!session?.user) {
    throw new Error("Unauthorized access");
  }

  // Build filter criteria
  const where: any = {};
  
  if (options.dateRange) {
    where.createdAt = {
      gte: options.dateRange.from,
      lte: options.dateRange.to,
    };
  }
  
  if (options.statuses && options.statuses.length > 0) {
    where.status = {
      in: options.statuses,
    };
  }
  
  if (options.serviceId) {
    
```

## `get-by-id.ts`

```ts
// Path: actions/complaints/get-by-id.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

/**
 * Fetches a specific complaint by its ID, including related data.
 * Performs authorization check to ensure the user has permission to view the complaint.
 * @param id - The ID of the complaint to fetch.
 * @returns A promise resolving to an object containing the complaint data or an error.
 */
export async function getComplaintById(id: string) {
    try {
        // Get the current user session
        const session = await auth();

        // Check if the user is authenticated
        if (!session || !session.user) {
            return { error: "Unauthorized" };
        }

        // Fetch the complaint from the database, including necessary relations
        const complaint = await db.complaint.findUnique({
            where: { id },
            include: {
                submittedBy: { select: { id: true, name: true, 
```

## `getServicesByProvider.ts`

```ts
// Path: /actions/complaints/getServicesByProvider.ts
'use server';

import { db } from '@/lib/db';
import { auth } from '@/auth';
import { UserRole } from "@prisma/client";
import { currentRole } from '@/lib/auth'; // Pretpostavljena putanja do currentRole funkcije

// Definirajte tip za servis koji vraćamo
interface ServiceOption {
    id: string;
    name: string;
    type: string; // Dodajte i tip servisa ako je relevantno
}

export async function getServicesByProvider(providerId: string): Promise<{ data: ServiceOption[], error: string | null }> {
    // Provera autentifikacije i autorizacije
    const session = await auth();
    if (!session?.user) {
        return { data: [], error: "Unauthorized" };
    }

    const role = await currentRole();
    // Prilagodite uloge koje imaju pravo da vide servise povezane sa provajderima
    if (role !== UserRole.ADMIN && role !== UserRole.MANAGER && role !== UserRole.AGENT && role !== UserRole.USER) {
        return { data: [], error: "Forb
```

## `humanitarian.ts`

```ts
// Path: actions/complaints/humanitarian.ts

import { db } from "@/lib/db";

/**
 * Fetches all humanitarian organizations
 * @returns Array of humanitarian organizations with id and name
 */
export async function getHumanitarianOrgs() {
  try {
    const organizations = await db.humanitarianOrg.findMany({
      select: {
        id: true,
        name: true,
      },
      orderBy: {
        name: "asc",
      },
    });

    return organizations;
  } catch (error) {
    console.error("Error fetching humanitarian organizations:", error);
    return [];
  }
}
```

## `import.ts`

```ts
// /actions/complaints/import.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { ComplaintImportSchema } from "@/schemas/complaint"; // ✅ Ispravka: ComplaintImportSchema postoji
import { LogSeverity, ComplaintStatus } from "@prisma/client";

export type ImportResult = {
  success: boolean;
  importedCount?: number;
  failedCount?: number;
  errors?: string[];
  message?: string;
};

// ✅ Potrebno je kreirati ComplaintImportSchema u schemas/complaint.ts
// ili koristiti postojeću ComplaintSchema

export async function importComplaints(formData: FormData): Promise<ImportResult> {
  const session = await auth();
  
  if (!session?.user || !["ADMIN", "MANAGER"].includes(session.user.role)) {
    return {
      success: false,
      message: "Unauthorized access"
    };
  }

  try {
    // Get the file from FormData
    const file = formData.get("file") as File;
    if (!file) {
      return {
        success
```

## `parking.ts`

```ts
// Path: actions/complaints/parking.ts

import { db } from "@/lib/db";

/**
 * Fetches all parking services
 * @returns Array of parking services with id and name
 */
export async function getParkingServices() {
  try {
    const services = await db.parkingService.findMany({
      select: {
        id: true,
        name: true,
      },
      orderBy: {
        name: "asc",
      },
    });

    return services;
  } catch (error) {
    console.error("Error fetching parking services:", error);
    return [];
  }
}
```

## `products.ts`

```ts
"use server";

///actions/complaints/products.ts



import { db } from "@/lib/db";

interface ProductData {
  id: string;
  name: string;
}

export async function getProductsByServiceId(serviceId: string | null): Promise<ProductData[]> {
  if (!serviceId) {
    console.log("No serviceId provided to getProductsByServiceId");
    return [];
  }

  try {
    const products = await db.product.findMany({
        where: {
            serviceId: serviceId
        },
        select: {
            id: true,
            name: true,
        },
        orderBy: { name: 'asc' },
    });

    console.log(`Workspaceed ${products.length} products for service ${serviceId}`);
    return products;

  } catch (error) {
    console.error(`Error fetching products for service ${serviceId}:`, error);
    return [];
  }
}
```

## `providers.ts`

```ts
///actions/complaints/providers.ts


"use server";

import { db } from "@/lib/db";

interface ProviderData {
  id: string;
  name: string;
}

export async function getProviders(): Promise<ProviderData[]> {
  try {
    const providers = await db.provider.findMany({
      select: {
        id: true,
        name: true,
      },
      orderBy: { name: 'asc' },
    });
    console.log("Successfully fetched providers for form:", providers.length, "items");
    return providers;
  } catch (error) {
    console.error("Error fetching providers for form:", error);
    return [];
  }
}
```

## `services.ts`

```ts
///actions/complaints/services.ts

"use server";

import { db } from "@/lib/db";

interface ServiceData {
  id: string;
  name: string;
  type: string;
}

export async function getServicesByProviderId(providerId: string | null): Promise<ServiceData[]> {
  if (!providerId) {
    console.log("No providerId provided to getServicesByProviderId");
    return [];
  }

  try {
    const services = await db.service.findMany({
        where: {
            OR: [
                {
                    vasServices: {
                        some: {
                            provajderId: providerId
                        }
                    }
                },
                {
                    bulkServices: {
                        some: {
                            providerId: providerId
                        }
                    }
                }
            ]
        },
        select: {
            id: true,
            name: true,
            type: true,
        },
        orde
```

## `update.ts`

```ts
// /actions/complaints/update.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";
import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";
import { ComplaintStatus, ComplaintEntityType, UserRole } from "@prisma/client"; // Import UserRole
import { ComplaintFormData } from "@/lib/types/complaint-types";

const updateComplaintSchema = z.object({
  id: z.string().min(1),
  title: z.string().min(5, "Title must be at least 5 characters").max(100, "Title cannot exceed 100 characters"),
  description: z.string().min(10, "Description must be at least 10 characters"),
  
  entityType: z.nativeEnum(ComplaintEntityType).optional(), // Optional for update, as it might not be changed by restricted users
  serviceId: z.string().optional().nullable(),
  productId: z.string().optional().nullable(),
  providerId: z.string().optional().nullable(),
  humanitarianOrgId: z.string().optional().nullable(),
  parkingServiceId
```

## `acknowledge-reminder.ts`

```ts
// /actions/contracts/acknowledge-reminder.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';


export async function acknowledgeReminder(reminderId: string): Promise<{ success?: string; reminderId?: string; error?: string }> {
    if (!reminderId) {
        return { error: 'Reminder ID is required.' };
    }

    try {
        const reminderToUpdate = await db.reminder.findUnique({
            where: { id: reminderId },
        });

        if (!reminderToUpdate) {
             return { error: 'Reminder not found.' };
        }

        const updatedReminder = await db.reminder.update({
            where: { id: reminderId },
            data: {
                isDismissed: true,

            },
        });

         revalidatePath(`/contracts/${updatedReminder.contractId}`);


        return { success: 'Reminder acknowledged.', reminderId: updatedReminder.id };

    } catch (error) {
        console.error(`Error acknowledging reminder ${remin
```

## `add-attachment.ts`

```ts
// /actions/contracts/add-attachment.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { validateContractAttachment } from '@/lib/contracts/validators';
import { uploadFile } from '@/lib/file-storage'; // Now exists

export const addAttachment = async (formData: FormData) => {
  const session = await auth();
  const userId = session?.user?.id;

  if (!userId) {
    return { error: "Unauthorized" };
  }

  // Get form data
  const contractId = formData.get('contractId') as string;
  const file = formData.get('file') as File | null;

  if (!file) {
    return { error: "No file provided." };
  }

  // Validate file
  const validationResult = validateContractAttachment(file);
  if (!validationResult.success) {
    return { error: validationResult.errors?.join(', ') || "Invalid file." };
  }

  try {
    // Check if contract exists
    const existingContract = await db.contract.findUnique({
      where: { id: 
```

## `add-service.ts`

```ts
// /actions/contracts/add-service.ts
'use server';

import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth'; // Pretpostavljena putanja do vašeg auth helpera
import { z } from 'zod'; // Koristimo Zod za osnovnu validaciju ID-jeva

// Osnovna šema za validaciju ulaznih podataka
const AddServiceSchema = z.object({
  contractId: z.string().cuid("Invalid contract ID format"), // Pretpostavka da koristite CUIDs
  serviceId: z.string().cuid("Invalid service ID format"),   // Pretpostavka da koristite CUIDs
  specificTerms: z.string().optional().nullable(),
});

/**
 * Povezuje servis sa ugovorom.
 * @param values - Objekat koji sadrži contractId, serviceId i opcione specificTerms.
 * @returns Uspeh/neuspeh operacije i, u slučaju uspeha, podatke o kreiranom ServiceContract zapisu.
 */
export const addContractService = async (values: z.infer<typeof AddServiceSchema>) => {
  // 1. Validacija ul
```

## `check-expiring.ts`

```ts
// /actions/contracts/check-expiring.ts
'use server';

import { db } from '@/lib/db';
import { addDays, startOfDay, endOfDay } from 'date-fns';
import { sendContractExpirationNotification } from '@/lib/contracts/notification-sender';
import { auth } from '@/auth'; // Pretpostavljena putanja do vašeg auth helpera

/**
 * Proverava ugovore kojima ističe rok u narednom broju dana i kreira podsetnike/notifikacije.
 * Može se pokrenuti manuelno ili kroz zakazani task.
 * @param daysThreshold - Broj dana unutar kojih se ugovori smatraju "uskoro ističu". Podrazumevano 30.
 * @returns Objekat sa informacijama o rezultatu provere.
 */
export const checkExpiringContracts = async (daysThreshold: number = 30) => {
  // Opciono: Provera autorizacije ako ovu akciju mogu pokrenuti samo određeni korisnici
   const session = await auth();
   if (!session?.user) {
     return { error: "Unauthorized" };
   }

  try {
    const today = startOfDay(new Date());
    const expiryDateThreshold = endOfDay(addDa
```

## `contract-actions.ts`

```ts
// actions/contracts/contract-actions.ts
'use server'

import { auth } from '@/lib/auth';
import { db } from '@/lib/db';
import { ContractStatus, ContractRenewalSubStatus } from '@prisma/client';
import { revalidatePath } from 'next/cache';

export async function updateContractStatus(
  contractId: string,
  newStatus: ContractStatus,
  comments?: string
) {
  try {
    const session = await auth();

    if (!session?.user) {
      return {
        success: false,
        message: 'Not authenticated'
      };
    }

    if (!contractId || !contractId.trim()) {
      return {
        success: false,
        message: 'Contract ID is required'
      };
    }

    if (!Object.values(ContractStatus).includes(newStatus)) {
      return {
        success: false,
        message: 'Invalid contract status'
      };
    }

    await db.$connect();

    const existingContract = await db.contract.findUnique({
      where: { id: contractId },
      include: {
        renewals: {
          orderBy: 
```

## `create.ts`

```ts
// Path: /actions/contracts/create.ts
// Path: /actions/contracts/create.ts
'use server';

import { db } from '@/lib/db';
import { contractSchema } from '@/schemas/contract';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import type { ContractFormData } from '@/schemas/contract';
import { ContractType } from '@prisma/client';

export async function createContract(data: ContractFormData) {
  try {
    // Debug logs to help troubleshoot form submissions
    console.log("[CREATE_CONTRACT] Received data:", {
      ...data,
      startDate: data.startDate,
      endDate: data.endDate,
      operatorId: data.operatorId,
      operatorRevenue: data.operatorRevenue,
      isRevenueSharing: data.isRevenueSharing,
      services: data.services?.length
    });

    // Validate contract type
    const validContractTypes = Object.values(ContractType);
    if (!validContractTypes.includes(data.type)) {
      return {
        error: "Invalid contract type",
        detai
```

## `create-reminder.ts`

```ts
// /actions/contracts/create-reminder.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { z } from 'zod';


const createReminderSchema = z.object({
    contractId: z.string().min(1, "Contract ID is required"),
    remindAt: z.string().datetime({ message: "Valid datetime string is required" }),
    message: z.string().optional(),
    isDismissed: z.boolean().default(false),
});


type CreateReminderFormData = z.infer<typeof createReminderSchema>;


export async function createReminder(data: CreateReminderFormData): Promise<{ success?: string; reminderId?: string; error?: string; details?: any }> {
    const validationResult = createReminderSchema.safeParse(data);

    if (!validationResult.success) {
        return {
            error: 'Validation failed.',
            details: validationResult.error.format(),
        };
    }

    const validatedData = validationResult.data;

    try {

        const reminder = await db.reminder.create
```

## `delete.ts`

```ts
// Path: /actions/contracts/delete.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { LogSeverity } from '@prisma/client'; // Uvezite LogSeverity

export const deleteContract = async (id: string) => {
  // 1. Dobijanje ID-a trenutnog korisnika i provera sesije
  const session = await auth();
  const userId = session?.user?.id;

  if (!userId) {
      // Opciono: rigoroznija provera autorizacije pre brisanja
      return { error: "Unauthorized. Please sign in to delete a contract.", success: false }; // Dodajte success: false
  }

  try {
    // 2. Provera da li ugovor postoji pre brisanja i dohvatanje detalja za logovanje
    const existingContract = await db.contract.findUnique({
      where: { id },
      select: { // Selektujte polja koja ce vam trebati za log detalje
          id: true,
          contractNumber: true,
          name: true,
          // Dodajte ostala polja ako zelite da ih logujete
 
```

## `delete-attachment.ts`

```ts
///actions/contracts/delete-attachment.ts

'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';

export const deleteAttachment = async (contractId: string, attachmentId: string) => {
  const session = await auth();
  const userId = session?.user?.id;

  if (!userId) {
    return { error: "Unauthorized" };
  }

  try {
    // Verify user has permission to delete
    const contract = await db.contract.findUnique({
      where: { id: contractId },
      select: { createdById: true }
    });

    if (!contract) {
      return { error: "Contract not found" };
    }

    // Only contract owner or admin can delete
    if (contract.createdById !== userId && session.user.role !== 'ADMIN') {
      return { error: "Unauthorized" };
    }

    // Delete the attachment
    await db.contractAttachment.delete({
      where: { id: attachmentId }
    });

    // Revalidate cache
    revalidatePath(`/contracts/${contractId}`);
    reva
```

## `get-expiring-contracts.ts`

```ts
// actions/contracts/get-expiring-contracts.ts
import { db } from "@/lib/db";
import { addDays, subDays, startOfDay, endOfDay } from 'date-fns';

export async function getExpiringContracts(daysThreshold: number = 60) { // ✅ Changed from 30 to 60
  try {
    const today = startOfDay(new Date());
    
    // ✅ Add support for recently expired contracts (60 days ago)
    const pastThreshold = startOfDay(subDays(today, daysThreshold));
    const futureThreshold = endOfDay(addDays(today, daysThreshold));
    
    const contracts = await db.contract.findMany({
      where: {
        OR: [
          // ✅ Contracts expiring soon (within 60 days)
          {
            status: 'ACTIVE',
            endDate: {
              gte: today,
              lte: futureThreshold,
            },
          },
          // ✅ Recently expired contracts (within last 60 days)
          {
            status: 'EXPIRED',
            endDate: {
              gte: pastThreshold,
              lt: today,
          
```

## `get-expiring-contracts-timeline.ts`

```ts
// actions/contracts/get-expiring-contracts-timeline.ts
import { db } from "@/lib/db";
import { addDays, startOfDay, endOfDay } from 'date-fns'; // Fixed import

export async function getExpiringContractsTimeline(daysThreshold: number = 30) {
  try {
    const today = startOfDay(new Date());
    const expiryDateThreshold = endOfDay(addDays(today, daysThreshold));

    const contracts = await db.contract.findMany({
      where: {
        status: 'ACTIVE',
        endDate: {
          gte: today,
          lte: expiryDateThreshold,
        },
      },
      select: { // Changed from include to select for scalar fields
        endDate: true,
        type: true, // This is a scalar field, not a relation
      },
    });

    // Debug log
    console.log(`Found ${contracts.length} contracts for timeline`);

    // Grupisanje po mesecima
    const grouped = contracts.reduce((acc, contract) => {
      const monthKey = contract.endDate.toISOString().slice(0, 7); // YYYY-MM
      
      if (!ac
```

## `getProviderContractsForLinking.ts`

```ts
///actions/contracts/getProviderContractsForLinking.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";

interface ContractForLinking {
  id: string;
  name: string;
  contractNumber: string;
}

interface GetProviderContractsForLinkingResult {
  success: boolean;
  data?: ContractForLinking[];
  error?: string;
}

export async function getProviderContractsForLinking(
  providerId: string
): Promise<GetProviderContractsForLinkingResult> {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    if (!providerId) {
      return { success: false, error: "Provider ID is required." };
    }

    const contracts = await db.contract.findMany({
      where: {
        providerId: providerId,
        // Opciono: filtrirajte samo aktivne ugovore ili ugovore određenog tipa
        // status: "ACTIVE",
      },
      select: {
        id: true,
        name: true,
    
```

## `linkServiceToContract.ts`

```ts
//actions/contracts/linkServiceToContract.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { z } from "zod";
import { revalidatePath } from "next/cache";
import { ActivityLogService } from "@/lib/services/activity-log-service";
import { LogSeverity } from "@prisma/client";

const linkServiceToContractSchema = z.object({
  serviceId: z.string().min(1, "Service ID is required."),
  contractId: z.string().min(1, "Contract ID is required."),
});

interface LinkServiceToContractResult {
  success: boolean;
  data?: { serviceContractId: string };
  error?: string;
}

export async function linkServiceToContract(
  params: z.infer<typeof linkServiceToContractSchema>
): Promise<LinkServiceToContractResult> {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    const validatedParams = linkServiceToContractSchema.safeParse(params);

    if (!validatedP
```

## `remove-service.ts`

```ts
// /actions/contracts/remove-service.ts
'use server';

import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth'; // Pretpostavljena putanja do vašeg auth helpera
import { z } from 'zod'; // Koristimo Zod za osnovnu validaciju ID-jeva

// Osnovna šema za validaciju ulaznih podataka
const RemoveServiceSchema = z.object({
  contractId: z.string().cuid("Invalid contract ID format"), // Pretpostavka da koristite CUIDs
  serviceId: z.string().cuid("Invalid service ID format"),   // Pretpostavka da koristite CUIDs
});

/**
 * Uklanja povezanost servisa sa ugovorom.
 * @param values - Objekat koji sadrži contractId i serviceId veze koju treba ukloniti.
 * @returns Uspeh/neuspeh operacije.
 */
export const removeContractService = async (values: z.infer<typeof RemoveServiceSchema>) => {
  // 1. Validacija ulaznih podataka
  const validatedFields = RemoveServiceSchema.safeParse(values);

  if (!va
```

## `services.ts`

```ts
// /actions/contracts/services.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { SelectedService } from '@/lib/types/contract-types';


interface AddContractServiceInput {
    contractId: string;
    serviceId: string;
    specificTerms?: string | null;
}

export async function addContractService(input: AddContractServiceInput): Promise<{ success?: string; newLink?: SelectedService; error?: string }> {
    if (!input.contractId || !input.serviceId) {
        return { error: 'Contract ID and Service ID are required.' };
    }

    try {
         const existingLink = await db.contractsOnServices.findUnique({
             where: {
                 contractId_serviceId: {
                     contractId: input.contractId,
                     serviceId: input.serviceId,
                 },
             },
             include: {
                  service: true,
             }
         });

         if (existingLink) {
             return
```

## `update.ts`

```ts
// /actions/contracts/update.ts
'use server';

import { z } from 'zod';
import { ContractType } from '@prisma/client';
import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { logActivity } from '@/lib/security/audit-logger';
import { contractSchema } from '@/schemas/contract';

export async function updateContract(contractId: string, data: any) {
  console.log('[UPDATE_CONTRACT] Starting update for:', contractId);
  console.log('[UPDATE_CONTRACT] Data keys:', Object.keys(data || {}));
  
  try {
    let session;  
    try {
      session = await auth();
    } catch (authError) {
      console.error('[UPDATE_CONTRACT] Auth error:', authError);
      return { 
        success: false, 
        error: 'Authentication failed. Please refresh the page and try again.' 
      };
    }
    
    if (!session?.user) {
      console.log('[UPDATE_CONTRACT] No session or user');
      return { success: false, error: 'Unauthorized - please lo
```

## `create.ts`

```ts
// Path: /actions/humanitarian-orgs/create.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { LogSeverity } from '@prisma/client';
import { humanitarianOrgSchema, HumanitarianOrgFormData } from '@/schemas/humanitarian-org';
import { z } from 'zod';


export const createHumanitarianOrg = async (values: HumanitarianOrgFormData) => {
    const validatedFields = humanitarianOrgSchema.safeParse(values);

    if (!validatedFields.success) {
        console.error("Validation failed:", validatedFields.error.errors);
        return { error: "Invalid fields!", details: validatedFields.error.format(), success: false };
    }

    const {
        name,
        contactName,
        email,
        phone,
        address,
        website,
        mission,
        isActive,
    } = validatedFields.data;

    const session = await auth();
    if (!session?.user || !['ADMIN', 'MANAGER'].includes(session.user.role)) {
    
```

## `delete.ts`

```ts
// Path: /actions/humanitarian-orgs/delete.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { LogSeverity } from '@prisma/client';
import { PrismaClientKnownRequestError } from '@prisma/client/runtime/library';


export const deleteHumanitarianOrg = async (id: string) => {
    const session = await auth();
    const userId = session?.user?.id;

    if (!userId) {
        return { error: "Unauthorized", success: false };
    }

    try {
        const existingOrganization = await db.humanitarianOrg.findUnique({
            where: { id },
            select: {
                id: true,
                name: true,
            }
        });

        if (!existingOrganization) {
            return { error: "Humanitarian organization not found.", success: false };
        }

        await db.humanitarianOrg.delete({
            where: { id },
        });

        await db.activityLog.create({
          data: {
```

## `get.ts`

```ts
// /actions/humanitarian-orgs/get.ts
'use server';

import { db } from '@/lib/db';
import { HumanitarianOrgWithDetails, HumanitarianOrgFilterOptions } from '@/lib/types/humanitarian-org-types';
import { auth } from '@/auth';
import { UserRole } from "@prisma/client";
import { currentRole } from '@/lib/auth'; // Pretpostavljena putanja do currentRole funkcije

interface GetOrgsParams extends HumanitarianOrgFilterOptions {
    page?: number;
    limit?: number;
}

export async function getHumanitarianOrgs(params: GetOrgsParams) {
    // U realnoj aplikaciji, dodali biste proveru autentifikacije/autorizacije
    const session = await auth();
    if (!session?.user) {
       // Vratite grešku ili preusmerite
        return { data: [], total: 0, error: "Unauthorized" };
     }

    // Provera uloge ako je potrebna za dohvatanje liste
     const role = await currentRole(); // Ako koristite currentRole()
     if (role !== UserRole.ADMIN && role !== UserRole.MANAGER && role !== UserRole.USER) 
```

## `update.ts`

```ts
// Path: /actions/humanitarian-orgs/update.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { LogSeverity } from '@prisma/client';
import { PrismaClientKnownRequestError } from '@prisma/client/runtime/library';
import { humanitarianOrgSchema, HumanitarianOrgFormData } from '@/schemas/humanitarian-org';
import { z } from 'zod';


export const updateHumanitarianOrg = async (id: string, values: HumanitarianOrgFormData) => {
    const validatedFields = humanitarianOrgSchema.safeParse(values);

    if (!validatedFields.success) {
        console.error("Validation failed:", validatedFields.error.errors);
        return { error: "Invalid fields!", details: validatedFields.error.format(), success: false };
    }

    const {
        name,
        contactPerson,
        email,
        phone,
        address,
        website,
        mission,
        isActive,
    } = validatedFields.data;

    const session = awa
```

## `create.ts`

```ts
// /actions/humanitarian-renewals/create.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { createHumanitarianRenewalSchema } from "@/schemas/humanitarian-renewal";
import { revalidatePath } from "next/cache";

export async function createHumanitarianRenewal(values: any) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { error: "Neautorizovan pristup" };
    }

    const validatedFields = createHumanitarianRenewalSchema.safeParse(values);
    if (!validatedFields.success) {
      return { error: "Neispravni podaci", details: validatedFields.error.flatten() };
    }

    const { contractId, humanitarianOrgId, ...data } = validatedFields.data;

    // Proveri da li ugovor postoji i pripada humanitarnoj organizaciji
    const contract = await db.contract.findFirst({
      where: {
        id: contractId,
        humanitarianOrgId: humanitarianOrgId,
        type: "HUMANITARIAN"
      }
    });

    if (!contract) 
```

## `delete.ts`

```ts
// /actions/humanitarian-renewals/delete.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { revalidatePath } from "next/cache";

export async function deleteHumanitarianRenewal(renewalId: string) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { error: "Neautorizovan pristup" };
    }

    // Proveri da li korisnik ima dozvolu za brisanje
    // (samo admin ili manager može da briše obnove)
    if (!["ADMIN", "MANAGER"].includes(session.user.role)) {
      return { error: "Nemate dozvolu za brisanje obnova" };
    }

    if (!renewalId) {
      return { error: "ID obnove je obavezan" };
    }

    // Pronađi obnovu pre brisanja
    const renewal = await db.humanitarianContractRenewal.findUnique({
      where: { id: renewalId },
      include: {
        contract: true,
        humanitarianOrg: true
      }
    });

    if (!renewal) {
      return { error: "Obnova nije pronađena" };
    }

    // Ne dozvoli bri
```

## `list.ts`

```ts
// /actions/humanitarian-renewals/list.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { renewalFiltersSchema } from "@/schemas/humanitarian-renewal";
import { RenewalStatistics, HumanitarianRenewalsList } from "@/lib/types/humanitarian-renewal-types";

export async function getHumanitarianRenewals(filters: any = {}) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { error: "Neautorizovan pristup" };
    }

    const validatedFilters = renewalFiltersSchema.safeParse(filters);
    if (!validatedFilters.success) {
      return { error: "Neispravni filteri" };
    }

    const { status, organizationId, contractId, dateFrom, dateTo, page, limit } = validatedFilters.data;

    // Kreiraj where uslov
    const where: any = {};

    if (status) {
      where.subStatus = status;
    }

    if (organizationId) {
      where.humanitarianOrgId = organizationId;
    }

    if (contractId) {
      where.contractId = contra
```

## `update.ts`

```ts
// /actions/humanitarian-renewals/update.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { updateHumanitarianRenewalSchema } from "@/schemas/humanitarian-renewal";
import { revalidatePath } from "next/cache";

export async function updateHumanitarianRenewal(values: any) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return { error: "Neautorizovan pristup" };
    }

    const validatedFields = updateHumanitarianRenewalSchema.safeParse(values);
    if (!validatedFields.success) {
      return { error: "Neispravni podaci", details: validatedFields.error.flatten() };
    }

    const { id, contractId, humanitarianOrgId, ...data } = validatedFields.data;

    // Pronađi postojeću obnovu
    const existingRenewal = await db.humanitarianContractRenewal.findUnique({
      where: { id },
      include: {
        contract: true,
        humanitarianOrg: true
      }
    });

    if (!existingRenewal) {
      return { error: 
```

## `createLogEntry.ts`

```ts
//actions/log/createLogEntry.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { LogActionType, LogStatus, LogEntityType } from "@prisma/client"; // Import enums from Prisma client
import { z } from "zod"; // Assuming you use Zod for validation
// import { sendEmailToProvider } from "@/lib/email"; // Assuming you have an email sending utility

// Define the Zod schema for validating the input data
const createLogEntrySchema = z.object({
  entityType: z.nativeEnum(LogEntityType), // Must be one of the defined entity types
  entityId: z.string().min(1, "Entity ID is required"), // The ID of the specific entity

  action: z.nativeEnum(LogActionType), // Must be one of the defined action types
  subject: z.string().min(1, "Subject is required").max(255, "Subject is too long"),
  description: z.string().max(1000, "Description is too long").optional().nullable(),

  sendEmail: z.boolean().default(false),
  status: z.nativeEnum(LogStatus).de
```

## `getLogEntries.ts`

```ts
// /actions/log/getLogEntries.ts
"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { LogEntityType, LogActionType, LogStatus } from "@prisma/client";
import { z } from "zod";

const getLogEntriesSchema = z.object({
  entityType: z.nativeEnum(LogEntityType),
  entityId: z.string().optional().nullable(),

  action: z.nativeEnum(LogActionType).optional().nullable().transform(e => e === 'ALL' ? undefined : e),
  status: z.nativeEnum(LogStatus).optional().nullable().transform(e => e === 'ALL' ? undefined : e),
  subjectKeyword: z.string().optional().nullable().transform(e => e?.trim() === '' ? undefined : e),
  dateFrom: z.preprocess((arg) => { if (typeof arg == "string" || arg instanceof Date) return new Date(arg); }, z.date().optional().nullable()),
  dateTo: z.preprocess((arg) => { if (typeof arg == "string" || arg instanceof Date) return new Date(arg); }, z.date().optional().nullable()),

  page: z.number().int().min(1).default(1),
  limit
```

## `updateLogStatus.ts`

```ts
//actions/log/updateLogStatus.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { LogStatus } from "@prisma/client";
import { z } from "zod";

const updateLogStatusSchema = z.object({
  id: z.string(),
  status: z.nativeEnum(LogStatus),
});

export type UpdateLogStatusInput = z.infer<typeof updateLogStatusSchema>;

interface UpdateLogStatusResult {
    success: boolean;
    data?: {
        id: string;
        status: LogStatus;
    };
    error?: string;
}

export async function updateLogStatus(
  params: UpdateLogStatusInput
): Promise<UpdateLogStatusResult> {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    const validatedParams = updateLogStatusSchema.safeParse(params);

    if (!validatedParams.success) {
      console.error("Update log status validation failed:", validatedParams.error.errors);
      return { success: false, error: "
```

## `get-by-user-id.ts`

```ts
// Path: actions/notifications/get-by-user-id.ts
"use server";

import { db } from "@/lib/db"; // Assuming your Prisma client is imported from here
import { auth } from "@/auth"; // Assuming your auth config is here
import { Notification } from "@/lib/types/notification-types"; // Import your Notification type
import { UserRole } from "@prisma/client"; // Import UserRole if used for checks

/**
 * Fetches notifications for a specific user ID.
 * Includes basic authorization check to ensure the user is authenticated.
 * Note: For stricter security, you might want to ensure the user requesting
 * notifications is the owner of those notifications, unless they have an admin/manager role.
 * @param userId - The ID of the user whose notifications to fetch.
 * @returns A promise resolving to an array of Notification objects or an error object.
 */
export async function getNotificationsByUserId(userId: string): Promise<Notification[] | { error: string }> {
  try {
    // Optional: Basic check 
```

## `get-preferences.ts`

```ts
// Path: actions/notifications/get-preferences.ts
"use server";

// !!! PROVERITE DA JE OVAJ IMPORT ISPRAVAN i da fajl /lib/db.ts postoji i izvozi 'db' !!!
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { NotificationPreferences } from "@/lib/types/notification-types"; // Updated type
import { UserRole } from "@prisma/client";

/**
 * Fetches user notification preferences or returns default if not found.
 * @param userId - The ID of the user.
 * @returns A promise resolving to NotificationPreferences object or an error.
 */
export async function getUserNotificationPreferences(userId: string): Promise<NotificationPreferences | { error: string }> {
    try {
        const session = await auth();
        if (!session || session.user?.id !== userId) {
             return { error: "Unauthorized" };
        }

        // !!! OVO JE LINIJA GDE PUCANJE ZBOG UNDEFINED 'db' UKAZUJE NA PROBLEM SA /lib/db.ts ILI IMPORTOM !!!
        // Ako je import iznad ispravan, onda je pr
```

## `mark-as-read.ts`

```ts
///actions/notifications/mark-as-read.ts


"use server";

import { z } from "zod";
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";

// Schema for validation
const MarkAsReadSchema = z.object({
  id: z.string().optional(),
  markAllAsRead: z.boolean().optional()
});

export type MarkAsReadInput = z.infer<typeof MarkAsReadSchema>;

export async function markAsRead(data: MarkAsReadInput) {
  try {
    const validatedData = MarkAsReadSchema.parse(data);
    
    // Get the current user
    const session = await auth();
    if (!session?.user?.id) {
      return { error: "Unauthorized" };
    }
    
    const userId = session.user.id;
    
    // If marking all as read
    if (validatedData.markAllAsRead) {
      await db.notification.updateMany({
        where: {
          userId,
          isRead: false
        },
        data: {
          isRead: true
        }
      });
      
      revalidatePath("/notifications");
      return
```

## `update-preferences.ts`

```ts
// Path: actions/notifications/update-preferences.ts
"use server";

import { z } from "zod";
import { auth } from "@/auth";
import { db } from "@/lib/db"; // Proverite da je import ispravan i da /lib/db.ts radi
import { revalidatePath } from "next/cache";
import { NotificationPreferences, NotificationType } from "@/lib/types/notification-types";

// Schema koja odgovara NotificationPreferences interfejsu
const NotificationPreferencesSchema = z.object({
    userId: z.string(),
    contractExpiring: z.object({ inApp: z.boolean(), email: z.boolean() }),
    contractRenewalStatusChange: z.object({ inApp: z.boolean(), email: z.boolean() }),
    complaintAssigned: z.object({ inApp: z.boolean(), email: z.boolean() }),
    complaintUpdated: z.object({ inApp: z.boolean(), email: z.boolean() }),
    reminder: z.object({ inApp: z.boolean(), email: z.boolean() }),
    system: z.object({ inApp: z.boolean(), email: z.boolean() }),
    emailNotifications: z.boolean(),
    inAppNotifications: z.boolea
```

## `create.ts`

```ts
// actions/operators/create.ts

"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { operatorSchema, type OperatorFormValues } from "@/schemas/operator";
import { revalidatePath } from "next/cache";

export async function createOperator(data: OperatorFormValues) {
  console.log("[CREATE_OPERATOR] Function called with data:", data);
  
  try {
    const session = await auth();
    
    if (!session?.user) {
      console.log("[CREATE_OPERATOR] No session found");
      return { error: "Unauthorized" };
    }

    console.log("[CREATE_OPERATOR] User authenticated:", session.user.email);
    
    // Validate form data
    const validatedData = operatorSchema.safeParse(data);
    
    if (!validatedData.success) {
      console.log("[CREATE_OPERATOR] Validation failed:", validatedData.error.format());
      return { 
        error: "Invalid operator data", 
        details: validatedData.error.format() 
      };
    }

    console.log("[CREATE_OPERATOR] Dat
```

## `delete.ts`

```ts
// actions/operators/delete.ts


"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";

export async function deleteOperator(operatorId: string) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return { error: "Unauthorized" };
    }
    
    // Check if the user has admin role
    if (session.user.role !== "ADMIN" && session.user.role !== "MANAGER") {
      return { error: "Insufficient permissions" };
    }
    
    // Check if the operator exists
    const existingOperator = await db.operator.findUnique({
      where: { id: operatorId },
      include: {
        contracts: true
      }
    });
    
    if (!existingOperator) {
      return { error: "Operator not found" };
    }
    
    // Check if operator has associated contracts
    if (existingOperator.contracts.length > 0) {
      return { 
        error: "Cannot delete operator with associated contracts", 
        contracts: exi
```

## `getAllOperators.ts`

```ts
// Path: actions/operators/getAllOperators.ts
'use server';

import { db } from '@/lib/db';
import { auth } from '@/auth';
import { UserRole, Operator } from "@prisma/client";
import { currentRole } from '@/lib/auth'; // Pretpostavljena putanja do currentRole funkcije

/**
 * Dohvata listu svih aktivnih operatora za upotrebu u dropdown menijima.
 * @returns Niz operatora (ID i name) ili prazan niz u slučaju greške/nedostatka dozvola.
 */
export async function getAllOperators(): Promise<Pick<Operator, 'id' | 'name'>[]> {
    // Provera autentifikacije
    const session = await auth();
    if (!session?.user) {
        console.error("[getAllOperators] Unauthorized access attempt.");
        return [];
    }

    // Opciona provera autorizacije (koje uloge mogu da vide listu operatora?)
    // Pretpostavljamo da svi ulogovani korisnici mogu videti operatere za potrebe ugovora.
    // const role = await currentRole();
    // if (role !== UserRole.ADMIN && role !== UserRole.MANAGER && role 
```

## `getContractsByOperatorId.ts`

```ts
// Path: actions/operators/getContractsByOperatorId.ts
'use server';

import { db } from '@/lib/db';
import { auth } from '@/auth';
import { UserRole } from "@prisma/client";
import { currentRole } from '@/lib/auth'; // Pretpostavljena putanja do currentRole funkcije
import { Contract } from '@prisma/client'; // Uvezite Contract tip

/**
 * Dohvata listu ugovora povezanih sa određenim operaterom.
 * @param operatorId ID operatera.
 * @returns Niz ugovora ili prazan niz u slučaju greške/nedostatka dozvola.
 */
export async function getContractsByOperatorId(operatorId: string): Promise<Contract[]> {
    // Provera autentifikacije
    const session = await auth();
    if (!session?.user) {
        console.error("[getContractsByOperatorId] Unauthorized access attempt.");
        // Vratite prazan niz umesto greške za bolji UX na klijentu
        return [];
    }

    // Opciona provera autorizacije (koje uloge mogu da vide ugovore operatera?)
    // const role = await currentRole();
    //
```

## `getOperatorById.ts`

```ts
// Path: actions/operators/getOperatorById.ts
'use server';

import { db } from "@/lib/db";
import { Operator } from "@prisma/client"; // Uvezite Operator tip iz Prisme

/**
 * Dohvata detalje operatera po ID-u.
 * Uključuje povezane ugovore i njihov broj.
 * @param id ID operatera.
 * @returns Objekat operatera sa uključenim ugovorima i brojem ugovora.
 * @throws Error ako operater nije pronađen ili dođe do greške pri dohvatanju.
 */
export async function getOperatorById(id: string) {
  try {
    // Dohvatite operatera po jedinstvenom ID-u
    const operator = await db.operator.findUnique({
      where: {
        id, // Filtrirajte po prosleđenom ID-u
      },
      include: {
        // Uključite povezane ugovore
        contracts: {
          select: {
            id: true,
            name: true,
            contractNumber: true,
            startDate: true,
            endDate: true,
            revenuePercentage: true,
            operatorRevenue: true,
            status: true, 
```

## `getOperators.ts`

```ts
// Path: actions/operators/getOperators.ts
'use server';

import { db } from "@/lib/db";
// Uvezite Operator tip ako je definisan u types fajlu
import { Operator } from "@prisma/client"; // Ili direktno iz Prisme ako ga koristite

// Definirajte tip za parametre, slično kao u page.tsx
interface GetOperatorsParams {
  search?: string;
  active?: "all" | "active" | "inactive";
  sortBy?: "name" | "code" | "createdAt";
  sortOrder?: "asc" | "desc";
  page?: number;
  pageSize?: number;
}

/**
 * Dohvata listu operatora sa opcijama filtriranja, sortiranja i paginacije.
 * @param params Objekat sa parametrima za pretragu, status, sortiranje i paginaciju.
 * @returns Objekat sa listom operatora, ukupnim brojem, brojem stranica i trenutnom stranicom.
 */
export async function getOperators(params: GetOperatorsParams) {
  try {
    const {
      search = "",
      active = "all",
      sortBy = "name",
      sortOrder = "asc",
      page = 1,
      pageSize = 10
    } = params;

    // Build wh
```

## `index.ts`

```ts
// Path: actions/operators/index.ts

"use server";

import { db } from "@/lib/db";
import { OperatorPayload } from "@/lib/types/operator-types";
import { operatorSchema } from "@/schemas/operator";
import { revalidatePath } from "next/cache";
import { getCurrentUser } from "@/lib/auth";
import { redirect } from "next/navigation";

import { getContractsByOperatorId } from "./getContractsByOperatorId";
import { createOperator } from "./create";
import { updateOperator } from "./update";
import { deleteOperator } from "./delete";
import { getOperators } from "./getOperators";
import { getOperatorById } from "./getOperatorById";
import { getAllOperators } from "./getAllOperators";

export { createOperator };
export { updateOperator };
export { deleteOperator };
export { getOperators };
export { getOperatorById };
export { getContractsByOperatorId };
export { getAllOperators };

/*
export async function createOperator(...) { ... }
export async function updateOperator(...) { ... }
export asy
```

## `update.ts`

```ts
// actions/operators/update.ts

"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { OperatorFormValues } from "@/lib/types/operator-types";
import { operatorSchema } from "@/schemas/operator";
import { revalidatePath } from "next/cache";

export async function updateOperator(id: string, data: OperatorFormValues) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return { error: "Unauthorized" };
    }
    
    // Validate form data
    const validatedData = operatorSchema.safeParse(data);
    
    if (!validatedData.success) {
      return { error: "Invalid operator data", errors: validatedData.error.format() };
    }
    
    const { name, code, description, logoUrl, website, contactEmail, contactPhone, active } = validatedData.data;
    
    // Check if operator with the same code already exists (excluding current)
    const existingOperator = await db.operator.findFirst({
      where: {
        code,
        id: { not: 
```

## `get-humanitarian.ts`

```ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function getHumanitarianOrgs() {
  try {
    const session = await auth();
    if (!session?.user) {
      throw new Error("Unauthorized access");
    }

    const organizations = await db.humanitarianOrg.findMany({
      orderBy: {
        name: "asc",
      },
      select: {
        id: true,
        name: true,
        contactName: true,
        email: true,
        phone: true,
        address: true,
        website: true,
      },
      where: {
        isActive: true,
      },
    });

    return organizations;
  } catch (error) {
    console.error("Error fetching humanitarian organizations:", error);
    throw error;
  }
}
```

## `create.ts`

```ts
//actions/parking-services/create.ts
"use server";

import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { getCurrentUser } from "@/lib/auth";
import { CreateParkingServiceParams } from "@/lib/types/parking-service-types";
import { createParkingServiceSchema } from "@/schemas/parking-service";
import { logActivity } from "@/lib/security/audit-logger"; // Corrected import

export async function create(data: CreateParkingServiceParams) {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      // For create actions, unauthorized means the user cannot proceed
      return { success: false, error: "Unauthorized" };
    }

    const validatedData = createParkingServiceSchema.parse(data);

    // Filter out empty additional emails if they exist
    if (validatedData.additionalEmails) {
      validatedData.additionalEmails = validatedData.additionalEmails.filter(
        email => email && email.trim() !== ''
      );
    }

    const 
```

## `delete.ts`

```ts
//actions/parking-services/delete.ts

//actions/parking-services/delete.ts

"use server";

import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { getCurrentUser } from "@/lib/auth";
import { logActivity } from "@/lib/security/audit-logger"; // Corrected import

export async function deleteService(id: string) {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    const existingParkingService = await db.parkingService.findUnique({
      where: { id },
      include: {
        contracts: true,
      }
    });

    if (!existingParkingService) {
      return { success: false, error: "Parking service not found" };
    }

    if (existingParkingService.contracts.length > 0) {
      return {
        success: false,
        error: "Cannot delete parking service with associated contracts"
      };
    }

    await db.parkingService.delete({
      where: { id },
    });

    revalidatePath(
```

## `getActiveContractsCount.ts`

```ts
// actions/parking-services/getActiveContractsCount.ts
import { db } from "@/lib/db";

export async function getActiveContractsCount(parkingServiceId: string) {
  try {
    return await db.contract.count({
      where: {
        parkingServiceId,
        status: "ACTIVE",
      },
    });
  } catch (error) {
    console.error("Error fetching active contracts count:", error);
    return 0;
  }
}
```

## `getAllParkingServices.ts`

```ts
//actions/parking-services/getAllParkingServices.ts


"use server";

import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
// import { ActivityLogCreate } from "@/lib/activity-log";
import { getCurrentUser } from "@/lib/auth";

/**
 * Get all parking services
 * @returns Array of all parking services
 */
export async function getAllParkingServices() {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    const parkingServices = await db.parkingService.findMany({
      orderBy: {
        name: "asc",
      },
    });

    // Log the activity
    // await ActivityLogCreate({
    //  action: "GET_ALL_PARKING_SERVICES",
    //  entityType: "parking_service",
    //  userId: currentUser.id,
    //  details: `Retrieved all parking services`,
   // });

    return { success: true, data: parkingServices };
  } catch (error) {
    console.error("Error fetching parking services:", error);
    return
```

## `getAvgDailyParkingRevenue.ts`

```ts
// actions/parking-services/getAvgDailyParkingRevenue.ts
import { db } from "@/lib/db";
import { subDays } from "date-fns";

export async function getAvgDailyParkingRevenue(parkingServiceId: string): Promise<number> {
  try {
    // Get revenue for the last 30 days
    const thirtyDaysAgo = subDays(new Date(), 30);
    
    const result = await db.parkingTransaction.aggregate({
      where: { 
        parkingServiceId,
        date: { gte: thirtyDaysAgo }
      },
      _sum: { amount: true }
    });
    
    const totalRevenue = result._sum.amount || 0;
    return totalRevenue / 30;
  } catch (error) {
    console.error("Error fetching average daily parking revenue:", error);
    return 0;
  }
}
```

## `getContractsByParkingServiceId.ts`

```ts
//actions/parking-services/getContractsByParkingServiceId.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
// import { ActivityLogCreate } from "@/lib/activity-log";

/**
 * Get contracts associated with a parking service
 * @param parkingServiceId - Parking service ID
 * @returns List of contracts associated with the parking service
 */
export async function getContractsByParkingServiceId(parkingServiceId: string) {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      throw new Error("Unauthorized");
    }

    // Verify parking service exists
    const parkingService = await db.parkingService.findUnique({
      where: { id: parkingServiceId },
    });

    if (!parkingService) {
      return { success: false, error: "Parking service not found" };
    }

    // Fetch contracts associated with the parking service
    const contracts = await db.contract.findMany({
      where: { parkingServiceId },
      sele
```

## `getContractsCount.ts`

```ts
//actions/parking-services/getContractsCount.ts


'use server';

import { db } from '@/lib/db';

export async function getContractsCountForParkingService(parkingServiceId: string) {
  try {
    const count = await db.contract.count({
      where: {
        parkingServiceId: parkingServiceId,
      }
    });
    return count;
  } catch (error) {
    console.error('Error fetching contracts count:', error);
    return 0;
  }
}
```

## `getMonthlyRevenueStats.ts`

```ts
// actions/parking-services/getMonthlyRevenueStats.ts
import { db } from "@/lib/db";

export interface MonthlyRevenueStat {
  month_start: Date;
  month_year: string;
  total_amount: number;
  total_quantity: number;
  average_price: number;
}

export async function getMonthlyRevenueStats(parkingServiceId: string): Promise<MonthlyRevenueStat[]> {
  try {
    return await db.$queryRaw`
      SELECT
        DATE_TRUNC('month', "date") AS month_start,
        TO_CHAR(DATE_TRUNC('month', "date"), 'YYYY-MM') AS month_year,
        SUM(amount) AS total_amount,
        SUM(quantity) AS total_quantity,
        AVG(price) AS average_price
      FROM "ParkingTransaction"
      WHERE "parkingServiceId" = ${parkingServiceId}
      GROUP BY DATE_TRUNC('month', "date")
      ORDER BY DATE_TRUNC('month', "date") DESC
    `;
  } catch (error) {
    console.error("Error fetching monthly revenue statistics:", error);
    return [];
  }
}
```

## `getParkingServiceById.ts`

```ts
//actions/parking-services/getParkingServiceById.ts
"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";


export async function getParkingServiceById(id: string) {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    const parkingService = await db.parkingService.findUnique({
      where: { id },
    });

    if (!parkingService) {
      return { success: false, error: "Parking service not found" };
    }

    

    return { success: true, data: parkingService };
  } catch (error) {
    console.error("Error fetching parking service:", error);
    return {
      success: false,
      error: error instanceof Error ? error.message : "Failed to fetch parking service",
    };
  }
}

```

## `getParkingServices.ts`

```ts
//actions/parking-services/getParkingServices.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { ParkingServiceFilters, PaginatedParkingServices } from "@/lib/types/parking-service-types";
import { parkingServiceFiltersSchema } from "@/schemas/parking-service";

export async function getParkingServices(
  filters: ParkingServiceFilters = {}
): Promise<{ success: boolean; data?: PaginatedParkingServices; error?: string }> {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      // For data retrieval actions, you might return an empty list or specific error
      // depending on your security policy for unauthorized users.
      // Returning an error might be safer if the data is sensitive.
      return { success: false, error: "Unauthorized" };
    }

    const validatedFilters = parkingServiceFiltersSchema.parse(filters);
    const {
      searchTerm,
      isActive,
      sortBy = "name",
      sortDir
```

## `getParkingServiceStats.ts`

```ts
// actions/parking-services/getParkingServiceStats.ts
import { db } from "@/lib/db";

export async function getParkingServiceStats(parkingServiceId: string) {
  try {
    const [totalTransactions, activeContracts] = await Promise.all([
      db.parkingTransaction.count({
        where: { parkingServiceId }
      }),
      db.contract.count({
        where: { 
          parkingServiceId,
          status: "ACTIVE"
        }
      })
    ]);
    
    return {
      totalTransactions,
      activeContracts
    };
  } catch (error) {
    console.error("Error fetching parking service stats:", error);
    return {
      totalTransactions: 0,
      activeContracts: 0
    };
  }
}
```

## `getServicesCount.ts`

```ts
//actions/parking-services/getServicesCount.ts

'use server';
import { db } from '@/lib/db';

export async function getServicesCountForParkingService(parkingServiceId: string) {
  try {
    // Alternativni pristup - prvo nalazimo jedinstvene service ID-jeve
    const contracts = await db.contract.findMany({
      where: {
        parkingServiceId: parkingServiceId
      },
      select: {
        services: {
          select: {
            serviceId: true
          }
        }
      }
    });
    
    // Izvlačimo jedinstvene service ID-jeve
    const serviceIds = new Set();
    contracts.forEach(contract => {
      contract.services.forEach(service => {
        serviceIds.add(service.serviceId);
      });
    });
    
    return serviceIds.size;
  } catch (error) {
    console.error('Error fetching services count:', error);
    return 0;
  }
}
```

## `getServicesForParkingServiceContracts.ts`

```ts
//actions/parking-services/getServicesForParkingServiceContracts.ts

'use server';

import { db } from '@/lib/db';

export async function getServicesForParkingServiceContracts(
  parkingServiceId: string,
  showAll: boolean = false
) {
  try {
    // Fetch services with their contract count
    const services = await db.service.findMany({
      where: {
        parkingServiceId: parkingServiceId,
      },
      include: {
        _count: {
          select: {
            contracts: true
          }
        }
      }
    });

    // Filter services based on whether they're linked to contracts
    const filteredServices = showAll 
      ? services 
      : services.filter(service => service._count.contracts === 0);

    return {
      success: true,
      data: filteredServices.map(service => ({
        id: service.id,
        name: service.name,
        type: service.type,
        description: service.description,
        contractCount: service._count.contracts
      }))
    };
  } catc
```

## `getTotalParkingRevenue.ts`

```ts
// actions/parking-services/getTotalParkingRevenue.ts
import { db } from "@/lib/db";

export async function getTotalParkingRevenue(parkingServiceId: string): Promise<number> {
  try {
    const result = await db.parkingTransaction.aggregate({
      where: { parkingServiceId },
      _sum: { amount: true }
    });
    
    return result._sum.amount || 0;
  } catch (error) {
    console.error("Error fetching total parking revenue:", error);
    return 0;
  }
}
```

## `index.ts`

```ts
//actions/parking-services/index.ts

// Export all parking service actions
export * from "./getAllParkingServices";
export * from "./getParkingServiceById";
export * from "./getActiveContractsCount";
export * from "./getParkingServices";
export * from "./getContractsByParkingServiceId";
export * from "./getServicesForParkingServiceContracts";
export * from "./getServicesCount";
export * from "./getContractsCount";
export * from "./getTotalParkingRevenue";
export * from "./getAvgDailyParkingRevenue";
export * from "./getParkingServiceStats";
export * from "./getMonthlyRevenueStats";
export * from "./create";
export * from "./update";
export * from "./delete";
```

## `update.ts`

```ts
//actions/parking-services/update.ts
"use server";

import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { getCurrentUser } from "@/lib/auth";
import { UpdateParkingServiceParams } from "@/lib/types/parking-service-types";
import { updateParkingServiceSchema } from "@/schemas/parking-service";
import { logActivity } from "@/lib/security/audit-logger";

export async function update(data: UpdateParkingServiceParams) {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    const validatedData = updateParkingServiceSchema.parse(data);
    const { id, ...updateData } = validatedData;

    const existingParkingService = await db.parkingService.findUnique({
      where: { id },
    });

    if (!existingParkingService) {
      return { success: false, error: "Parking service not found" };
    }

    const updatedParkingService = await db.parkingService.update({
      where
```

## `delete.ts`

```ts
// actions/products/delete.ts
'use server';

import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";
import { logActivity } from "@/lib/security/audit-logger";
import { getCurrentUser } from "@/lib/security/auth-helpers";

interface DeleteProductResult {
  success?: string;
  error?: string;
}

/**
 * Server action to delete a product
 * Includes proper authorization, validation, and audit logging
 */
export async function deleteProduct(productId: string): Promise<DeleteProductResult> {
  try {
    // Check authentication
    const user = await getCurrentUser();
    if (!user) {
      return { error: "Unauthorized. Please log in." };
    }

    // Validate input
    if (!productId || typeof productId !== 'string') {
      return { error: "Invalid product ID provided." };
    }

    // Check if product exists
    const existingProduct = await db.product.findUnique({
      where: { id: productId },
      include: {
        _count: {
          select: {
            com
```

## `get-by-service.ts`

```ts
// /actions/products/get-by-service.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function getProductsByService(serviceId?: string) {
  const session = await auth();
  if (!session?.user) {
    throw new Error("Unauthorized access");
  }

  // If no service ID is provided, return all active products
  if (!serviceId) {
    return db.product.findMany({
      where: { isActive: true },
      orderBy: { name: "asc" },
      select: {
        id: true,
        name: true,
        code: true,
        description: true,
      },
    });
  }

  // First, fetch complaints that are associated with the given service
  const complaintsWithService = await db.complaint.findMany({
    where: {
      serviceId,
      productId: { not: null },
    },
    select: {
      productId: true,
    },
    distinct: ["productId"],
  });

  // Extract product IDs
  const productIds = complaintsWithService.map(c => c.productId).filter(Boolean) as string[];

  // If
```

## `create.ts`

```ts
// /actions/providers/create.ts
'use server';

import { db } from '@/lib/db';
import { providerSchema, ProviderFormData } from '@/schemas/provider';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { LogSeverity } from '@prisma/client';
import { z } from 'zod';

export async function createProvider(data: ProviderFormData): Promise<{ success?: string; id?: string; error?: string; details?: any, success: boolean, message?: string }> {
    const validationResult = providerSchema.safeParse(data);

    if (!validationResult.success) {
        console.error("Validation failed:", validationResult.error.format());
        return {
            error: 'Validation failed.',
            details: validationResult.error.format(),
            success: false
        };
    }

    const validatedData = validationResult.data;

    const session = await auth();
    const userId = session?.user?.id;

    if (!userId) {
         return { error: "Unauthorized", success: fal
```

## `delete.ts`

```ts
// Path: /actions/providers/delete.ts
'use server';

import { db } from '@/lib/db';
import { auth } from '@/auth';
import { currentRole } from "@/lib/auth";
import { UserRole, LogSeverity } from "@prisma/client";
import { PrismaClientKnownRequestError } from '@prisma/client/runtime/library';
import { revalidatePath } from 'next/cache';


export async function deleteProvider(id: string): Promise<{ success?: boolean; error?: string, message?: string }> {
    const session = await auth();
    const userId = session?.user?.id;

    if (!userId) {
         return { error: "Unauthorized", success: false };
    }

    const role = await currentRole();
    if (role !== UserRole.ADMIN) {
       return { error: "Forbidden", success: false };
    }

    try {
        const existingProvider = await db.provider.findUnique({
            where: { id },
            select: {
                id: true,
                name: true,
            }
        });

        if (!existingProvider) {
            re
```

## `get.ts`

```ts
// actions/providers/get.ts
'use server';

import db from '@/lib/db';
import { auth } from '@/auth';
import { currentRole } from '@/lib/auth';
import { UserRole } from '@prisma/client';
import { ProviderWithCounts, ProviderFilterOptions } from '@/lib/types/provider-types';

export async function getProviders(params: ProviderFilterOptions & {
  page?: number;
  limit?: number;
}) {
  try {
  const session = await auth();
    if (!session?.user) {
      return {
        data: [],
        total: 0,
        page: 1,
        limit: 10,
        error: "Unauthorized: Please login to access this resource"
      };
    }

    // Provera uloge
    const role = await currentRole();
    if (![UserRole.ADMIN, UserRole.MANAGER].includes(role!)) {
      return {
        data: [],
        total: 0,
        page: 1,
        limit: 10,
        error: "Forbidden: You don't have permission to access this resource"
      };
    }
    const { page = 1, limit = 10, ...filters } = params;
    
    // Build wh
```

## `getAllProviders.ts`

```ts
// actions/providers/getAllProviders.ts

// Uvoz tvoje Prisma klijent instance
import { db } from '@/lib/db';

// Opciona interfejs definicija za filtere koje funkcija prihvata
interface GetAllProvidersFilters {
  isActive?: boolean; // Omogućava filtriranje po isActive statusu
  // Dodaj ovde druge filtere koji bi mogli biti potrebni (npr. name search, itd.)
}

/**
 * Server-side funkcija za dohvatanje liste provajdera iz baze podataka.
 * Može opcionalno filtrirati rezultate.
 * Poziva se u Server Komponentama ili API rutama.
 */
export async function getAllProviders(filters?: GetAllProvidersFilters) {
  try {
    // Kreiranje objekta uslova za `where` klauzulu u Prisma upitu
    const where: any = {};

    // Primena isActive filtera ako je prosleđen
    // NAPOMENA: Na osnovu prethodne Prisma šeme koju si poslao, model `Provider`
    // NEMA `isActive` polje. Ako ti je ovo filtriranje zaista potrebno, moraćeš
    // ili da dodaš `isActive` polje u Prisma šemu za Provider model,
   
```

## `get-by-service.ts`

```ts
// /actions/providers/get-by-service.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function getProvidersByService(serviceId?: string) {
  const session = await auth();
  if (!session?.user) {
    throw new Error("Unauthorized access");
  }

  if (!serviceId) {
    // Return all active providers if no service ID is provided
    return db.provider.findMany({
      where: { isActive: true },
      orderBy: { name: "asc" },
      select: {
        id: true,
        name: true,
        contactName: true,
        email: true,
        phone: true,
      },
    });
  }

  // Check if the service exists
  const service = await db.service.findUnique({
    where: { id: serviceId },
  });

  if (!service) {
    throw new Error("Service not found");
  }

  // Get providers associated with this service through contracts
  const contractsWithService = await db.serviceContract.findMany({
    where: {
      serviceId,
    },
    select: {
      contract: 
```

## `getProviderDetails.ts`

```ts
///actions/providers/getProviderDetails.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { logActivity } from "@/lib/security/audit-logger";
import { ProviderWithDetails } from '@/lib/types/provider-types'; // Assuming this type exists

export async function getProviderDetails(
  providerId: string
): Promise<{ success: boolean; data?: ProviderWithDetails; error?: string }> {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    const provider = await db.provider.findUnique({
      where: { id: providerId },
      include: {
        contracts: {
          select: {
            id: true,
            name: true,
            contractNumber: true,
            status: true,
            endDate: true,
            revenuePercentage: true,
            isRevenueSharing: true,
            operatorRevenue: true,
            operator: {
              sel
```

## `get-providers.ts`

```ts
// /actions/providers/get-providers.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function getProviders() {
  const session = await auth();
  if (!session?.user) {
    throw new Error("Unauthorized access");
  }

  return db.provider.findMany({
    where: { isActive: true },
    orderBy: { name: "asc" },
    select: {
      id: true,
      name: true,
      contactName: true,
      email: true,
      phone: true,
    },
  });
}
```

## `getServicesForNewContracts.ts`

```ts
//actions/providers/getServicesForNewContracts.ts

"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { Service, ServiceType } from "@prisma/client";

interface ServiceForContract {
  id: string;
  name: string;
  type: ServiceType;
  description: string | null;
}

interface GetServicesForNewContractsResult {
  success: boolean;
  data?: ServiceForContract[];
  error?: string;
}

export async function getServicesForNewContracts(
  providerId: string,
  includeAllServices: boolean = false // NOVI PARAMETAR: podrazumevano false
): Promise<GetServicesForNewContractsResult> {
  try {
    const currentUser = await getCurrentUser();
    if (!currentUser) {
      return { success: false, error: "Unauthorized" };
    }

    if (!providerId) {
      return { success: false, error: "Provider ID is required." };
    }

    let serviceIdsToExclude: Set<string> = new Set();

    // Ako ne želimo sve servise, dohvatamo ID-eve povezanih servisa koje treb
```

## `update.ts`

```ts
// /actions/providers/update.ts
'use server';

import { db } from '@/lib/db';
import { providerSchema, ProviderFormData } from '@/schemas/provider';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { LogSeverity } from '@prisma/client';
import { PrismaClientKnownRequestError } from '@prisma/client/runtime/library';
import { z } from 'zod';


export async function updateProvider(id: string, data: ProviderFormData): Promise<{ success?: boolean; id?: string; error?: string; details?: any, message?: string }> {
    const validationResult = providerSchema.safeParse(data);

    if (!validationResult.success) {
        console.error("Validation failed:", validationResult.error.format());
        return {
            error: 'Validation failed.',
            details: validationResult.error.format(),
            success: false
        };
    }

    const validatedData = validationResult.data;

    const session = await auth();
    const userId = session?.user?.i
```

## `generate-excel.ts`

```ts
///actions/reports/generate-excel.ts


"use server";

import { db } from "@/lib/db";
import { currentUser } from "@/lib/auth";
import { canGenerateReports } from "@/lib/security/permission-checker";
import { revalidatePath } from "next/cache";
import { excelGenerator } from "@/lib/reports/excel-generator";
import { createActivityLog } from "@/lib/security/audit-logger";

export type ReportParams = {
  reportType: string;
  startDate?: Date;
  endDate?: Date;
  filters?: Record<string, any>;
  columns?: string[];
};

export type ReportResult = {
  fileUrl: string;
  reportName: string;
  generatedAt: Date;
  recordCount: number;
};

export async function generateExcelReport({
  reportType,
  startDate,
  endDate,
  filters = {},
  columns = [],
}: ReportParams): Promise<ReportResult> {
  const user = await currentUser();

  if (!user) {
    throw new Error("Authentication required");
  }

  // Check if the user has permission to generate reports
  const hasPermission = await canGenerate
```

## `get-recent-reports.ts`

```ts
// actions/reports/get-recent-reports.ts
"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";

export async function getRecentReports(limit = 10) {
  try {
    // Get current user
    const user = await getCurrentUser();
    if (!user) {
      return { error: "Unauthorized access" };
    }
    
    // Get the most recently generated reports
    const reports = await db.generatedReport.findMany({
      orderBy: {
        generatedAt: "desc",
      },
      take: limit,
    });
    
    return { success: true, data: reports };
  } catch (error) {
    console.error("Failed to fetch recent reports:", error);
    return { error: "Failed to fetch recent reports. Please try again." };
  }
}

export async function getScheduledReports(params?: {
  isActive?: boolean;
  reportType?: string;
}) {
  try {
    // Get current user
    const user = await getCurrentUser();
    if (!user) {
      return { error: "Unauthorized access" };
    }
    
    // Build quer
```

## `getReportFileNames.ts`

```ts
//actions/reports/getReportFileNames.ts

import fs from 'fs/promises';
import path from 'path';
import { getParkingServiceById } from "../parking-services/getParkingServiceById";

export const getReportFileNames = async (parkingServiceId: string) => {
  try {
    const serviceResult = await getParkingServiceById(parkingServiceId);
    if (!serviceResult.success || !serviceResult.data) {
      return [];
    }
    
    const service = serviceResult.data;
    const formattedName = service.name
      .toLowerCase()
      .replace(/\s+/g, '-')
      .replace(/[^a-z0-9-]/g, '');
    
    // Base path to reports
    const basePath = path.join(
      process.cwd(), 
      'public', 
      'parking-service',
      formattedName,
      'report'
    );
    
    // Check if base path exists
    try {
      await fs.access(basePath);
    } catch {
      return []; // Directory doesn't exist
    }
    
    const allFiles: string[] = [];
    const yearDirs = await fs.readdir(basePath);
    
    for 
```

## `get-scheduled-reports.ts`

```ts
"use server";

import { db } from "@/lib/db";
import { getCurrentUser } from "@/lib/auth";
import { logActivity } from "@/actions/security/log-event";

/**
 * Fetches all scheduled reports
 * Can be filtered by isActive status
 */
export async function getScheduledReports(filters?: { isActive?: boolean }) {
  try {
    // Get current user for authentication
    const user = await getCurrentUser();
    if (!user) {
      throw new Error("Unauthorized access");
    }

    // Build query with optional filters
    const whereClause: any = {};
    if (filters?.isActive !== undefined) {
      whereClause.isActive = filters.isActive;
    }

    // Fetch scheduled reports
    const scheduledReports = await db.scheduledReport.findMany({
      where: whereClause,
      orderBy: [
        { isActive: 'desc' },
        { nextRun: 'asc' }
      ],
    });

    // Log the activity
    await logActivity({
      action: "VIEW_SCHEDULED_REPORTS",
      entityType: "report",
      entityId: null,
      
```

## `schedule-report.ts`

```ts
///actions/reports/schedule-report.ts

"use server";

import { z } from "zod";
import { db } from "@/lib/db";
import { ReportFrequency } from "@prisma/client";
import { getNextRunDate } from "@/lib/reports/scheduled-jobs";
import { getCurrentUser } from "@/lib/auth";
import { logActivity } from "@/actions/security/log-event";

// Schema for validating report scheduling input
const ScheduleReportSchema = z.object({
  name: z.string().min(3, "Report name must be at least 3 characters"),
  description: z.string().optional(),
  reportType: z.string().min(1, "Report type is required"),
  frequency: z.nativeEnum(ReportFrequency),
  parameters: z.record(z.any()).optional(),
});

export type ScheduleReportInput = z.infer<typeof ScheduleReportSchema>;

export async function scheduleReport(data: ScheduleReportInput) {
  try {
    // Validate input
    const validatedData = ScheduleReportSchema.parse(data);
    
    // Get current user
    const user = await getCurrentUser();
    if (!user) {
   
```

## `check-permission.ts`

```ts
///actions/security/check-permission.ts
"use server";

import { auth } from "@/lib/auth";
import { UserRole, LogSeverity } from "@prisma/client";
import { db } from "@/lib/db";
import { logActivity } from "@/lib/security/audit-logger";

export interface Permission {
  name: string;
  resource: string;
  action: string;
  roles: UserRole[];
}

// Define system permissions
const PERMISSIONS: Permission[] = [
  // Analytics permissions
  { name: "view_analytics", resource: "analytics", action: "view", roles: [UserRole.ADMIN, UserRole.MANAGER] },
  { name: "view_financial_analytics", resource: "analytics", action: "view_financial", roles: [UserRole.ADMIN, UserRole.MANAGER] },
  { name: "view_sales_analytics", resource: "analytics", action: "view_sales", roles: [UserRole.ADMIN, UserRole.MANAGER, UserRole.AGENT] },
  { name: "view_complaint_analytics", resource: "analytics", action: "view_complaints", roles: [UserRole.ADMIN, UserRole.MANAGER, UserRole.AGENT] },
  { name: "view_provider_analy
```

## `get-distinct-entity-types.ts`

```ts
// Path: actions/security/get-distinct-entity-types.ts

"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

export async function getDistinctEntityTypes() {
    try {
        const session = await auth();

        if (!session || !session.user || (session.user.role !== 'ADMIN' && session.user.role !== 'MANAGER')) {
            return [];
        }

        const distinctTypes = await db.activityLog.findMany({
            select: {
                entityType: true,
            },
            distinct: ['entityType'],
            where: {
                // ISPRAVLJENO: Koristimo null umesto String
                entityType: { not: null }
            }
        });

        const entityTypes = distinctTypes
            .map(log => log.entityType)
            .filter(type => type !== null) as string[];

        return entityTypes;

    } catch (error) {
        console.error("[GET_DISTINCT_ENTITY_TYPES_ACTION_ERROR]", er
```

## `get-performance-metrics.ts`

```ts
// Path: actions/security/get-performance-metrics.ts

"use server";

import { db } from "@/lib/db";
import { ActivityLog } from "@prisma/client";
import { subHours } from "date-fns";

interface PerformanceDataPoint {
  timestamp: string;
  responseTime: number;
  requestCount: number;
  errorRate: number;
  cpuUsage: number | null;
  memoryUsage: number | null;
}

interface GetPerformanceMetricsResult {
  data: PerformanceDataPoint[];
  avgResponseTime: number | null;
  requestCount24h: number | null;
  errorRate24h: number | null;
  activeUsers: number | null;
}

export const getPerformanceMetrics = async (): Promise<GetPerformanceMetricsResult> => {
  try {
    const defaultTimeRangeStart = subHours(new Date(), 24);

    const relevantLogs = await db.activityLog.findMany({
      where: {
        createdAt: {
          gte: defaultTimeRangeStart,
        },
        details: { not: null },
      },
      orderBy: {
        createdAt: 'asc',
      },
      select: {
         id: true,
 
```

## `log-event.ts`

```ts
// Path: actions/security/log-event.ts (ili gde god se nalazi getActivityLogs)

"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { LogSeverity, ActivityLog as PrismaActivityLog } from "@prisma/client";
import { z } from "zod"; // Moguće da treba Zod i ovde za validaciju ulaza
import { subHours, subDays, subWeeks, subMonths } from "date-fns"; // Ako koristite vremenske opsege, iako ovde koristimo datume

interface GetActivityLogsFilters {
  severity?: LogSeverity;
  entityType?: string;
  startDate?: Date;
  endDate?: Date;
  userId?: string;
  action?: string;
  page?: number;
  limit?: number; // Page size
}

interface GetActivityLogsResult {
  logs: (PrismaActivityLog & { user: { id: string; name: string | null; email: string; role: string } | null })[];
  total: number;
}

export async function getActivityLogs(filters: GetActivityLogsFilters): Promise<GetActivityLogsResult> {
    try {
        const session = await auth();

        if (!session || 
```

## `metrics.ts`

```ts
// Path: actions/security/metrics.ts
"use server";

import { db } from "@/lib/db";
import { LogSeverity } from "@prisma/client";
import { subDays } from "date-fns";

// (Keep getCriticalAlertMetrics from previous step)
export async function getCriticalAlertMetrics() {
  const now = new Date();
  const twentyFourHoursAgo = subDays(now, 1);
  const fortyEightHoursAgo = subDays(now, 2);

  try {
    const criticalLast24h = await db.activityLog.count({
      where: {
        severity: LogSeverity.CRITICAL,
        createdAt: { gte: twentyFourHoursAgo },
      },
    });

    const criticalPrevious24h = await db.activityLog.count({
      where: {
        severity: LogSeverity.CRITICAL,
        createdAt: { gte: fortyEightHoursAgo, lt: twentyFourHoursAgo },
      },
    });

    let changePercentage = 0;
    let changePositive = true;

    if (criticalPrevious24h > 0) {
      const change = criticalLast24h - criticalPrevious24h;
      changePercentage = Math.round((change / criticalPrevious2
```

## `create.ts`

```ts
// actions/services/create.ts
'use server'

import { z } from 'zod';
import { serviceSchema } from '@/schemas/service';
import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { ServiceType } from '@prisma/client';
import { logActivity } from '@/lib/security/audit-logger';

export async function createService(formData: z.infer<typeof serviceSchema>) {
  const session = await auth();
  
  if (!session?.user) {
    throw new Error("Unauthorized");
  }
  
  try {
    // Validate form data
    const validatedData = serviceSchema.parse(formData);
    
    // Create the service
    const service = await db.service.create({
      data: {
        name: validatedData.name,
        type: validatedData.type as ServiceType,
        description: validatedData.description,
        isActive: validatedData.isActive
      }
    });
    
    // Log activity - Fixed function call
    await logActivity("CREATE", {
      entityType: 'service',
     
```

## `delete.ts`

```ts
///actions/services/delete.ts

'use server'

import { prisma } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { logActivity } from '@/lib/security/audit-logger';

export async function deleteService(id: string) {
  const session = await auth();
  
  if (!session?.user || !['ADMIN', 'MANAGER'].includes(session.user.role)) {
    throw new Error("Unauthorized");
  }
  
  try {
    // Find the service to get the type before deletion
    const service = await prisma.service.findUnique({
      where: { id }
    });
    
    if (!service) {
      throw new Error("Service not found");
    }
    
    // Delete the service
    await prisma.service.delete({
      where: { id }
    });
    
    // Log activity
    await logActivity({
      action: 'DELETE',
      entityType: 'service',
      entityId: id,
      details: `Deleted service: ${service.name}`,
      userId: session.user.id
    });
    
    // Revalidate cache
    revalidatePath('/ser
```

## `export.ts`

```ts
// /actions/services/export.ts
'use server';

import { db } from '@/lib/db';
// Uvozimo sync stringifier
import { stringify } from 'csv-stringify/sync';
// Uvozimo ažurirane tipove
import { ServiceWithDetails, ServiceFilterOptions } from '@/lib/types/service-types';
// Uvozimo auth funkcije za proveru autentifikacije/autorizacije
import { auth } from '@/auth';
import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client"; // Koristimo UserRole enum iz Prisma klijenta


/**
 * Server akcija za export servisa/usluga u CSV formatu.
 * Prihvata filtere kako bi se odredilo koji servisi se exportuju.
 * Usklađena sa Service modelom u schema.prisma.
 * @param filters - Opcije filtera za export.
 * @returns Sadržaj CSV fajla kao string ili grešku.
 */
export async function exportServices(filters: ServiceFilterOptions): Promise<{ csv: string | null; error: string | null }> {
    // Provera autorizacije - samo ADMIN ili MANAGER mogu exportovati
     const role = await curre
```

## `get.ts`

```ts
// /actions/services/get.ts
'use server';

import { db } from '@/lib/db';
import { ServiceType } from '@prisma/client';
import { Service } from '@/lib/types/service-types';


export async function getServiceById(id: string): Promise<{ data?: Service; error?: string }> {
    if (!id) {
        return { error: 'Service ID is required.' };
    }

    try {
        const service = await db.service.findUnique({
            where: { id },

        });

        if (!service) {
            return { error: 'Service not found.' };
        }

        return { data: service as Service };

    } catch (error) {
        console.error(`Error fetching service ${id}:`, error);
        return { error: 'Failed to fetch service.' };
    }
}


export async function getServices(): Promise<{ data?: Service[]; error?: string }> {
     try {
         const services = await db.service.findMany({
             orderBy: { name: 'asc' },

         });

         return { data: services as Service[] };

     } catch 
```

## `getAllServices.ts`

```ts
// actions/services/getAllServices.ts

// Uvoz tvoje Prisma klijent instance
import { db } from '@/lib/db';
// Uvoz ServiceType enum-a ako ga koristiš u Prisma šemi za tipove servisa
import { ServiceType } from '@prisma/client';

// Opciona interfejs definicija za filtere koje funkcija prihvata
interface GetAllServicesFilters {
  type?: ServiceType; // Omogućava filtriranje po tipu servisa (npr. "BULK")
  // Dodaj ovde druge filtere koji bi mogli biti potrebni (npr. name, isActive, itd.)
}

/**
 * Server-side funkcija za dohvatanje liste servisa iz baze podataka.
 * Može opcionalno filtrirati rezultate.
 * Poziva se u Server Komponentama ili API rutama.
 */
export async function getAllServices(filters?: GetAllServicesFilters) {
  try {
    // Kreiranje objekta uslova za `where` klauzulu u Prisma upitu
    const where: any = {};

    // Ako je filter za tip servisa prosleđen, dodaj ga u where uslove
    if (filters?.type) {
      where.type = filters.type;
    }

    // Dodaj logiku za 
```

## `get-by-category.ts`

```ts
// /actions/services/get-by-category.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";
import { ServiceType } from "@prisma/client";

export async function getServicesByCategory(type?: ServiceType) {
  const session = await auth();
  if (!session?.user) {
    throw new Error("Unauthorized access");
  }

  const where = type ? { type, isActive: true } : { isActive: true };

  return db.service.findMany({
    where,
    orderBy: {
      name: "asc",
    },
    select: {
      id: true,
      name: true,
      type: true,
      description: true,
    },
  });
}
```

## `get-parking-services.ts`

```ts
// /actions/services/get-parking-services.ts
"use server";

import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function getParkingServices() {
  const session = await auth();
  if (!session?.user) {
    throw new Error("Unauthorized access");
  }

  return db.parkingService.findMany({
    where: { isActive: true },
    orderBy: { name: "asc" },
    select: {
      id: true,
      name: true,
      contactName: true,
      email: true,
      phone: true,
      address: true,
    },
  });
}
```

## `import.ts`

```ts
// /actions/services/import.ts
'use server';

import { db } from '@/lib/db';
// Uvozimo sync parser i stringifier
import { parse } from 'csv-parse/sync';
import { stringify } from 'csv-stringify/sync';
// Uvozimo ažurirane tipove
import { ServiceCsvRow, CsvImportResult } from '@/lib/types/csv-types';
// Uvozimo ažuriranu Zod šemu za validaciju i tip
import { serviceSchema, ServiceFormData } from '@/schemas/service';
// Uvozimo ažurirani CSV procesor
import { processServiceCsv } from '@/lib/services/csv-processor'; // Koristimo funkciju iz procesora
// Uvozimo auth funkcije za proveru autentifikacije/autorizacije
import { auth } from '@/auth';
import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client"; // Koristimo UserRole enum iz Prisma klijenta


/**
 * Server akcija za import servisa/usluga iz CSV fajla.
 * Prihvata CSV sadržaj kao string, parsira, validira i unosi u bazu.
 * Usklađena sa Service modelom u schema.prisma i ažuriranom serviceSchema.
 * @param 
```

## `importVasData.ts`

```ts
// Path: actions/services/importVasData.ts
"use server";

import { db } from "@/lib/db";
import csv from 'csv-parser'; // `npm install csv-parser` ili `yarn add csv-parser`
import { Readable } from 'stream'; // Node.js stream za parsiranje
import { ServiceType } from '@prisma/client'; // Uvezite ServiceType ako ga koristite u Prisma shemi


// Definisanje strukture parsiranog reda iz VAS CSV-a
// NAZIVI KLJUČEVA MORAJU TAČNO ODGOVARATI HEADER REDU U VAŠEM CSV FAJLU.
interface VasCsvRow {
    'Proizvod': string;
    'Mesec_pruzanja_usluge': string;
    'Jedinicna_cena': string;
    'Broj_transakcija': string;
    'Fakturisan_iznos': string;
    'Fakturisan_korigovan_iznos': string;
    'Naplacen_iznos': string;
    'Kumulativ_naplacenih_iznosa': string;
    'Nenaplacen_iznos': string;
    'Nenaplacen_korigovan_iznos': string;
    'Storniran_iznos_u_tekucem_mesecu_iz_perioda_pracenja': string;
    'Otkazan_iznos': string;
    'Kumulativ_otkazanih_iznosa': string;
    'Iznos_za_prenos_sre
```

## `update.ts`

```ts
///actions/services/update.ts

'use server'
import { z } from 'zod';
import { contractSchema } from '@/schemas/contract';
import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';
import { auth } from '@/auth';
import { logActivity } from '@/lib/security/audit-logger';

export async function updateContract(id: string, formData: any) {
  console.log('[UPDATE_CONTRACT] Starting update for:', id);
  console.log('[UPDATE_CONTRACT] Data keys:', Object.keys(formData));
  
  try {
    // Dobij session bez pozivanja headers direktno
    const session = await auth();
    
    if (!session?.user) {
      console.log('[UPDATE_CONTRACT] No session or user');
      return { success: false, error: 'Unauthorized' };
    }

    console.log('[UPDATE_CONTRACT] Session user:', {
      id: session.user.id,
      role: session.user.role
    });

    // Validacija podataka
    const validatedData = contractSchema.parse(formData);
    
    // Pronađi postojeći ugovor
    const existingContr
```

## `get-assignable-users.ts`

```ts
// Path: actions/users/get-assignable-users.ts
"use server";

import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

/**
 * Fetches a list of users who can be assigned to a complaint (Admins and Agents).
 * Only accessible by Admin and Manager roles.
 * @returns A promise resolving to an array of assignable users (id, name) or an error object.
 */
export async function getAssignableUsers() {
  try {
    const session = await auth();

    // Check if the user is authenticated and has the necessary role (Admin or Manager)
    if (!session || !session.user || (session.user.role !== UserRole.ADMIN && session.user.role !== UserRole.MANAGER)) {
      return { error: "Unauthorized" };
    }

    // Fetch users with ADMIN or AGENT roles
    const assignableUsers = await db.user.findMany({
      where: {
        role: {
          in: [UserRole.ADMIN, UserRole.AGENT],
        },
      },
      select: {
        id: true,
        name: true,
    
```


# Sekcija: lib

## `auth.ts`

```ts
//lib/auth.ts
import { auth } from "@/auth";

export const currentUser = async () => {
  const session = await auth();

  return session?.user;
};
export async function getCurrentUser() {
    // Replace with your logic to get the current authenticated user
    const session = await auth(); // Example for NextAuth v5
    return session?.user;
}

export const currentRole = async () => {
  const session = await auth();

  return session?.user.role;
};

```

## `constants.ts`

```ts
//lib/constants.ts

export const months = [
  'January', 'February', 'March', 'April', 'May', 'June',
  'July', 'August', 'September', 'October', 'November', 'December'
];
```

## `db.ts`

```ts
// lib/db.ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };

export const db = globalForPrisma.prisma || new PrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db;

```

## `exceptions.ts`

```ts
// lib/exceptions.ts

/**
 * Custom error class for server-side errors, typically used in API routes or server actions.
 * Includes an optional status code.
 */
export class ServerError extends Error {
  public readonly statusCode: number;

  constructor(message: string, statusCode: number = 500) {
    super(message); // Prosleđuje poruku standardnom Error konstruktoru
    this.name = 'ServerError'; // Postavlja ime greške
    this.statusCode = statusCode; // Postavlja status kod

    // Opcionalno: Dodaj ovo ako želiš da očuvaš ispravan stack trace
    // Error.captureStackTrace(this, ServerError);
  }
}

// Opcionalno: Odradi export i za druge klase grešaka ako ih budeš dodavao u budućnosti
// export class ValidationError extends ServerError {
//   constructor(message: string = "Validation failed", statusCode: number = 400) {
//     super(message, statusCode);
//     this.name = 'ValidationError';
//   }
// }
```

## `file-storage.ts`

```ts
///lib/file-storage.ts
interface UploadResult {
  success: boolean;
  fileUrl?: string;
  fileName?: string;
  fileType?: string;
  error?: string;
}

export const uploadFile = async (
  file: File,
  destinationPath: string
): Promise<UploadResult> => {
  try {
    // In a real implementation, this would upload to cloud storage
    console.log(`Simulating upload of file "${file.name}" to "${destinationPath}"`);
    
    // Create a unique filename
    const uniqueFileName = `${Date.now()}-${file.name}`;
    
    // Simulate successful upload
    return {
      success: true,
      fileUrl: `/uploads/${destinationPath}${uniqueFileName}`,
      fileName: uniqueFileName,
      fileType: file.type,
    };
  } catch (error) {
    console.error("File upload error:", error);
    return {
      success: false,
      error: "Failed to upload file",
    };
  }
};
```

## `formatters.ts`

```ts
// lib/formatters.ts

/**
 * Formats a number as currency (USD by default).
 * @param amount - The number amount to format.
 * @param locale - The locale string (e.g., 'en-US', 'de-DE'). Defaults to 'en-US'.
 * @param currency - The currency code (e.g., 'USD', 'EUR'). Defaults to 'USD'.
 * @returns The formatted currency string.
 */
export function formatCurrency(
    amount: number,
    locale: string = 'en-US',
    currency: string = 'RSD'
  ): string {
    // Use the built-in Intl.NumberFormat for reliable localization
    const formatter = new Intl.NumberFormat(locale, {
      style: 'currency',
      currency: currency,
      // Optional: control decimal places
      // minimumFractionDigits: 2,
      // maximumFractionDigits: 2,
    });
  
    return formatter.format(amount);
  }
  
  /**
   * Formats a number as a percentage.
   * @param value - The number amount to format (e.g., 0.75 for 75%).
   * @param locale - The locale string. Defaults to 'en-US'.
   * @param options - Op
```

## `mail.ts`

```ts
import { Resend } from "resend";

const resend = new Resend(process.env.RESEND_API_KEY);

const domain = process.env.NEXT_PUBLIC_APP_URL;

export const sendPasswordResetEmail = async (email: string, token: string) => {
  const resetLink = `${domain}/auth/new-password?token=${token}`;

  await resend.emails.send({
    from: "onboarding@resend.dev",
    to: email,
    subject: "Reset password",
    html: `<p>Click <a href="${resetLink}">here</a> to reset your password.</p>`,
  });
};

export const sendVerificationEmail = async (email: string, token: string) => {
  const confirmLink = `${domain}/auth/new-verification?token=${token}`;

  await resend.emails.send({
    from: "onboarding@resend.dev",
    to: email,
    subject: "Confirm your email",
    html: `<p>Click <a href="${confirmLink}">here</a> to confirm email.</p>`,
  });
};

export const sendTwoFactorTokenEmail = async (email: string, token: string) => {
  await resend.emails.send({
    from: "onboarding@resend.dev",
    to: email
```

## `providers.ts`

```ts
////lib/providers.ts


import { db } from "@/lib/db";

/**
 * Fetches all unique providers from the database
 * @returns Array of unique providers with their id and name
 */
export async function getUniqueProviders() {
  try {
    const providers = await db.provider.findMany({
      select: {
        id: true,
        name: true
      },
      orderBy: {
        name: 'asc'
      }
    });
    
    return providers.map(provider => ({
      id: provider.id,
      name: provider.name || provider.id
    }));
  } catch (error) {
    console.error("Error fetching unique providers:", error);
    return [];
  }
}
```

## `session.ts`

```ts
// lib/session.ts

import { auth } from "@/auth"; // OVDE SE PRETPODSTAVLJA DA JE TVOJA AUTH.JS KONFIGURACIJA IZVEZENA IZ FAJLA NA PUTANJI "@/auth"

/**
 * Server-side helper to get the current authenticated user's session.
 * Uses the Auth.js `auth()` helper to retrieve the session data.
 * Returns the user object or null/undefined if no session exists.
 */
export async function getCurrentUser() {
  // Poziv Auth.js `auth()` helpera za dobijanje sesije na server-strani
  // (Ovo je uobičajen način u Next.js App Routeru sa Auth.js v5+)
  const session = await auth();

  // Vraća objekat korisnika iz sesije
  // Ako sesija ne postoji, session će biti null, a session?.user će takođe biti null/undefined
  return session?.user;
}

// Možeš dodati i druge korisne funkcije za sesiju ovde, npr:
// export async function isLoggedIn() {
//   const session = await auth();
//   return !!session?.user; // Vraća true ako je korisnik ulogovan
// }
```

## `tokens.ts`

```ts
import crypto from "crypto";
import { v4 as uuidv4 } from "uuid";
import { db } from "./db";
import { getVerificationTokenByEmail } from "@/data/verification-token";
import { getPasswordResetTokenByEmail } from "@/data/password-reset-token";
import { getTwoFactorTokenByEmail } from "@/data/two-factor-token";

export const generatePasswordResetToken = async (email: string) => {
  const token = uuidv4();

  const expires = new Date(new Date().getTime() + 3600 * 1000);

  const existingToken = await getPasswordResetTokenByEmail(email);

  if (existingToken) {
    await db.passwordResetToken.delete({
      where: {
        id: existingToken.id,
      },
    });
  }

  const passwordResetToken = await db.passwordResetToken.create({
    data: {
      email,
      token,
      expires,
    },
  });

  return passwordResetToken;
};

export const generateVerificationToken = async (email: string) => {
  const token = uuidv4();

  const min = 10;
  const expires = new Date(new Date().getTime() + 
```

## `utils.ts`

```ts
//lib/utils.ts

import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
export function formatDate(dateString) {
  if (!dateString) return "Invalid date";

  const date = new Date(dateString);
  
  // Proveri da li je datum valjan
  if (isNaN(date.getTime())) return "Invalid date";

  // Koristi UTC metode za konzistentno formatiranje
  const day = String(date.getUTCDate()).padStart(2, "0");
  const month = String(date.getUTCMonth() + 1).padStart(2, "0");
  const year = date.getUTCFullYear();

  const hours = String(date.getUTCHours()).padStart(2, "0");
  const minutes = String(date.getUTCMinutes()).padStart(2, "0");
  const seconds = String(date.getUTCSeconds()).padStart(2, "0");

  return `${day}/${month}/${year} ${hours}:${minutes}:${seconds}`;
}
export function formatCurrency(value: number): string {
  return new Intl.NumberFormat('sr-RS', {
    style: 'currency',
    curre
```

## `complaints.ts`

```ts
// lib/actions/complaints.ts
"use server"; // Dodat "use server" direktiva ako nedostaje

// ISPRAVLJENO: Uvezen db iz lib/db
import { db } from "@/lib/db";
import { auth } from "@/auth"; // Proverite putanju za auth
import { z } from "zod"; // Uvezeno z ako se koristi negde drugde
import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation"; // Uvezeno redirect ako se koristi negde drugde
// Uvezen ComplaintStatus direktno iz Prisma klijenta ako je to izvor istine
import { ComplaintStatus } from "@prisma/client";

// Uvezeni potrebni tipovi (pretpostavljamo da su definisani ovde)
import { Complaint, ComplaintFilters, CreateComplaintData, UpdateComplaintData } from "@/lib/types/interfaces";

// Uvezeni helperi iz utils
import { createNotification } from "@/utils/complaint-notification"; // Proverite putanju
// ISPRAVLJENO: Uvezeno calculateStatistics i detectAnomalies iz utils/complaint-statistics
import { calculateStatistics, detectAnomalies } from "@/utils/
```

## `contract-actions.ts`

```ts
// /lib/actions/contract-actions.ts
"use server";

import { db } from "@/lib/db";
import { ContractStatus, ContractRenewalSubStatus } from "@/lib/types/contract-types";
import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";

// Action za promenu statusa ugovora
export async function updateContractStatus(
  contractId: string,
  newStatus: ContractStatus,
  comments?: string
) {
  try {
    // Validacija da ugovor postoji
    const existingContract = await db.contract.findUnique({
      where: { id: contractId },
      include: {
        renewals: {
          where: { isActive: true },
          orderBy: { createdAt: 'desc' },
          take: 1
        }
      }
    });

    if (!existingContract) {
      throw new Error("Contract not found");
    }

    // Ako menjamo status sa RENEWAL_IN_PROGRESS na nešto drugo, 
    // treba da deaktiviramo trenutni renewal
    if (existingContract.status === ContractStatus.RENEWAL_IN_PROGRESS && 
        newStatus !
```

## `debug-session.ts`

```ts
// Debug session server action - Path: /lib/actions/debug-session.ts
import { auth } from "@/auth";

export async function debugServerSession() {
  "use server";
  
  const session = await auth();
  
  console.log("[SERVER_SESSION] ===== DEBUG =====");
  console.log("[SERVER_SESSION] Session exists:", !!session);
  console.log("[SERVER_SESSION] User:", session?.user);
  console.log("[SERVER_SESSION] Expires:", session?.expires);
  console.log("[SERVER_SESSION] =================");
  
  return {
    hasSession: !!session,
    user: session?.user || null,
    expires: session?.expires || null
  };
}
```

## `report.actions.ts`

```ts
// lib/actions/report.actions.ts
export async function fetchInitialReports(page: number = 1, limit: number = 10) {
  try {
    const response = await fetch(
      `${process.env.NEXT_PUBLIC_API_URL}/api/reports?page=${page}&limit=${limit}`
    );

    if (!response.ok) {
      throw new Error('Greška pri učitavanju izveštaja');
    }

    const data = await response.json();
    return data.data;
  } catch (error) {
    console.error('Error fetching initial reports:', error);
    return [];
  }
}
```

## `auth-utils.ts`

```ts
// lib/auth/auth-utils.ts
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { UserRole } from "@prisma/client";
import { redirect } from "next/navigation";

export async function getCurrentUser() {
  try {
    const session = await auth();
    
    if (!session?.user?.id) {
      return null;
    }

    const user = await db.user.findUnique({
      where: { id: session.user.id },
      select: {
        id: true,
        email: true,
        role: true,
        isActive: true,
      },
    });

    return user;
  } catch (error) {
    console.error("[getCurrentUser] Error:", error);
    return null;
  }
}

export async function getUserRole(): Promise<UserRole | null> {
  try {
    const session = await auth();
    
    console.log("[GET_USER_ROLE] Session debug:", {
      hasSession: !!session,
      hasUser: !!session?.user,
      userEmail: session?.user?.email,
      roleInSession: (session?.user as any)?.role
    });

    // First try to get role from session
   
```

## `listAuthenticatorsByUserId.ts`

```ts
///lib/auth/adapters/listAuthenticatorsByUserId.ts


import { Awaitable } from "next-auth";
import { AdapterAuthenticator } from "next-auth/adapters";
import { db } from "@/lib/db";

/**
 * Lists all authenticators for a given user
 * @param userId The user ID to retrieve authenticators for
 * @returns An array of authenticators associated with the user
 */
export async function listAuthenticatorsByUserId(userId: string): Awaitable<AdapterAuthenticator[]> {
  try {
    const authenticators = await db.authenticator.findMany({
      where: {
        userId: userId
      }
    });
    
    return authenticators.map(auth => ({
      id: auth.id,
      userId: auth.userId,
      credentialID: auth.credentialID,
      providerAccountId: auth.providerAccountId,
      credentialPublicKey: auth.credentialPublicKey,
      counter: auth.counter,
      credentialDeviceType: auth.credentialDeviceType,
      credentialBackedUp: auth.credentialBackedUp,
      transports: auth.transports,
    }));
  }
```

## `csv-processor.ts`

```ts
///lib/bulk-services/csv-processor.ts
// lib/bulk-services/csv-processor.ts

import Papa from 'papaparse';
import { BulkServiceValidationError, BulkServiceData } from '@/lib/types/bulk-service-types';

/**
 * Parse the CSV content for bulk service imports
 */
export async function parseBulkServiceCSV(csvContent: string) {
  const data: BulkServiceData[] = [];
  const errors: BulkServiceValidationError[] = [];

  // Use Papa.parse to parse the CSV content
  const parseResult = Papa.parse(csvContent, {
    header: true,
    skipEmptyLines: true,
    transformHeader: header => header.trim(), // Trim whitespace from headers
    // Optional: Add error handling for Papa.parse
    error: (err) => {
      errors.push({
        rowIndex: -1,
        errors: [`CSV parsing error: ${err.message}`],
        originalRow: {}
      });
    }
  });

  if (parseResult.errors && parseResult.errors.length > 0) {
    parseResult.errors.forEach(err => {
      errors.push({
        rowIndex: err.row || -1,
 
```

## `statistics.ts`

```ts
///lib/bulk-services/statistics.ts

/**
 * Statistical calculations for bulk services
 * Provides utilities for analyzing bulk service data
 */

import type { BulkService } from "@prisma/client";
import _ from "lodash";

export type BulkServiceStats = {
  totalRequests: number;
  totalMessageParts: number;
  byProvider: Record<string, {
    providerName: string;
    requests: number;
    messageParts: number;
    serviceCount: number;
  }>;
  byService: Record<string, {
    serviceName: string;
    requests: number;
    messageParts: number;
    providerCount: number;
  }>;
  topProviders: {
    providerName: string;
    requests: number;
    messageParts: number;
  }[];
  topServices: {
    serviceName: string;
    requests: number;
    messageParts: number;
  }[];
};

/**
 * Calculate comprehensive statistics from bulk service data
 * @param bulkServices Array of bulk service records
 * @param limit Number of items to include in top lists (default: 5)
 * @returns Object containing ca
```

## `validators.ts`

```ts
///lib/bulk-services/validators.ts

/**
 * Bulk services validation utilities
 * Contains functions for validating bulk service data
 */

import { z } from "zod";
import { BulkServiceSchema } from "@/schemas/bulk-service";
import type { BulkService } from "@prisma/client";

/**
 * Validates bulk service data against the schema
 * @param data The bulk service data to validate
 * @returns Validation result with success flag and either validated data or error message
 */
export const validateBulkService = (data: unknown) => {
  try {
    const validatedData = BulkServiceSchema.parse(data);
    return { success: true, data: validatedData };
  } catch (error) {
    if (error instanceof z.ZodError) {
      const formattedErrors = error.errors.map((err) => ({
        path: err.path.join("."),
        message: err.message,
      }));
      return { success: false, error: formattedErrors };
    }
    return { success: false, error: "Invalid data provided" };
  }
};

/**
 * Validates required fi
```

## `expiration-checker.ts`

```ts
// /lib/contracts/expiration-checker.ts

import { isPast, differenceInDays } from 'date-fns'; // Pretpostavljena utility funkcija za rad sa datumima
import { ContractStatus } from '@prisma/client'; // Prisma enum za status

/**
 * Proverava da li je ugovor istekao na osnovu datuma završetka i statusa.
 * Status "EXPIRED" u bazi je primaran indikator, ali provera datuma
 * je korisna za klijentsku logiku ili za sinhronizaciju statusa.
 * @param endDate - Datum završetka ugovora.
 * @param status - Trenutni status ugovora.
 * @returns True ako je ugovor istekao, inače False.
 */
export function isContractExpired(endDate: Date, status: ContractStatus): boolean {
  // Ako status eksplicitno kaže da je istekao, to je najjači indikator
  if (status === ContractStatus.EXPIRED) {
    return true;
  }
  // Ako status nije EXPIRED, ali je datum završetka u prošlosti, smatramo ga isteklim
  // Proveravamo samo ako status nije PENDING ili RENEWAL_IN_PROGRESS, jer ti statusi mogu
  // imati datum u
```

## `notification-sender.ts`

```ts
// /lib/contracts/notification-sender.ts

import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
import { NotificationType, Contract, ContractReminder, User } from '@prisma/client'; // Prisma modeli i enumi
import { format } from 'date-fns'; // Utility za formatiranje datuma

// Pretpostavljeni interfejsi za kontekst
interface ContractNotificationContext extends Partial<Contract> {
     id: string; // ID ugovora je neophodan
     name?: string;
     contractNumber?: string;
     endDate?: Date;
}

interface ReminderNotificationContext extends Partial<ContractReminder> {
    id: string; // ID podsetnika je neophodan
    reminderDate?: Date;
    reminderType?: string;
    contract?: ContractNotificationContext | null;
}


/**
 * Kreira zapis o in-app notifikaciji u bazi podataka.
 * @param params - Parametri za kreiranje notifikacije.
 * @returns Kreirani Notification zapis ili null u slučaju greške.
 */
const createNotification = async (params: {
    userId: 
```

## `pdf-generator.ts`

```ts
// /lib/contracts/pdf-generator.ts

import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
import { Contract, Provider, HumanitarianOrg, ParkingService, ServiceContract, ContractAttachment, ContractReminder, User, Service } from '@prisma/client'; // Prisma modeli
// U realnosti, ovde biste importovali biblioteku za generisanje PDF-ova, npr:
// import PDFDocument from 'pdfkit'; // Primer za serversko generisanje
// ili biblioteku koja renderuje React komponente u PDF (ako koristite taj pristup):
// import { renderToBuffer } from '@react-pdf/renderer'; // Primer

// Tip za ugovor sa svim uključenim relacijama potrebnim za PDF
type FullContractDetails = Contract & {
    provider: Provider | null;
    humanitarianOrg: HumanitarianOrg | null;
    parkingService: ParkingService | null;
    services: (ServiceContract & { service: Service })[];
    attachments: ContractAttachment[];
    reminders: ContractReminder[];
    createdBy: { name: string | null; email: stri
```

## `reminder-scheduler.ts`

```ts
// /lib/contracts/reminder-scheduler.ts

import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
import { ContractReminder, Contract, User } from '@prisma/client'; // Prisma modeli
import { sendReminderNotification } from '@/lib/contracts/notification-sender'; // Utility za slanje notifikacija (kreiraćemo kasnije)

// Tip koji proširuje Reminder sa potrebnim relacijama
interface DueReminder extends ContractReminder {
    contract: Contract | null; // Uključujemo podatke o ugovoru
    acknowledgedBy: User | null; // Podaci o tome ko je pregledao (trebalo bi biti null ovde)
    contract: { // Detalji ugovora potrebni za notifikaciju/kontekst
        id: string;
        name: string;
        contractNumber: string;
        createdBy: { id: string; email: string | null; name: string | null; } | null; // Ko je kreirao ugovor (potencijalni primalac notifikacije)
    } | null;
}


/**
 * Proverava sve podsetnike koji su dospeli (reminderDate je danas ili prošao)
 * 
```

## `revenue-calculator.ts`

```ts
// lib/contracts/revenue-calculator.ts
import { db } from '@/lib/db';
import { Contract, ServiceContract, ServiceType } from '@prisma/client';
import { startOfMonth, endOfMonth, max, min } from 'date-fns';

type ContractWithServicesAndProvider = Contract & {
    services: (ServiceContract & { service: { id: string, type: ServiceType, name: string } })[];
    provider: { id: string } | null;
    parkingService: { id: string } | null;
};

interface ServiceBreakdownItem {
    id: string;
    name: string;
    revenueAmount: number;
    percentage: number;
    details?: {
        messages: number;
        messageRevenue: number;
        records: number;
        recordRevenue: number;
    };
}

interface CalculatedRevenueData {
    totalGrossRevenue: number;
    platformRevenue: number;
    partnerRevenue: number;
    serviceBreakdown: ServiceBreakdownItem[];
}

export const calculateContractRevenue = async (
    contractId: string,
    calculationStartDate?: Date,
    calculationEndDate?: 
```

## `validators.ts`

```ts
///lib/contracts/validators.ts

import { z } from "zod";
import { ContractStatus, ContractType } from "@prisma/client";
import { contractSchema } from "@/schemas/contract";
import { ContractFormData } from "@/lib/types/contract-types";

/**
 * Validates if a contract's end date is after its start date
 */
export function validateContractDates(startDate: Date, endDate: Date): boolean {
  return endDate > startDate;
}

/**
 * Checks if a contract is expiring within the specified number of days
 */
export function isContractExpiringSoon(endDate: Date, days: number = 30): boolean {
  const today = new Date();
  const daysInMs = days * 24 * 60 * 60 * 1000;
  return endDate.getTime() - today.getTime() <= daysInMs && endDate > today;
}

/**
 * Validates contract data using the Zod schema
 */
export function validateContractData(data: any): { 
  success: boolean; 
  data?: ContractFormData; 
  errors?: z.ZodFormattedError<ContractFormData>; 
} {
  try {
    const validatedData = contractSchema
```

## `validators.ts`

```ts
// /lib/humanitarian-orgs/validators.ts

// Uvozimo model Humanitarne Organizacije (osnovni tip)
import { HumanitarianOrg } from '@prisma/client';

// Opciono: Uvozite Zod šemu ako neki validatori koriste njene delove ili izvode logiku iz nje
// import { humanitarianOrgSchema } from '@/schemas/humanitarian-org';

/**
 * Proverava da li je humanitarna organizacija aktivna.
 * @param organization - Objekat humanitarne organizacije.
 * @returns True ako je organizacija aktivna, inače False.
 */
export function isHumanitarianOrgActive(organization: Pick<HumanitarianOrg, 'isActive'>): boolean {
    return organization.isActive;
}

/**
 * Proverava da li je humanitarna organizacija "validna" za određenu operaciju, npr. za povezivanje sa novim ugovorom.
 * Ovo može uključivati proveru da li je aktivna i/ili druge uslove.
 * @param organization - Objekat humanitarne organizacije (može biti null).
 * @param criteria - Kriterijumi validnosti (opciono, npr. { needsEmail: boolean }).
 * @returns T
```

## `anomaly-alert.ts`

```ts
// /lib/notifications/anomaly-alert.ts
import { NotificationType, PrismaClient, UserRole } from '@prisma/client';
import { AnomalyDetectionResult } from '@/utils/anomaly-detection';

/**
 * Create notifications for detected anomalies in complaint patterns
 */
export async function createAnomalyAlerts(
  anomaly: AnomalyDetectionResult,
  relatedEntityId: string | null,
  db: PrismaClient
): Promise<void> {
  // Only create alerts for actual detected anomalies
  if (!anomaly.detected) {
    return;
  }
  
  // Find users who should receive anomaly alerts (admins and managers)
  const targetUsers = await db.user.findMany({
    where: {
      role: { in: [UserRole.ADMIN, UserRole.MANAGER] },
      isActive: true
    },
    select: { id: true }
  });
  
  if (targetUsers.length === 0) {
    return;
  }
  
  // Create appropriate title based on severity
  let title = 'Complaint Pattern Alert';
  if (anomaly.severity === 'high') {
    title = '⚠️ URGENT: Critical Complaint Pattern Detected';
```

## `complaint-status.ts`

```ts
// /lib/notifications/complaint-status.ts
import { Complaint, User, NotificationType, Prisma, PrismaClient } from '@prisma/client';
import { ComplaintWithRelations } from '@/lib/types/complaint-types';

type NotificationOptions = {
  includeInternal?: boolean; // Whether to notify internal users
  includeCustomer?: boolean; // Whether to notify customer
  message?: string; // Custom message
};

/**
 * Create status change notifications for relevant users
 */
export async function createStatusChangeNotification(
  complaint: ComplaintWithRelations,
  previousStatus: string | null,
  newStatus: string,
  changedBy: User,
  db: PrismaClient,
  options: NotificationOptions = { includeInternal: true, includeCustomer: true }
): Promise<void> {
  // Default message if not provided
  const message = options.message || 
    `Complaint "${complaint.title}" status changed from ${previousStatus || 'NEW'} to ${newStatus} by ${changedBy.name || changedBy.email}`;
  
  const usersToNotify: string[] =
```

## `cron-alerts.ts`

```ts
////lib/notifications/cron-alerts.ts

import { db } from '@/lib/db';
import { NotificationType, ContractStatus } from '@prisma/client';
import { createNotification, notifyUsersByRole } from './in-app-notifier';
import { logEvent } from '@/actions/security/log-event';

/**
 * Check for contracts expiring soon and send notifications
 * This function should be triggered by a scheduled cron job
 */
export async function checkExpiringContracts() {
  try {
    const today = new Date();

    const thirtyDaysFromNow = new Date();
    thirtyDaysFromNow.setDate(today.getDate() + 30);

    const sevenDaysFromNow = new Date();
    sevenDaysFromNow.setDate(today.getDate() + 7);

    const contracts30Days = await db.contract.findMany({
      where: {
        status: ContractStatus.ACTIVE,
        endDate: {
          gte: today,
          lte: thirtyDaysFromNow
        }
      },
      include: {
        provider: { select: { name: true } },
        humanitarianOrg: { select: { name: true } },
     
```

## `email-sender.ts`

```ts
////lib/notifications/email-sender.ts


import nodemailer from 'nodemailer';
import { Notification, NotificationType, User } from '@prisma/client';
import { db } from '@/lib/db';
import { getEmailTemplate } from './templates';
import { logEvent } from '@/actions/security/log-event';

// Email configuration - use environment variables in production
const emailConfig = {
  host: process.env.EMAIL_HOST || 'smtp.example.com',
  port: parseInt(process.env.EMAIL_PORT || '587'),
  secure: process.env.EMAIL_SECURE === 'true',
  auth: {
    user: process.env.EMAIL_USER || 'user@example.com',
    pass: process.env.EMAIL_PASSWORD || 'password'
  },
  from: process.env.EMAIL_FROM || 'notifications@yourcompany.com'
};

// Create nodemailer transporter
const transporter = nodemailer.createTransport({
  host: emailConfig.host,
  port: emailConfig.port,
  secure: emailConfig.secure,
  auth: emailConfig.auth
});

/**
 * Send email notification to a user
 */
export async function sendEmailNotification({
```

## `in-app-notifier.ts`

```ts
// Path: lib/notifications/in-app-notifier.ts

import { Notification, NotificationType as PrismaNotificationType, User } from '@prisma/client';
import { db } from '@/lib/db'; // Assuming your Prisma client instance
import { pusherServer } from '@/lib/pusher'; // Assuming your Pusher server instance
import { logEvent } from '@/actions/security/log-event'; // Assuming a logging action
import { sendNotificationEmail } from './email-sender'; // Assuming an email sending function
// Import the action to get user preferences and the NotificationPreferences type
import { getUserNotificationPreferences } from '@/actions/notifications/get-preferences';
import { NotificationPreferences, NotificationType } from '@/lib/types/notification-types';


/**
 * Create a notification in the database and trigger delivery based on user preferences.
 * This function now fetches user preferences internally to decide delivery channels.
 */
export async function createNotification({
  userId,
  title,
  message
```

## `templates.ts`

```ts
////lib/notifications/templates.ts


import { NotificationType } from "@prisma/client";

/**
 * Interface for notification template parameters
 * Different notification types require different parameters
 */
export interface NotificationTemplateParams {
  [key: string]: any;
}

/**
 * Contract expiring notification parameters
 */
export interface ContractExpiringParams {
  contractName: string;
  contractNumber: string;
  daysRemaining: number;
  expiryDate: Date;
}

/**
 * Contract renewal status change parameters
 */
export interface ContractRenewalStatusChangeParams {
  contractName: string;
  contractNumber: string;
  newStatus: string;
  humanitarianOrgName?: string;
}

/**
 * Complaint assigned notification parameters
 */
export interface ComplaintAssignedParams {
  complaintId: string;
  complaintTitle: string;
  assignedAgentName: string;
}

/**
 * Complaint updated notification parameters
 */
export interface ComplaintUpdatedParams {
  complaintId: string;
  complaintTitle: st
```

## `validators.ts`

```ts
// lib/operators/validators.ts

import { z } from "zod";
import { operatorSchema } from "@/schemas/operator";

export const validateOperator = (data: unknown) => {
  return operatorSchema.safeParse(data);
};

export const validateOperatorCode = (code: string) => {
  const codeSchema = z.string().min(2).max(20).regex(/^[a-zA-Z0-9_-]+$/);
  return codeSchema.safeParse(code);
};

export const validateOperatorEmail = (email: string | null | undefined) => {
  if (!email) return { success: true };
  const emailSchema = z.string().email();
  return emailSchema.safeParse(email);
};

export const validateOperatorWebsite = (website: string | null | undefined) => {
  if (!website) return { success: true };
  const websiteSchema = z.string().url();
  return websiteSchema.safeParse(website);
};

export const getOperatorValidationErrors = (errors: z.ZodFormattedError<any>) => {
  const formattedErrors: Record<string, string> = {};
  
  Object.entries(errors).forEach(([key, value]) => {
    if (key !
```

## `validators.ts`

```ts
//lib/parking-services/validators.ts

import { z } from "zod";

/**
 * Validation utilities for parking service data
 */

export const parkingServiceSchema = z.object({
  id: z.string().optional(),
  name: z.string().min(1, "Name is required"),
  description: z.string().optional().nullable(),
  contactName: z.string().optional().nullable(),
  email: z.string().email("Invalid email address").optional().nullable(),
  phone: z.string().optional().nullable(),
  address: z.string().optional().nullable(),
  isActive: z.boolean().default(true),
});

export type ParkingServiceFormValues = z.infer<typeof parkingServiceSchema>;

/**
 * Validates a parking service form submission
 */
export function validateParkingService(data: unknown) {
  const result = parkingServiceSchema.safeParse(data);
  if (!result.success) {
    // Format the errors for easier use in forms
    const formattedErrors: Record<string, string> = {};
    result.error.errors.forEach((error) => {
      if (error.path.length > 0)
```

## `validators.ts`

```ts
// /lib/providers/validators.ts

import { Provider } from '@prisma/client'; // Uvoz Provider modela (osnovni tip)
// Ako imate kompleksniji tip sa relacijama, uvezite njega:
// import { ProviderWithDetails } from '@/lib/types/provider-types';

// Opciono: Uvezite Zod šemu ako neki validatori koriste njene delove ili izvode logiku iz nje
// import { providerSchema } from '@/schemas/provider';

/**
 * Proverava da li je provajder aktivan.
 * Iako je ovo jednostavno polje, utility funkcija može biti korisna za konzistentnost.
 * @param provider - Objekat provajdera.
 * @returns True ako je provajder aktivan, inače False.
 */
export function isProviderActive(provider: Pick<Provider, 'isActive'>): boolean {
    return provider.isActive;
}

/**
 * Proverava da li je provajder "validan" za određenu operaciju, npr. za povezivanje sa novim ugovorom.
 * Ovo može uključivati proveru da li je aktivan i/ili druge uslove.
 * @param provider - Objekat provajdera.
 * @param criteria - Kriterijumi vali
```

## `audit-logger.ts`

```ts
// lib/security/audit-logger.ts
import { db } from "@/lib/db";
import { LogSeverity } from "@prisma/client";
import { getCurrentUser } from "./auth-helpers";

interface LogOptions {
  entityType: string;
  entityId?: string;
  details?: string;
  severity?: LogSeverity;
  userId?: string; // Added userId to options
}

export async function logActivity(
  action: string,
  options: LogOptions
) {
  let userId = options.userId;
  
  // If userId is not provided, try to get current user
  if (!userId) {
    const user = await getCurrentUser();
    if (!user) {
      console.error("No authenticated user for activity log");
      return;
    }
    userId = user.id;
  }
  
  const { entityType, entityId, details, severity = LogSeverity.INFO } = options;
  
  return db.activityLog.create({  // Changed prisma to db to match imports
    data: {
      action,
      entityType,
      entityId,
      details,
      severity,
      userId,
    }
  });
}
```

## `auth-helpers.ts`

```ts
// Path: lib/security/auth-helpers.ts

// Import the auth function from your NextAuth.js configuration file
import { auth } from "@/auth"; // Assuming "@/auth" is the path to your NextAuth config exporting 'auth'
import { UserRole } from "@prisma/client"; // Assuming UserRole enum is imported from Prisma client

/**
 * Fetches the current authenticated user from the session.
 * @returns A promise resolving to the user object or undefined if not authenticated.
 */
export async function getCurrentUser() {
    // Use the auth() function to get the session
    const session = await auth();
    // Return the user object from the session
    return session?.user;
}

/**
 * Checks if the current user has any of the required roles.
 * @param requiredRoles - An array of UserRole enums.
 * @returns A promise resolving to true if the user has one of the roles, false otherwise.
 */
export async function hasRequiredRole(requiredRoles: UserRole[]) {
    const user = await getCurrentUser();
    // Ch
```

## `backup-service.ts`

```ts
// /lib/security/backup-service.ts
import { prisma } from "@/lib/db";
import fs from "fs/promises";
import path from "path";
import { exec } from "child_process";
import { promisify } from "util";

const execPromise = promisify(exec);

interface BackupOptions {
  includeSchema?: boolean;
  includeData?: boolean;
  tables?: string[];
}

export async function generateDatabaseBackup(options: BackupOptions = {
  includeSchema: true,
  includeData: true,
}) {
  const timestamp = new Date().toISOString().replace(/[:.]/g, "-");
  const backupDir = path.join(process.cwd(), "backups");
  const backupPath = path.join(backupDir, `backup-${timestamp}`);
  
  try {
    // Ensure backup directory exists
    await fs.mkdir(backupDir, { recursive: true });
    
    // Get DB connection string from environment
    const dbUrl = process.env.DATABASE_URL;
    if (!dbUrl) {
      throw new Error("DATABASE_URL environment variable not found");
    }
    
    // Build pg_dump command
    let command = `pg_d
```

## `black-log.ts`

```ts
//lib/security/black-log.ts

import { db } from "@/lib/db";
import { LogBlackType } from "@prisma/client";

interface LogParams {
  action: LogBlackType;
  entityId: string;
  entityType?: string;
  userId: string;
  oldData?: any;
  newData?: any;
}

export async function createAuditLog(params: LogParams) {
  try {
    console.log("Creating audit log with params:", params); // DEBUG
    
    const result = await db.blacklistLog.create({
      data: {
        action: params.action,
        entityId: params.entityId,
        entityType: params.entityType || "SenderBlacklist",
        userId: params.userId,
        oldData: params.oldData ? JSON.parse(JSON.stringify(params.oldData)) : undefined,
        newData: params.newData ? JSON.parse(JSON.stringify(params.newData)) : undefined,
      }
    });
    
    console.log("Audit log created successfully:", result.id); // DEBUG
    return result;
  } catch (error) {
    console.error("Failed to create audit log:", error);
    throw error; /
```

## `permission-checker.ts`

```ts
// /lib/security/permission-checker.ts
import { db } from "@/lib/db";
import { getCurrentUser } from "./auth-helpers";
import { UserRole } from "@prisma/client";

type EntityType = "contract" | "complaint" | "service" | "provider" |
                 "humanitarian" | "report" | "user" | "analytics"; // Added 'analytics' if needed for analytics view permissions

type ActionType = "view" | "create" | "update" | "delete";

const rolePermissionMap: Record<UserRole, Record<EntityType, ActionType[]>> = {
    ADMIN: {
        contract: ["view", "create", "update", "delete"],
        complaint: ["view", "create", "update", "delete"],
        service: ["view", "create", "update", "delete"],
        provider: ["view", "create", "update", "delete"],
        humanitarian: ["view", "create", "update", "delete"],
        report: ["view", "create", "update", "delete"],
        user: ["view", "create", "update", "delete"],
        analytics: ["view"], // Assume Admins can view analytics
    },
    MANA
```

## `rate-limiter.ts`

```ts
// /lib/security/rate-limiter.ts
import { NextRequest, NextResponse } from "next/server";
import { Redis } from "@upstash/redis";

const redis = Redis.fromEnv();

type RateLimitConfig = {
  maxRequests: number;  // Maximum requests in window
  window: number;       // Time window in seconds
}

const defaultConfig: RateLimitConfig = {
  maxRequests: 60,      // 60 requests
  window: 60,          // per minute
}

export async function rateLimit(
  req: NextRequest, 
  identifier: string, 
  config: RateLimitConfig = defaultConfig
): Promise<{
  success: boolean;
  limit: number;
  remaining: number;
  reset: number;
}> {
  const ip = req.ip || "anonymous";
  const key = `ratelimit:${identifier}:${ip}`;
  
  const now = Math.floor(Date.now() / 1000);
  const windowStart = now - (now % config.window);
  const windowExpiry = windowStart + config.window;
  
  const result = await redis.pipeline()
    .incr(key)
    .expire(key, config.window)
    .exec();
    
  const currentCount = result[0
```

## `activity-log-service.ts`

```ts
// lib/services/activity-log-service.ts

import { db } from "@/lib/db"; // Uvoz tvoje Prisma klijent instance
import { LogSeverity, LogActionType, LogEntityType } from "@prisma/client"; // Uvoz LogSeverity, LogActionType, LogEntityType enum-a
// Uklonjeno: import { getCurrentUser } from "@/lib/session"; // Više se ne dohvaća korisnik unutar ove akcije

// Definicija parametara koje log metoda prihvata
// userId je sada OBAVEZAN
interface ActivityLogParams {
  action: LogActionType; // Naziv akcije koja se loguje (npr. 'CREATE_BULK_SERVICE', 'USER_LOGIN', 'UPDATE_CONTRACT')
  entityType: LogEntityType; // Tip entiteta na koji se akcija odnosi (npr. 'BULK_SERVICE', 'USER', 'CONTRACT')
  entityId: string | null; // ID entiteta (može biti null ako akcija nije vezana za specifičan entitet)
  details?: string; // Dodatni detalji o akciji (opciono)
  severity?: LogSeverity; // Nivo ozbiljnosti loga (INFO, WARNING, ERROR, itd.), podrazumevano INFO
  userId: string; // OBAVEZNO: ID korisnika ko
```

## `constants.ts`

```ts
// /lib/services/constants.ts
// Definicija konstanti i enuma vezanih za servise i proizvode

// Uvozimo ServiceType enum direktno iz Prisma klijenta
import { ServiceType } from '@prisma/client';

// Izvozimo ServiceType enum za korišćenje u kodu (npr. u filterima, formama)
export { ServiceType };

// Primeri mapiranja ili lista dozvoljenih vrednosti ako je potrebno
// export const ServiceCategoryLabels: Record<ServiceType, string> = {
//     [ServiceType.VAS]: 'Value Added Service',
//     [ServiceType.BULK]: 'Bulk Service',
//     [ServiceType.HUMANITARIAN]: 'Humanitarian Service',
//     [ServiceType.PARKING]: 'Parking Service',
// };

// Možete dodati i druge konstante relevantne za servise/proizvode
// export const DEFAULT_SERVICE_LIMIT = 10;
// export const ALLOWED_CSV_MIME_TYPES = ['text/csv'];
```

## `csv-processor.ts`

```ts
// /lib/services/csv-processor.ts
// Utilitiji za parsiranje i validaciju CSV fajlova za import servisa i proizvoda

import { parse } from 'csv-parse/sync'; // Koristimo sync parser
// Uvozimo potrebne tipove
import { ServiceCsvRow, ProductCsvRow, CsvRowValidationResult, CsvImportResult } from '@/lib/types/csv-types';
// Uvozimo Zod šeme za validaciju protiv očekivanog formata
import { serviceSchema, ServiceFormData } from '@/schemas/service';
import { productSchema, ProductFormData } from '@/schemas/product';
// Uvozimo enume ili tipove ako su potrebni za specifične provere (npr. ServiceType)
import { ServiceType } from '@prisma/client'; // Uvozimo ServiceType iz Prisma klijenta

/**
 * Parsira CSV sadržaj u niz JavaScript objekata.
 * @param csvContent - Sadržaj CSV fajla kao string.
 * @returns Niz objekata koji reprezentuju redove CSV-a.
 */
function parseCsvContent(csvContent: string): any[] {
    try {
        // Prilagodite opcije parsera prema formatu vašeg CSV-a
        const 
```

## `statistics.ts`

```ts
// /lib/services/statistics.ts
// Utility funkcije za izračunavanje statistika vezanih za servise i proizvode

import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
// Uvozimo tipove ako su potrebni za ulazne parametre ili povratne vrednosti
import { Service, Product } from '@prisma/client';
// import { ServiceCategory } from '@/lib/types/service-types'; // Ako je potrebno filtrirati po kategoriji

// Primer: Tip za sumarni prikaz prihoda po servisu/proizvodu
interface RevenueSummary {
    id: string;
    name: string;
    totalRevenue: number; // Ukupni prihodi povezani sa ovim entitetom
    contractsCount: number; // Broj povezanih ugovora
}

// Primer: Tip za prosečnu cenu reklamacija po servisu/proizvodu
interface ComplaintCostSummary {
    id: string;
    name: string;
    averageCost: number;
    totalComplaints: number;
}

/**
 * Izračunava sumu prihoda za date servise na osnovu povezanih ugovora.
 * @param serviceIds - Niz ID-jeva servisa za koje se
```

## `analytics-types.ts`

```ts
////lib/types/analytics-types.ts


import { ServiceType } from "@prisma/client";

// Basic time range interface
export interface TimeRange {
  startDate: Date;
  endDate?: Date;
}

// Financial analytics interfaces
export interface FinancialMetric {
  revenue: number;
  costs: number;
  profit: number;
  profitMargin: number;
}

export interface FinancialTrend extends FinancialMetric {
  period: string; // Month/year or date string
}

export interface RevenueBreakdown {
  serviceType: ServiceType;
  revenue: number;
  percentage: number;
}

export interface ProviderRevenue {
  providerId: string;
  providerName: string;
  revenue: number;
  transactions: number;
  serviceCount: number;
}

export interface FinancialOverview {
  totalRevenue: number;
  totalProfit: number;
  averageProfitMargin: number;
  revenueBreakdown: RevenueBreakdown[];
  topProviders: ProviderRevenue[];
  monthlyTrend: FinancialTrend[];
}

// Sales analytics interfaces
export interface SalesMetric {
  transactions
```

## `blacklist.ts`

```ts
// lib/types/blacklist.ts
export interface SenderBlacklistEntry {
  id: string;
  senderName: string;
  effectiveDate: Date;
  description?: string | null;
  isActive: boolean;
  matchCount: number;
  lastMatchDate?: Date | null;
  createdAt: Date;
  updatedAt: Date;
  createdBy: {
    id: string;
    name?: string | null;
  };
}

export interface CreateBlacklistEntryRequest {
  senderName: string;
  effectiveDate: Date;
  description?: string;
  isActive?: boolean;
}

export interface CreateBlacklistEntryResponse {
  success: boolean;
  data?: SenderBlacklistEntry[];
  error?: string;
  message?: string;
}

// Za match-ove
export interface BlacklistMatch {
  blacklistEntry: SenderBlacklistEntry;
  matchingServices: any[];
}
```

## `bulk-service-types.ts`

```ts
//lib/types/bulk-service-types.ts
import { BulkService, Provider, Service } from "@prisma/client";

// Enhanced types with relations
export interface BulkServiceWithRelations extends BulkService {
  provider: Provider;
  service: Service;
}

// Filters for bulk services
export interface BulkServiceFilters {
  providerId?: string;
  serviceId?: string;
  providerName?: string;
  serviceName?: string;
  senderName?: string;
  startDate?: Date;
  endDate?: Date;
}

// Statistics for bulk services
export interface BulkServiceStats {
  totalRequests: number;
  totalMessageParts: number;
  providerDistribution: {
    providerId: string;
    providerName: string;
    requestCount: number;
    messagePartCount: number;
    percentage: number;
  }[];
  serviceDistribution: {
    serviceId: string;
    serviceName: string;
    requestCount: number;
    messagePartCount: number;
    percentage: number;
  }[];
  timeSeriesData?: {
    date: string;
    requests: number;
    messageParts: number;
 
```

## `complaint-types.ts`

```ts
// /lib/types/complaint-types.ts:

import { ComplaintStatus, ServiceType } from "@prisma/client";
import { ComplaintPriority, ServiceCategoryType } from "./enums";
import { Complaint, User, Service, Product, Provider } from "./interfaces";

export type ComplaintWithRelations = Complaint & {
  submittedBy: User;
  assignedAgent?: User;
  service?: Service;
  product?: Product;
  provider?: Provider;
};

export type ComplaintSummary = {
  id: string;
  title: string;
  status: ComplaintStatus;
  priority: number;
  createdAt: Date;
  updatedAt: Date;
  submittedByName: string;
  assignedAgentName?: string;
  serviceName?: string;
  productName?: string;
  providerName?: string;
};

export type ComplaintStatusUpdate = {
  complaintId: string;
  status: ComplaintStatus;
  notes?: string;
};

export type ComplaintAssignment = {
  complaintId: string;
  agentId: string;
};

export type ComplaintComment = {
  complaintId: string;
  text: string;
  isInternal: boolean;
};

export type Complain
```

## `contract-status.ts`

```ts
// /lib/types/contract-status.ts

import { ContractStatus } from '@prisma/client'; // Uvoz Prisma enum-a

// Re-exportujemo Prisma enum radi lakšeg uvoza
export { ContractStatus };

/**
 * Vraća korisnički-prijateljsku labelu za status ugovora.
 * @param status - Status ugovora iz Prisma enum-a.
 * @returns String labela statusa.
 */
export function getContractStatusLabel(status: ContractStatus): string {
  switch (status) {
    case ContractStatus.ACTIVE:
      return 'Active';
    case ContractStatus.EXPIRED:
      return 'Expired';
    case ContractStatus.PENDING:
      return 'Pending';
    case ContractStatus.RENEWAL_IN_PROGRESS:
      return 'Renewal in Progress';
    default:
      // Trebalo bi pokriti sve slučajeve sa enumom, ali dodajemo fallback
      return status.replace(/_/g, ' ').toLowerCase().replace(/\b\w/g, l => l.toUpperCase());
  }
}

/**
 * Vraća CSS klasu (Tailwind) za boju statusa ugovora.
 * Može se koristiti za vizuelno razlikovanje statusa.
 * @param status - 
```

## `contract-types.ts`

```ts
// /lib/types/contract-types.ts
import { ContractStatus, ContractType, ContractRenewalSubStatus, Service } from '@prisma/client';
import { z } from 'zod';
import { contractSchema } from '@/schemas/contract';

export type ContractFormData = z.infer<typeof contractSchema>;

// Re-export Prisma types for easier importing
export { ContractStatus, ContractType, ContractRenewalSubStatus };

export interface SelectedService {
  serviceId: string;
  specificTerms?: string;
}

export interface Contract {
  id: string;
  name: string;
  contractNumber: string;
  type: ContractType;
  status: ContractStatus;
  startDate: Date;
  endDate: Date;
  revenuePercentage: number;
  description: string | null;
  providerId: string | null;
  humanitarianOrgId: string | null;
  parkingServiceId: string | null;
  services?: (Service & { specificTerms?: string })[];
  renewals?: ContractRenewal[];
  provider?: { name: string };
  humanitarianOrg?: { name: string };
  parkingService?: { name: string };
  creat
```

## `enums.ts`

```ts
// /lib/types/enums.ts:

import { ComplaintStatus, ServiceType } from "@prisma/client";

export enum ComplaintPriority {
  CRITICAL = 1,
  HIGH = 2,
  MEDIUM = 3,
  LOW = 4,
  TRIVIAL = 5
}

export enum ComplaintSortField {
  CREATED_AT = "createdAt",
  UPDATED_AT = "updatedAt",
  PRIORITY = "priority",
  STATUS = "status"
}

export enum SortDirection {
  ASC = "asc",
  DESC = "desc"
}

export enum FileType {
  IMAGE = "image",
  DOCUMENT = "document",
  SPREADSHEET = "spreadsheet",
  PDF = "pdf",
  OTHER = "other"
}

export enum ReportType {
  COMPLAINT_SUMMARY = "complaint_summary",
  RESOLUTION_TIME = "resolution_time",
  PROVIDER_PERFORMANCE = "provider_performance",
  SERVICE_ISSUES = "service_issues",
  PRODUCT_ISSUES = "product_issues",
  AGENT_PERFORMANCE = "agent_performance"
}

// Re-export from prisma for easier imports
export { ComplaintStatus, ServiceType };

export enum ExportFormat {
  CSV = "csv",
  EXCEL = "excel",
  PDF = "pdf",
  JSON = "json"
}

export enum ServiceC
```

## `humanitarian-org-types.ts`

```ts
// /lib/types/humanitarian-org-types.ts
export interface HumanitarianOrg {
  id: string;
  name: string;
  email: string | null;
  phone: string | null;
  contactPerson: string | null;
  address: string | null;
  website: string | null;
  mission: string | null;
  isActive: boolean;
  createdAt: Date;
  updatedAt: Date;
  pib: string | null;
  registrationNumber: string | null;
  bank: string | null;
  accountNumber: string | null;
  shortNumber: string | null;
}

export interface HumanitarianOrgWithDetails extends HumanitarianOrg {
  _count?: {
    contracts: number;
    complaints: number;
    renewals: number;
  }
}

export interface HumanitarianOrgFilterOptions {
  search?: string;
  isActive?: boolean;
  sortBy?: 'name' | 'createdAt' | 'updatedAt';
  sortDirection?: 'asc' | 'desc';
}

export interface HumanitarianOrgsResult {
  data: HumanitarianOrgWithDetails[];
  total: number;
  page: number;
  limit: number;
}
```

## `humanitarian-renewal-types.ts`

```ts
// /lib/types/humanitarian-renewal-types.ts
import { Prisma } from "@prisma/client";

// Osnovni tip obnove sa uključenim relacijama
export type HumanitarianRenewalWithRelations = Prisma.HumanitarianContractRenewalGetPayload<{
  include: {
    contract: {
      include: {
        humanitarianOrg: true;
      };
    };
    humanitarianOrg: true;
    createdBy: {
      select: {
        id: true;
        name: true;
        email: true;
      };
    };
    lastModifiedBy: {
      select: {
        id: true;
        name: true;
        email: true;
      };
    };
  };
}>;

// Tip za listu obnova
export type HumanitarianRenewalsList = {
  renewals: HumanitarianRenewalWithRelations[];
  total: number;
  page: number;
  limit: number;
  totalPages: number;
};

// Tip za dashboard statistike
export type RenewalStatistics = {
  totalRenewals: number;
  inProgress: number;
  awaitingSignature: number;
  completed: number;
  averageProgress: number;
  renewalsByStatus: {
    status: string;
   
```

## `interfaces.ts`

```ts
// /lib/types/interfaces.ts:

import { ComplaintStatus, ServiceType, UserRole } from "@prisma/client";

export interface ComplaintBase {
  title: string;
  description: string;
  status: ComplaintStatus;
  priority: number;
  financialImpact?: number;
  serviceId?: string;
  productId?: string;
  providerId?: string;
}

export interface Complaint extends ComplaintBase {
  id: string;
  submittedById: string;
  submittedBy: User;
  assignedAgentId?: string;
  assignedAgent?: User;
  assignedAt?: Date;
  createdAt: Date;
  updatedAt: Date;
  resolvedAt?: Date;
  closedAt?: Date;
  service?: Service;
  product?: Product;
  provider?: Provider;
  comments: Comment[];
  attachments: Attachment[];
  statusHistory: ComplaintStatusHistory[];
}

export interface ComplaintStatusHistory {
  id: string;
  complaintId: string;
  previousStatus?: ComplaintStatus;
  newStatus: ComplaintStatus;
  changedById: string;
  changedAt: Date;
  notes?: string;
}

export interface Comment {
  id: string;
  te
```

## `notification-types.ts`

```ts
// Path: lib/types/notification-types.ts

// Notification types
export type NotificationType =
  | "CONTRACT_EXPIRING"
  | "CONTRACT_RENEWAL_STATUS_CHANGE"
  | "COMPLAINT_ASSIGNED"
  | "COMPLAINT_UPDATED"
  | "REMINDER"
  | "SYSTEM";

// Base notification interface
export interface Notification {
  id: string;
  title: string;
  message: string;
  type: NotificationType;
  isRead: boolean;
  userId: string;
  entityType?: string;
  entityId?: string;
  createdAt: Date;
}

// Contract expiring notification specifics
export interface ContractExpiryNotification extends Notification {
  type: "CONTRACT_EXPIRING";
  entityType: "contract";
  daysRemaining: number;
}

// Contract renewal status notification specifics
export interface ContractRenewalNotification extends Notification {
  type: "CONTRACT_RENEWAL_STATUS_CHANGE";
  entityType: "renewal";
  previousStatus: string;
  newStatus: string;
}

// Complaint notification specifics
export interface ComplaintNotification extends Notificatio
```

## `operator-types.ts`

```ts
// lib/types/operator-types.ts


import { Operator } from "@prisma/client";

// Base Operator type from Prisma
export type OperatorType = Operator;

// Extended Operator type with related data
export interface OperatorWithRelations extends Operator {
  contracts?: {
    id: string;
    name: string;
    contractNumber: string;
    startDate: Date;
    endDate: Date;
    revenuePercentage: number;
    operatorRevenue: number | null;
  }[];
  _count?: {
    contracts: number;
  };
}

// Type for operator list item with minimal data
export interface OperatorListItem {
  id: string;
  name: string;
  code: string;
  contactEmail: string | null;
  contactPhone: string | null;
  active: boolean;
  contractCount: number;
}

// Type for operator creation/update API payload
export interface OperatorPayload {
  name: string;
  code: string;
  description?: string | null;
  logoUrl?: string | null;
  website?: string | null;
  contactEmail?: string | null;
  contactPhone?: string | null;
  active
```

## `parking-service-types.ts`

```ts
// lib/types/parking-service-types.ts

// Types for Parking Services
import { ParkingService } from "@prisma/client";

// Base Parking Service type from Prisma schema
export type ParkingServiceType = ParkingService;

// Base interface for parking service data
export interface ParkingServiceFormData {
  id?: string;
  name: string;
  description?: string;
  contactName?: string;
  email?: string;
  phone?: string;
  address?: string;
  additionalEmails?: string[]; // Novo polje za dodatne email adrese
  isActive: boolean;
}

// Type for creating a new parking service
export interface CreateParkingServiceParams {
  name: string;
  description?: string;
  contactName?: string;
  email?: string;
  phone?: string;
  address?: string;
  additionalEmails?: string[];
  isActive?: boolean;
  // New file tracking fields
  originalFileName?: string;
  originalFilePath?: string;
  fileSize?: number;
  mimeType?: string;
  lastImportDate?: Date;
  importedBy?: string;
  importStatus?: 'success' | '
```

## `product-types.ts`

```ts
// /lib/types/product-types.ts

// Uvozimo Product model iz Prisma klijenta
import { Product } from '@prisma/client';

// Tip koji proširuje Prisma Product model
export interface ProductWithDetails extends Product {
    _count?: {
        // Brojači za relacije koje postoje u vašoj schema.prisma Product modelu
        complaints?: number; // Relacija Complaint[]
    };
    // Relacija 'services' NE postoji direktno na Product modelu u vašoj šemi
}

// Tip za opcije filtera za proizvode
// Usklađen sa poljima u vašoj schema.prisma Product modelu i query parametrima
export interface ProductFilterOptions {
    search?: string; // Pretraga po imenu, šifri (code), opisu
    isActive?: boolean; // Filtriranje po statusu aktivnost
    // serviceId filter NE postoji jer Product model u vašoj šemi nema direktnu relaciju ka Service modelu
    // Dodajte druge opcije filtera ako se pojave nova polja u Product modelu
}

// Tip za odgovor API rute koja vraća listu proizvoda sa totalCount
export int
```

## `provider-types.ts`

```ts
// /lib/types/provider-types.ts
import { Provider } from "@prisma/client";

// Основни типи
export interface ProviderBase extends Provider {
  // Поље 'services' није потребно јер се већ налази у Prisma типу
}

// Проширени тип са бројачима релација
export interface ProviderWithCounts extends Provider {
  _count?: {
    contracts: number;
    vasServices: number;
    bulkServices: number;
    complaints: number;
  };
}

// Опције за филтрирање и сортирање
export interface ProviderFilterOptions {
  search?: string;
  isActive?: boolean;
  hasContracts?: boolean;
  hasComplaints?: boolean;
  sortBy?: 'name' | 'createdAt';
  sortDirection?: 'asc' | 'desc';
}

// Опције за пагинацију
export interface PaginationOptions {
  page: number;
  limit: number;
}

// Резултат API позива
export interface ProvidersResult {
  data: ProviderWithCounts[];
  total: number;
  page: number;
  limit: number;
  error?: string | null;
}
```

## `security-types.ts`

```ts
////lib/types/security-types.ts


import { UserRole } from "@prisma/client";

// Activity log severity levels
export type LogSeverity = "INFO" | "WARNING" | "ERROR" | "CRITICAL";

// Activity log interface
export interface ActivityLog {
  id: string;
  action: string;
  entityType: string;
  entityId?: string;
  details?: string;
  severity: LogSeverity;
  userId: string;
  createdAt: Date;
}

// Activity log filter options
export interface ActivityLogFilter {
  startDate?: Date;
  endDate?: Date;
  action?: string;
  entityType?: string;
  entityId?: string;
  userId?: string;
  severity?: LogSeverity;
}

// Permission action types
export type PermissionAction = "CREATE" | "READ" | "UPDATE" | "DELETE" | "EXPORT" | "ADMIN";

// Permission interface
export interface Permission {
  id: string;
  name: string;
  description?: string;
  module: string;
  action: PermissionAction;
}

// Role permissions mapping
export interface RolePermissions {
  role: UserRole;
  permissions: string[]; //
```

## `service-types.ts`

```ts
// /lib/types/service-types.ts
import { Service, ServiceType as PrismaServiceType, Provider } from '@prisma/client';

export const ServiceType = PrismaServiceType;

export interface ServiceWithDetails extends Service {
    _count?: {
        contracts?: number;
        vasServices?: number;
        bulkServices?: number;
        complaints?: number;
    };
    // Add provider relationships
    vasServices?: {
        provider: Provider;
    }[];
    bulkServices?: {
        provider: Provider;
    }[];
}

export interface ServiceFilterOptions {
    search?: string;
    type?: ServiceType;
    isActive?: boolean;
}

export interface ServicesApiResponse {
    services: ServiceWithDetails[];
    totalCount: number;
}
```


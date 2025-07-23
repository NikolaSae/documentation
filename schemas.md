# Sekcija: schemas

## `analytics.ts`

```ts
////schemas/analytics.ts


import { z } from "zod";

// Analytics time range validation schema
export const AnalyticsTimeRangeSchema = z.object({
  startDate: z.coerce.date(),
  endDate: z.coerce.date().optional().default(() => new Date()),
});

// Financial analytics filter schema
export const FinancialAnalyticsFilterSchema = z.object({
  ...AnalyticsTimeRangeSchema.shape,
  providerId: z.string().optional(),
  serviceId: z.string().optional(),
  serviceType: z.enum(["VAS", "BULK", "HUMANITARIAN", "PARKING"]).optional(),
});

// Sales analytics filter schema
export const SalesAnalyticsFilterSchema = z.object({
  ...AnalyticsTimeRangeSchema.shape,
  providerId: z.string().optional(),
  serviceId: z.string().optional(),
  productId: z.string().optional(),
});

// Complaint analytics filter schema
export const ComplaintAnalyticsFilterSchema = z.object({
  ...AnalyticsTimeRangeSchema.shape,
  status: z.enum([
    "NEW", 
    "ASSIGNED", 
    "IN_PROGRESS", 
    "PENDING", 
    "RESOLVED",
```

## `auth.ts`

```ts
// schemas/auth.ts
import { z } from "zod";

// Login schema
export const loginSchema = z.object({
  email: z
    .string()
    .min(1, "Email is required")
    .email("Invalid email address")
    .toLowerCase(),
  password: z
    .string()
    .min(1, "Password is required")
    .min(6, "Password must be at least 6 characters"),
});

// Register schema
export const registerSchema = z.object({
  email: z
    .string()
    .min(1, "Email is required")
    .email("Invalid email address")
    .toLowerCase(),
  password: z
    .string()
    .min(8, "Password must be at least 8 characters")
    .regex(/[A-Z]/, "Password must contain at least one uppercase letter")
    .regex(/[a-z]/, "Password must contain at least one lowercase letter")
    .regex(/[0-9]/, "Password must contain at least one number")
    .regex(/[^A-Za-z0-9]/, "Password must contain at least one special character"),
  confirmPassword: z.string().min(1, "Please confirm your password"),
  name: z.string().min(1, "Name is req
```

## `bulk-service.ts`

```ts
//schemas/bulk-service.ts

import { z } from "zod";

// Basic validation schema for bulk service
export const bulkServiceSchema = z.object({
  provider_name: z.string()
    .min(1, "Provider name is required"),
  
  agreement_name: z.string()
    .min(1, "Agreement name is required"),
  
  service_name: z.string()
    .min(1, "Service name is required"),
  
  step_name: z.string() 
    .min(1, "Step name is required"),
  
  sender_name: z.string()
    .min(1, "Sender name is required"),
  
  requests: z.coerce.number()
    .int("Requests must be an integer")
    .min(0, "Requests must be a non-negative number"),
  
  message_parts: z.coerce.number()
    .int("Message parts must be an integer")
    .min(0, "Message parts must be a non-negative number"),
  
  serviceId: z.string().uuid("Invalid service ID"),
  
  providerId: z.string().uuid("Invalid provider ID"),
});

// Extended validation schema for bulk service with optional fields (for updates)
export const bulkServiceUpdateSchema =
```

## `complaint.ts`

```ts
// Path: schemas/complaint.ts

import { z } from "zod";
import { ComplaintStatus } from "@prisma/client"; // Assuming ComplaintStatus enum is imported from Prisma client

// Schema for creating a new complaint (used by the form)
export const ComplaintSchema = z.object({
  title: z.string().min(5, { message: "Title must be at least 5 characters long" }).max(100),
  description: z.string().min(10, { message: "Description must be at least 10 characters long" }),
   priority: z.number({
    invalid_type_error: "Priority must be a number",
    required_error: "Priority is required",
  }).int({ message: "Priority must be an integer" }).min(1, { message: "Priority must be at least 1" }).max(5, { message: "Priority must be at most 5" }),
  // serviceId, productId, providerId are now optional/nullable in this schema
  serviceId: z.string().optional().nullable(),
  // productId: z.string().optional().nullable(),
  providerId: z.string().optional().nullable(),
  financialImpact: z.number().option
```

## `contract.ts`

```ts
// /schemas/contract.ts
import { z } from 'zod';
import { ContractStatus, ContractType } from '@prisma/client';

// Create a schema for the services array
const serviceSchema = z.object({
  serviceId: z.string().min(1, "Service ID is required"),
  specificTerms: z.string().optional().nullable()
});

// Define the base contract schema
const baseContractSchema = z.object({
  name: z.string().min(1, { message: "Contract name is required" }),
  contractNumber: z.string().min(1, { message: "Contract number is required" }),
  type: z.nativeEnum(ContractType, {
    errorMap: () => ({ message: "Invalid contract type" }),
  }),
  status: z.string(), // Changed from enum to string to match the form data structure
  startDate: z.union([
  z.string().refine((val) => !isNaN(new Date(val).getTime()), {
    message: "Start date must be a valid date string",
  }),
  z.date()
]).transform((val) => typeof val === 'string' ? val : val.toISOString()),

endDate: z.union([
  z.string().refine((val) => !isNa
```

## `contract-attachment.ts`

```ts
// /schemas/contract-attachment.ts

import { z } from 'zod';

// Šema za validaciju podataka priloga ugovora
export const contractAttachmentSchema = z.object({
  // contractId se obično dobija iz konteksta (URL, forma) a ne direktno validira u šemi priloga
  // contractId: z.string().cuid("Invalid contract ID format"),

  name: z.string().min(1, { message: "Attachment name is required" }), // Ime fajla
  fileUrl: z.string().url({ message: "Invalid file URL format" }), // URL gde je fajl sačuvan
  fileType: z.string().min(1, { message: "File type is required" }), // Tip fajla (MIME type)
  // uploadedById se obično dobija iz sesije korisnika na serveru
  // uploadedById: z.string().cuid("Invalid uploader ID format"),

  // created/updated at polja su obično automatski generisana u bazi
});

// Tip koji se izvodi iz Zod šeme
export type ContractAttachmentData = z.infer<typeof contractAttachmentSchema>;
```

## `contract-reminder.ts`

```ts
// /schemas/contract-reminder.ts

import { z } from 'zod';

// Šema za validaciju podataka pri kreiranju podsetnika
export const createContractReminderSchema = z.object({
  // contractId se obično dobija iz konteksta (URL, forma), ali može se uključiti ako se validira ceo objekat
  // contractId: z.string().cuid("Invalid contract ID format"),

  reminderDate: z.string().refine((val) => !isNaN(new Date(val).getTime()), { // Validacija datuma kao string
     message: "Reminder date is required and must be a valid date",
  }).transform((val) => new Date(val)), // Transformiši u Date objekat
  reminderType: z.string().min(1, { message: "Reminder type is required" }), // Tip podsetnika (string)
  // Opciono: rigoroznija validacija tipa ako koristite fiksne vrednosti
  // reminderType: z.enum(["expiration", "renewal", "review"], {
  //   errorMap: () => ({ message: "Invalid reminder type" }),
  // }),
});

// Tip koji se izvodi iz šeme za kreiranje
export type CreateContractReminderData = z.
```

## `humanitarian-org.ts`

```ts
// /schemas/humanitarian-org.ts
import { z } from 'zod';

export const humanitarianOrgSchema = z.object({
  id: z.string().cuid("Invalid organization ID format").optional(),
  name: z.string().min(1, { message: "Organization name is required" }),
  contactPerson: z.string().nullable().optional(),
  email: z.string().email("Invalid email format").or(z.literal('')).nullable().optional().transform(e => e === "" ? null : e),
  phone: z.string().nullable().optional(),
  address: z.string().nullable().optional(),
  website: z.string().url("Invalid website URL format").or(z.literal('')).nullable().optional().transform(e => e === "" ? null : e),
  mission: z.string().nullable().optional(),
  isActive: z.boolean({
    invalid_type_error: "Active status must be true or false",
  }).default(true),
  
  // New fields
  pib: z.string()
    .regex(/^\d{8,9}$/, "PIB must be 8-9 digits")
    .nullable()
    .optional()
    .transform(v => v || null),  // Convert empty string to null
  registrationNumb
```

## `humanitarian-renewal.ts`

```ts
// schemas/humanitarian-renewal.ts
import { z } from "zod";

export const HumanitarianRenewalSubStatusEnum = z.enum([
  "DOCUMENT_COLLECTION",
  "LEGAL_REVIEW",
  "FINANCIAL_APPROVAL",
  "AWAITING_SIGNATURE",
  "FINAL_PROCESSING"
]);

// Prvo definiramo bazni schema objekat
const baseHumanitarianRenewalSchema = z.object({
  contractId: z.string().min(1, "Ugovor je obavezan").optional(), // Može biti opcionalan za kreiranje
  humanitarianOrgId: z.string().min(1, "Humanitarna organizacija je obavezna").optional(), // Može biti opcionalan za kreiranje
  subStatus: HumanitarianRenewalSubStatusEnum.default("DOCUMENT_COLLECTION"),
  proposedStartDate: z.string().min(1, "Predloženi početak je obavezan")
    .refine((date) => !isNaN(Date.parse(date)), "Neispravna vrednost datuma"),
  proposedEndDate: z.string().min(1, "Predloženi kraj je obavezan")
    .refine((date) => !isNaN(Date.parse(date)), "Neispravna vrednost datuma"),
  proposedRevenue: z.number()
    .min(0, "Procenat prihoda mora bit
```

## `index.ts`

```ts

//schemas/index.ts


import { UserRole } from "@prisma/client";
import * as z from "zod";

// Auth schemas
export const LoginSchema = z.object({
  email: z.string().email({
    message: "Email is required",
  }),
  password: z.string().min(1, {
    message: "Password is required",
  }),
  code: z.optional(z.string()),
});

export const RegisterSchema = z.object({
  email: z.string().email({
    message: "Email is required",
  }),
  password: z.string().min(6, {
    message: "Minimum 6 characters required",
  }),
  name: z.string().min(1, {
    message: "Name is required",
  }),
});

// schemas/index.ts - Export all schemas from a central location

// Auth schemas
export {
  loginSchema,
  registerSchema,
  resetPasswordSchema,
  newPasswordSchema,
  changePasswordSchema,
  profileUpdateSchema,
  userRoleUpdateSchema,
  userActivationSchema,
  verifyEmailSchema,
  twoFactorSetupSchema,
  twoFactorVerificationSchema,
  type LoginFormData,
  type RegisterFormData,
  type ResetPasswordFor
```

## `notification.ts`

```ts
////schemas/notification.ts


import { z } from "zod";

// Base notification schema
export const NotificationSchema = z.object({
  title: z.string().min(1).max(100),
  message: z.string().min(1),
  type: z.enum([
    "CONTRACT_EXPIRING",
    "CONTRACT_RENEWAL_STATUS_CHANGE",
    "COMPLAINT_ASSIGNED",
    "COMPLAINT_UPDATED",
    "REMINDER",
    "SYSTEM"
  ]),
  userId: z.string(),
  entityType: z.string().optional(),
  entityId: z.string().optional(),
});

// Create notification schema
export const CreateNotificationSchema = NotificationSchema.omit({ 
  userId: true 
}).extend({
  userIds: z.array(z.string()).min(1)
});

// Update notification schema (for marking as read)
export const UpdateNotificationSchema = z.object({
  id: z.string(),
  isRead: z.boolean(),
});

// Notification preferences schema
export const NotificationPreferencesSchema = z.object({
  contractExpiring: z.boolean().default(true),
  contractRenewalStatusChange: z.boolean().default(true),
  complaintAssigned: z.boo
```

## `operator.ts`

```ts
// schemas/operator.ts
// schemas/operator.ts
import { z } from "zod";

// Operator creation/update schema
export const operatorSchema = z.object({
  name: z
    .string()
    .min(1, "Name is required")
    .min(2, "Name must be at least 2 characters")
    .max(100, "Name must be less than 100 characters"),
  
  code: z
    .string()
    .min(1, "Code is required")
    .min(2, "Code must be at least 2 characters")
    .max(50, "Code must be less than 50 characters")
    .regex(/^[A-Z0-9_-]+$/, "Code must contain only uppercase letters, numbers, underscores, and hyphens"),
  
  description: z
    .string()
    .min(1, "Description is required")
    .min(10, "Description must be at least 10 characters")
    .max(500, "Description must be less than 500 characters"),
  
  logoUrl: z
    .string()
    .url("Invalid logo URL")
    .optional()
    .or(z.literal("")),
  
  website: z
    .string()
    .url("Invalid website URL")
    .optional()
    .or(z.literal("")),
  
  contactEmail: z
   
```

## `parking-service.ts`

```ts
//schemas/parking-service.ts

import { z } from "zod";

// Schema for validating new parking service creation
export const createParkingServiceSchema = z.object({
  name: z.string().min(1, "Name is required").max(100, "Name must be less than 100 characters"),
  description: z.string().max(500, "Description must be less than 500 characters").optional(),
  contactName: z.string().max(100, "Contact name must be less than 100 characters").optional(),
  email: z
    .string()
    .email("Please enter a valid email address")
    .max(100, "Email must be less than 100 characters")
    .optional()
    .nullable(),
  phone: z
    .string()
    .max(20, "Phone number must be less than 20 characters")
    .optional()
    .nullable(),
  address: z
    .string()
    .max(200, "Address must be less than 200 characters")
    .optional()
    .nullable(),
  additionalEmails: z.array(z.string().email("Invalid email format")).optional().default([]),
  isActive: z.boolean().optional().default(true),
});


```

## `product.ts`

```ts
// /schemas/product.ts

import { z } from "zod";

export const productSchema = z.object({

name: z.string().min(3, "Product name must be at least 3 characters"),

code: z.string().min(2, "Product code must be at least 2 characters"),

description: z.string().optional(),

isActive: z.boolean().default(true),

});

export const productUpdateSchema = productSchema.partial().extend({

id: z.string(),

});

export const productFilterSchema = z.object({

isActive: z.boolean().optional(),

name: z.string().optional(),

code: z.string().optional(),

});
```

## `provider.ts`

```ts
// /schemas/provider.ts

import { z } from 'zod';

// Šema za validaciju podataka provajdera
export const providerSchema = z.object({
    // ID nije potreban pri kreiranju, ali je potreban pri validaciji za ažuriranje (ako je uključen)
    // id: z.string().cuid("Invalid provider ID format").optional(),

    name: z.string().min(1, { message: "Provider name is required" }),
    contactName: z.string().nullable().optional(), // Može biti null ili undefined, ali ako je string, mora biti non-empty ako želite minLength
    // Možete dodati refine ako želite da prazan string bude null/undefined
    // contactName: z.string().nullable().optional().transform(e => e === "" ? null : e),


    email: z.string().email("Invalid email format").nullable().optional(), // Može biti null/undefined, ali ako je string, mora biti validan email
    // email: z.string().email("Invalid email format").or(z.literal('')).nullable().optional().transform(e => e === "" ? null : e), // Primer kako rukovati praznim 
```

## `security.ts`

```ts
////schemas/security.ts


import { z } from "zod";

// Activity log schema
export const ActivityLogSchema = z.object({
  action: z.string().min(1),
  entityType: z.string().min(1),
  entityId: z.string().optional(),
  details: z.string().optional(),
  severity: z.enum(["INFO", "WARNING", "ERROR", "CRITICAL"]).default("INFO"),
  userId: z.string(),
});

// Activity log filter schema
export const ActivityLogFilterSchema = z.object({
  startDate: z.coerce.date().optional(),
  endDate: z.coerce.date().optional(),
  action: z.string().optional(),
  entityType: z.string().optional(),
  entityId: z.string().optional(),
  userId: z.string().optional(),
  severity: z.enum(["INFO", "WARNING", "ERROR", "CRITICAL"]).optional(),
});

// Permission schema
export const PermissionSchema = z.object({
  id: z.string(),
  name: z.string().min(1),
  description: z.string().optional(),
  module: z.string(),
  action: z.enum(["CREATE", "READ", "UPDATE", "DELETE", "EXPORT", "ADMIN"]),
});

// Role based acce
```

## `service.ts`

```ts
// /schemas/service.ts

import { z } from "zod";
import { ServiceType } from "@prisma/client";

export const serviceSchema = z.object({
name: z.string().min(3, "Service name must be at least 3 characters"),
type: z.nativeEnum(ServiceType),
description: z.string().optional(),
isActive: z.boolean().default(true),
});

export const serviceUpdateSchema = serviceSchema.partial().extend({
id: z.string(),
});

export const serviceFilterSchema = z.object({
type: z.nativeEnum(ServiceType).optional(),
isActive: z.boolean().optional(),
name: z.string().optional(),
});
```


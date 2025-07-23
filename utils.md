# Sekcija: utils

## `anomaly-detection.ts`

```ts
// /utils/anomaly-detection.ts
import { Complaint } from '@prisma/client';
import { addDays, subDays, isWithinInterval } from 'date-fns';

export type AnomalyDetectionResult = {
  detected: boolean;
  type: 'volume' | 'financial' | 'service' | 'provider' | 'none';
  details: string;
  severity: 'low' | 'medium' | 'high';
};

export type AnomalyDetectionOptions = {
  volumeThreshold?: number; // % increase over baseline
  financialThreshold?: number; // % increase over baseline
  serviceConcentrationThreshold?: number; // % of complaints for a single service
  providerConcentrationThreshold?: number; // % of complaints for a single provider
  timeWindow?: number; // days to analyze
};

const DEFAULT_OPTIONS: AnomalyDetectionOptions = {
  volumeThreshold: 50, // 50% increase
  financialThreshold: 75, // 75% increase
  serviceConcentrationThreshold: 60, // 60% for one service
  providerConcentrationThreshold: 60, // 60% for one provider
  timeWindow: 7, // 7 days
};

export async function
```

## `complaint-notification.ts`

```ts
// utils/complaint-notification.ts
import { ComplaintStatus } from "@/lib/types/enums"; // Proverite putanju
import { Complaint, ComplaintWithRelations } from "@/lib/types/complaint-types"; // Proverite putanju
import { NotificationType } from "@prisma/client"; // Uvezeno NotificationType iz Prisma klijenta
import { db } from "@/lib/db"; // Proverite putanju za db

interface NotificationData {
  title: string;
  message: string;
  type: NotificationType;
  userId?: string; // userId je opcionalan ako koristite 'roles'
  roles?: string[]; // Dodata opcija za slanje notifikacija po rolama
  entityType: string;
  entityId: string;
}

// ISPRAVLJENO: Dodat export keyword
export async function createNotification(data: NotificationData): Promise<void> {
  // Ako su prosleđene role, pronađite korisnike sa tim rolama
  if (data.roles && data.roles.length > 0) {
    const usersToNotify = await db.user.findMany({
      where: {
        role: {
          in: data.roles,
        },
      },
      
```

## `complaint-statistics.ts`

```ts
// utils/complaint-statistics.ts
// Uklonjene lokalne definicije tipova, oslanjamo se na uvoz
import { Complaint, ComplaintWithRelations } from "@/lib/types/complaint-types";
// Proverite putanju za ComplaintStatus i ServiceType ako nisu iz Prisma klijenta
import { ComplaintStatus, ServiceType } from "@/lib/types/enums"; // Pretpostavljamo da su enumi ovde
import { format, differenceInDays, differenceInHours, isWithinInterval, subDays, subMonths } from "date-fns";

/**
 * Calculate resolution time for a complaint
 * @param complaint The complaint object
 * @returns Resolution time in days or null if not resolved
 */
export function calculateResolutionTime(complaint: Complaint): number | null {
  if (!complaint.resolvedAt) {
    return null;
  }

  const createdAt = new Date(complaint.createdAt);
  const resolvedAt = new Date(complaint.resolvedAt);

  // Proveravamo da li su datumi validni pre izračunavanja razlike
  if (isNaN(createdAt.getTime()) || isNaN(resolvedAt.getTime())) {
    c
```

## `complaint-status.ts`

```ts
// utils/complaint-status.ts
import { ComplaintStatus } from '@prisma/client';

/**
 * Returns a human-readable label for a complaint status
 * @param status ComplaintStatus enum value
 * @returns Formatted status label
 */
export function getStatusLabel(status: ComplaintStatus): string {
  switch (status) {
    case 'NEW':
      return 'New';
    case 'ASSIGNED':
      return 'Assigned';
    case 'IN_PROGRESS':
      return 'In Progress';
    case 'PENDING':
      return 'Pending';
    case 'RESOLVED':
      return 'Resolved';
    case 'CLOSED':
      return 'Closed';
    case 'REJECTED':
      return 'Rejected';
    default:
      return status;
  }
}

/**
 * Returns the appropriate CSS class for a status badge
 * @param status ComplaintStatus enum value
 * @returns CSS class name for the status
 */
export function getStatusColorClass(status: ComplaintStatus): string {
  switch (status) {
    case 'NEW':
      return 'bg-blue-100 text-blue-800';
    case 'ASSIGNED':
      return 'bg-
```

## `csv-validator.ts`

```ts
// /utils/csv-validator.ts
import { z } from 'zod';
import { ComplaintStatus } from '@prisma/client';
import { parse as csvParse } from 'csv-parse/sync';

export type ValidationError = {
  row: number;
  field: string;
  message: string;
};

export type ValidationResult<T> = {
  isValid: boolean;
  data: T[];
  errors: ValidationError[];
};

// Schema for complaint CSV import
export const ComplaintImportSchema = z.object({
  title: z.string().min(5, "Title must be at least 5 characters"),
  description: z.string().min(10, "Description must be at least 10 characters"),
  status: z.enum([
    'NEW', 'ASSIGNED', 'IN_PROGRESS', 'PENDING', 'RESOLVED', 'CLOSED', 'REJECTED'
  ] as [ComplaintStatus, ...ComplaintStatus[]]).default('NEW'),
  priority: z.coerce.number().min(1).max(5).default(3),
  financialImpact: z.coerce.number().optional().nullable(),
  serviceId: z.string().optional().nullable(),
  productId: z.string().optional().nullable(),
  providerId: z.string().optional().nullable(),
  
```

## `date-filters.ts`

```ts
// /utils/date-filters.ts
import { startOfDay, endOfDay, startOfWeek, endOfWeek, startOfMonth, endOfMonth, startOfQuarter, endOfQuarter, startOfYear, endOfYear, subDays, subWeeks, subMonths, subQuarters, subYears, format } from 'date-fns';

export type DateRange = {
  startDate: Date;
  endDate: Date;
};

export type DateFilterOption = 
  | 'today'
  | 'yesterday'
  | 'thisWeek'
  | 'lastWeek'
  | 'thisMonth'
  | 'lastMonth'
  | 'thisQuarter'
  | 'lastQuarter'
  | 'thisYear'
  | 'lastYear'
  | 'last7Days'
  | 'last30Days'
  | 'last90Days'
  | 'allTime';

// Add the missing formatDate function
export function formatDate(date: Date | null | undefined): string {
  if (!date) return '';
  return format(date, 'yyyy-MM-dd HH:mm:ss');
}

export function getDateRange(option: DateFilterOption): DateRange {
  const now = new Date();
  
  switch (option) {
    case 'today':
      return {
        startDate: startOfDay(now),
        endDate: endOfDay(now)
      };
    
    case 'yesterday': {
    
```

## `excel-generator.ts`

```ts
// /utils/excel-generator.ts
import ExcelJS from 'exceljs';
import { Complaint, Comment } from '@prisma/client';
import { ComplaintWithRelations } from '@/lib/types/complaint-types';

type ExportOptions = {
  includeComments?: boolean;
  includeStatusHistory?: boolean;
  dateRange?: {
    startDate: Date;
    endDate: Date;
  };
};

export async function generateComplaintExcel(
  complaints: ComplaintWithRelations[],
  options: ExportOptions = {}
): Promise<Buffer> {
  const workbook = new ExcelJS.Workbook();
  const worksheet = workbook.addWorksheet('Complaints');
  
  // Set up headers
  worksheet.columns = [
    { header: 'ID', key: 'id', width: 10 },
    { header: 'Title', key: 'title', width: 30 },
    { header: 'Status', key: 'status', width: 15 },
    { header: 'Priority', key: 'priority', width: 10 },
    { header: 'Service', key: 'service', width: 20 },
    { header: 'Product', key: 'product', width: 20 },
    { header: 'Provider', key: 'provider', width: 20 },
    { header: '
```

## `format.ts`

```ts
// /utils/format.ts

// Funkcija za formatiranje broja kao valute
export function formatCurrency(amount: number | null | undefined, currency: string = 'EUR', locale: string = 'en-US'): string {
  // Proveravamo da li je vrednost validan broj
  const numericAmount = typeof amount === 'number' ? amount : 0;

  // Koristimo Intl.NumberFormat za robustno formatiranje valute
  try {
    return new Intl.NumberFormat(locale, {
      style: 'currency',
      currency: currency,
      // Podesite broj decimala po potrebi
      minimumFractionDigits: 0,
      maximumFractionDigits: 2, 
    }).format(numericAmount);
  } catch (error) {
    console.error("Greška pri formatiranju valute:", error);
    // Fallback na jednostavan format u slučaju greške
    return `${numericAmount.toFixed(2)} ${currency}`;
  }
}

// Ako imate druge korisne funkcije, izvezite ih ovde
// export function formatDate(date: Date) { ... }
```

## `import-processor.ts`

```ts
// /utils/import-processor.ts
import { parse as csvParse } from 'csv-parse/sync';
import { PrismaClient, Complaint, ComplaintStatus, User } from '@prisma/client';
import { ComplaintImportData, parseAndValidateCsv, ComplaintImportSchema, validateServiceIds, validateProductIds, validateProviderIds, validateUserIds } from './csv-validator';

export type ImportResult = {
  success: boolean;
  message: string;
  importedCount: number;
  errors: Array<{ row: number; message: string }>;
  complaints?: Complaint[];
};

/**
 * Process a CSV file and import valid complaints
 */
export async function processComplaintImport(
  csvContent: string,
  submittedBy: User,
  db: PrismaClient,
  options = { hasHeaderRow: true }
): Promise<ImportResult> {
  try {
    // Parse and validate CSV data
    const validationResult = parseAndValidateCsv(csvContent, ComplaintImportSchema, options);
    
    if (!validationResult.isValid) {
      return {
        success: false,
        message: 'CSV validation fai
```

## `theme-script.ts`

```ts
// utils/theme-script.ts
export const themeScript = `
(function() {
  try {
    const theme = localStorage.getItem('theme') || 'light';
    const customThemes = JSON.parse(localStorage.getItem('customThemes') || '[]');
    const root = document.documentElement;
    
    // Add transition class to prevent jarring changes
    root.classList.add('theme-transitioning');
    
    // Clear any existing theme classes
    root.classList.remove('light', 'dark', 'custom-theme');
    
    // Apply theme immediately to prevent FOUC
    if (theme === 'dark') {
      root.style.colorScheme = 'dark';
      root.classList.add('dark');
    } else if (theme === 'light') {
      root.style.colorScheme = 'light';
      root.classList.add('light');
    } else {
      // Handle custom themes
      const customTheme = customThemes.find(t => t.id === theme);
      if (customTheme?.colors) {
        root.classList.add('custom-theme');
        root.style.colorScheme = customTheme.isDark ? 'dark' : 'light';
    
```


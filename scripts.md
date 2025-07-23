# Sekcija: scripts

## `getOrCreateContract.tsx`

```ts
private async getOrCreateContract(
  providerId: string,
  filename: string
): Promise<{ contract: Contract }> {
  const contractType = this.detectContractType(filename);
  const contractName = this.extractContractName(filename);
  const contractNumberPrefix = `VAS-${contractType}-`;

  const existingContract = await prisma.contract.findFirst({
    where: {
      providerId,
      contractNumber: { startsWith: contractNumberPrefix },
      status: 'ACTIVE'
    },
    orderBy: { createdAt: 'desc' }
  });

  if (existingContract) return { contract: existingContract };

  const newContract = await prisma.contract.create({
    data: {
      name: contractName,
      contractNumber: `${contractNumberPrefix}${Date.now().toString().slice(-6)}`,
      type: 'PROVIDER',
      status: 'ACTIVE',
      startDate: new Date(),
      endDate: new Date(new Date().setFullYear(new Date().getFullYear() + 1)),
      providerId,
      createdById: this.currentUserId,
      revenuePercentage: 0,
      descr
```

## `ParkingServiceProcessor.tsx`

```ts
// Path: scripts/vas-import/ParkingServiceProcessor.tsx
import { PrismaClient } from '@prisma/client';
import { promises as fs } from 'fs';
import path from 'path';
import { ExcelProcessor, ParkingRecord, FileProcessResult, ProgressCallback } from './processors/ExcelProcessor';
import {
  ensureDirectories,
  normalizeProviderName,
  extractServiceCode,
  createParkingServiceDirectory
} from './utils/ParkingImportUtils';

const prisma = new PrismaClient();

const PROJECT_ROOT = process.cwd();
const FOLDER_PATH = path.join(PROJECT_ROOT, 'scripts', 'input');
const PROCESSED_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'processed');
const ERROR_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'errors');

interface DatabaseOperationResult {
  inserted: number;
  updated: number;
  errors: number;
  duplicates: number;
}

export class ParkingServiceProcessor {
  private currentUserId: string;
  private progressCallback?: ProgressCallback;
  private serviceCache: Map<string, string> = new Map();
 
```

## `PostpaidServiceProcessor.ts`

```ts
// Path: scripts/vas-import/PostpaidServiceProcessor.ts
import { PrismaClient } from '@prisma/client';
import { promises as fs } from 'fs';
import path from 'path';
import { 
  PostpaidExcelProcessor, 
  VasServiceRecord, 
  VasServiceFileProcessResult, 
  VasServiceProgressCallback 
} from './processors/PostpaidExcelProcessor';
import {
  ensureDirectories,
  normalizeProviderName
} from './utils/ParkingImportUtils';

const prisma = new PrismaClient();
const PROJECT_ROOT = process.cwd();
const FOLDER_PATH = path.join(PROJECT_ROOT, 'scripts', 'input-vas-services');
const PROCESSED_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'processed-vas-services');
const ERROR_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'errors-vas-services');

interface VasServiceDatabaseOperationResult {
  inserted: number;
  updated: number;
  errors: number;
  duplicates: number;
}

export class PostpaidServiceProcessor {
  private currentUserId: string;
  private progressCallback?: VasServiceProgressCallback;
  
```

## `VasImportService.tsx`

```ts
//scripts/vas-import/VasImportService.tsx
import { PrismaClient, ServiceType, BillingType, ContractType, ContractStatus } from '@prisma/client';
import * as XLSX from 'xlsx';
import * as fs from 'fs/promises';
import path from 'path';
import { glob } from 'glob';
import { parse as parseCSV } from 'csv-parse';

const prisma = new PrismaClient();

const PROJECT_ROOT = process.cwd();
const FOLDER_PATH = path.join(PROJECT_ROOT, 'scripts', 'input');
const PROCESSED_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'processed');
const ERROR_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'errors');
const OUTPUT_FILE = path.join(PROJECT_ROOT, 'scripts', 'data', 'vas_output.csv');

interface VasRecord {
  providerId: string;
  serviceId: string;
  group: string;
  serviceName: string;
  serviceCode: string;
  price: number;
  date: string;
  quantity: number;
  amount: number;
}

interface FileProcessResult {
  records: VasRecord[];
  providerId: string;
  providerName: string;
  filename: string;
  use
```

## `ExcelProcessor.tsx`

```ts
// Path: scripts/vas-import/processors/ExcelProcessor.tsx
import * as XLSX from 'xlsx';
import { promises as fs } from 'fs';
import path from 'path';

interface ParkingRecord {
  parkingServiceId: string;
  serviceId: string;
  date: Date;
  group: string;
  serviceName: string;
  price: number;
  quantity: number;
  amount: number;
}

interface ProcessedSheet {
  name: string;
  records: ParkingRecord[];
  warnings: string[];
}

interface FileProcessResult {
  records: ParkingRecord[];
  parkingServiceId: string;
  providerName: string;
  filename: string;
  userId: string;
  sheetsProcessed: ProcessedSheet[];
  warnings: string[];
}

interface DateComponents {
  year: string;
  month: string;
  day: string;
}

interface ProviderPattern {
  pattern: RegExp;
  name: string;
  transform?: (match: string) => string;
}

interface ProgressCallback {
  onLog?: (message: string, type: 'info' | 'error' | 'success', file?: string) => void;
  onProgress?: (fileName: string, progress: number) =>
```

## `PostpaidExcelProcessor.ts`

```ts
// Path: scripts/vas-import/processors/PostpaidExcelProcessor.ts
import * as XLSX from 'xlsx';
import { promises as fs } from 'fs';
import path from 'path';

interface VasServiceRecord {
  // Mapiranje na VasService model
  proizvod: string;                          // proizvod
  mesec_pruzanja_usluge: Date;              // mesec_pruzanja_usluge (DateTime)
  jedinicna_cena: number;                   // jedinicna_cena
  broj_transakcija: number;                 // broj_transakcija
  fakturisan_iznos: number;                 // fakturisan_iznos
  fakturisan_korigovan_iznos: number;       // fakturisan_korigovan_iznos
  naplacen_iznos: number;                   // naplacen_iznos
  kumulativ_naplacenih_iznosa: number;      // kumulativ_naplacenih_iznosa
  nenaplacen_iznos: number;                 // nenaplacen_iznos
  nenaplacen_korigovan_iznos: number;       // nenaplacen_korigovan_iznos
  storniran_iznos: number;                  // storniran_iznos
  otkazan_iznos: number;               
```

## `ParkingImportUtils.ts`

```ts
// scripts/vas-import/utils/ParkingImportUtils.ts
import { PrismaClient } from '@prisma/client';
import { promises as fs } from 'fs';
import path from 'path';
import { ParkingRecord, ProgressCallback } from '../processors/ExcelProcessor';

const prisma = new PrismaClient();

const PROJECT_ROOT = process.cwd();
const FOLDER_PATH = path.join(PROJECT_ROOT, 'scripts', 'input');
const PROCESSED_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'processed');
const ERROR_FOLDER = path.join(PROJECT_ROOT, 'scripts', 'errors');

interface DatabaseOperationResult {
  inserted: number;
  updated: number;
  errors: number;
  duplicates: number;
}

interface ServiceMappingResult {
  id: string;
  created: boolean;
}

// 1. Directory Management
export async function ensureDirectories(): Promise<void> {
  const dirs = [FOLDER_PATH, PROCESSED_FOLDER, ERROR_FOLDER];
  for (const dir of dirs) {
    try {
      await fs.mkdir(dir, { recursive: true });
    } catch (error) {
      log(`Error creating directory $
```


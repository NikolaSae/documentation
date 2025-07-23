# Sekcija: hooks

## `use-bulk-services.ts`

```ts
///hooks/use-bulk-services.ts


"use client";

import { useState, useEffect, useCallback, useTransition } from "react";
import { useRouter, useSearchParams } from "next/navigation";
import { toast } from "sonner";
import { BulkService } from "@prisma/client";
import { BulkServiceWithRelations, BulkServiceFilters } from "@/lib/types/bulk-service-types";

// API functions for bulk services
const API = {
  getAll: async (filters?: BulkServiceFilters): Promise<BulkServiceWithRelations[]> => {
    const params = new URLSearchParams();
    
    if (filters) {
      if (filters.providerId) params.append("providerId", filters.providerId);
      if (filters.serviceId) params.append("serviceId", filters.serviceId);
      if (filters.providerName) params.append("providerName", filters.providerName);
      if (filters.serviceName) params.append("serviceName", filters.serviceName);
      if (filters.senderName) params.append("senderName", filters.senderName);
      if (filters.startDate) params.app
```

## `use-check-blacklisted-senders.ts`

```ts
// hooks/use-check-blacklisted-senders.ts
"use client";

import { useState, useCallback } from "react";
import { checkBlacklistedSenders } from "@/actions/blacklist/check-blacklisted-senders";
import { toast } from "sonner";

export function useCheckBlacklistedSenders() {
  const [matchedSenders, setMatchedSenders] = useState<any[]>([]);
  const [totalMatches, setTotalMatches] = useState(0);
  const [isChecking, setIsChecking] = useState(false);

  const checkSenders = useCallback(async () => {
    setIsChecking(true);
    
    try {
      const result = await checkBlacklistedSenders();
      
      if (result.success) {
        setMatchedSenders(result.matches || []);
        
        // Calculate total matches
        const total = result.matches?.reduce(
          (sum, match) => sum + (match.matchingServices?.length || 0),
          0
        ) || 0;
        
        setTotalMatches(total);
        
        if (total > 0) {
          toast.warning(
            `Found ${total} black
```

## `use-complaint-analytics.ts`

```ts
// hooks/use-complaint-analytics.ts
import { useState, useEffect, useCallback } from 'react';
import { DataFilterOptions } from '@/components/analytics/DataFilters'; // Assuming this path is correct
import { getComplaintStats } from '@/actions/analytics/get-complaint-stats'; // Assuming this action exists and accepts filters

// Define the type for the complaint data returned by getComplaintStats
// Replace with the actual type definition if you have one
interface ComplaintStatsData {
  totalComplaints: number;
  resolvedComplaints: number;
  openComplaints: number;
  averageResolutionTime: number; // in hours or days
  complaintsByStatus: { status: string; count: number }[];
  complaintsByType: { type: string; count: number }[];
  complaintsOverTime: { date: string; count: number }[]; // e.g., { date: '2023-10-01', count: 15 }
}

interface UseComplaintAnalyticsResult {
  data: ComplaintStatsData | null;
  loading: boolean;
  error: Error | null;
  refetch: () => void; // Optional: Add
```

## `use-complaints.ts`

```ts
// Path: hooks/use-complaints.ts
import { useState, useEffect, useCallback } from "react";
import { ComplaintStatus } from "@/lib/types/enums";
import { Complaint, ComplaintWithRelations } from "@/lib/types/complaint-types";

interface UseComplaintsParams {
  id?: string;
  status?: ComplaintStatus | string | null;
  serviceId?: string | null;
  providerId?: string | null;
  productId?: string | null;
  startDate?: Date | string | null;
  endDate?: Date | string | null;
  search?: string | null;
  limit?: number;
  page?: number;
}

interface UseComplaintsResult {
  complaints: ComplaintWithRelations[] | null; // Može biti null dok se učitava
  complaint: ComplaintWithRelations | null;
  isLoading: boolean;
  error: Error | null;
  totalCount: number;
  totalPages: number;
  refresh: () => Promise<void>;
}

export function useComplaints(params: UseComplaintsParams = {}): UseComplaintsResult {
  // Inicijalizujemo complaints kao null pre prvog dohvatanja
  const [complaints, setComplain
```

## `use-contract-analytics.ts`

```ts

```

## `use-contract-reminders.ts`

```ts
// /hooks/use-contract-reminders.ts
'use client';

import { useState, useEffect, useCallback } from 'react';
import { ContractReminder, User } from '@prisma/client'; // Prisma modeli
import { acknowledgeContractReminder } from '@/actions/contracts/acknowledge-reminder'; // Akcija za pregled podsetnika

// Tip za podsetnik sa uključenim acknowledgedBy korisnikom
interface ContractReminderWithUser extends ContractReminder {
    acknowledgedBy: { id: string; name: string | null; } | null;
}

interface UseContractRemindersResult {
  reminders: ContractReminderWithUser[];
  loading: boolean;
  error: Error | null;
  refreshReminders: () => void; // Funkcija za ručno osvežavanje liste podsetnika
  acknowledgeReminder: (reminderId: string) => Promise<{ success?: string; error?: string }>; // Funkcija za pregled podsetnika
}

/**
 * Hook za dohvatanje i upravljanje podsetnicima za specifičan ugovor.
 * @param contractId - ID ugovora za koji se dohvataju podsetnici. Može biti null ili undefined
```

## `use-contracts.ts`

```ts
// hooks/use-contracts.ts

"use client";

import { useState, useEffect, useCallback } from "react";
import { ContractStatus, ContractType } from "@prisma/client";

interface Provider {
  id: string;
  name: string;
}

interface HumanitarianOrg {
  id: string;
  name: string;
}

interface ParkingService {
  id: string;
  name: string;
}

interface Contract {
  id: string;
  name: string;
  contractNumber: string;
  type: ContractType;
  status: ContractStatus;
  startDate: Date;
  endDate: Date;
  revenuePercentage: number;
  description?: string | null;
  provider?: Provider | null;
  humanitarianOrg?: HumanitarianOrg | null;
  parkingService?: ParkingService | null;
  createdAt: Date;
  updatedAt: Date;
  createdBy: {
    id: string;
    name: string | null;
  };
  lastModifiedBy?: {
    id: string;
    name: string | null;
  } | null;
  _count?: {
    services: number;
    attachments: number;
    reminders: number;
  };
}

interface FilterOptions {
  type?: ContractType | null;
  st
```

## `use-current-role.ts`

```ts
import { useSession } from "next-auth/react";

export const useCurrentRole = () => {
  const session = useSession();

  return session.data?.user.role;
};

```

## `use-current-user.ts`

```ts
import { useSession } from "next-auth/react";

export const useCurrentUser = () => {
  const session = useSession();

  return session.data?.user;
};

```

## `useDebounce.ts`

```ts
///hooks/useDebounce.ts


import { useState, useEffect } from 'react';

export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}
```

## `use-expiring-contracts.ts`

```ts
///hooks/use-expiring-contracts.ts
"use client";

import { useState, useEffect } from "react";
import { Contract, ContractStatus } from "@prisma/client";

export interface ExpiringContractWithDetails extends Contract {
  provider?: { name: string } | null;
  humanitarianOrg?: { name: string } | null;
  parkingService?: { name: string } | null;
  _count: {
    reminders: number;
  };
}

export interface UseExpiringContractsOptions {
  days?: number;
  refreshInterval?: number;
  status?: ContractStatus;
}

export function useExpiringContracts(options: UseExpiringContractsOptions = {}) {
  const { 
    days = 30,
    refreshInterval = 0
  } = options;
  
  const [contracts, setContracts] = useState<ExpiringContractWithDetails[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  const fetchExpiringContracts = async () => {
    console.log(`Fetching expiring contracts (days=${days})`);
    
    try {
      setIsLoading(tru
```

## `use-humanitarian-org-contracts.ts`

```ts
// /hooks/use-humanitarian-org-contracts.ts
'use client';

import { useState, useEffect, useCallback } from 'react';
// Uvozimo custom tip za ugovore sa uključenim obnovama (i potencijalno operatorom, itd. kao u API ruti)
// Prilagodite ovaj tip da odgovara strukturi koju vraća vaša API ruta include klauzula
interface ContractWithDetails {
  id: string;
  name: string;
  contractNumber: string;
  status: "ACTIVE" | "EXPIRED" | "PENDING" | "RENEWAL_IN_PROGRESS" | "TERMINATED"; // Dodat TERMINATED status
  startDate: string | Date;
  endDate: string | Date;
  revenuePercentage: number;
  type: "HUMANITARIAN";
  // Dodajte ovde polja koja su uključena u API ruti, npr:
  // humanitarianRenewals: Array<any>; // Ako su uključene obnove
  operator?: { // Ako je uključen operator
     id: string;
     name: string;
  } | null;
}


interface UseHumanitarianOrgContractsResult {
  contracts: ContractWithDetails[]; // Lista ugovora
  isLoading: boolean; // Status učitavanja
  error: string | null;
```

## `use-humanitarian-orgs.ts`

```ts
// Path: hooks/use-humanitarian-orgs.ts
"use client";

import { useState, useEffect, useCallback } from "react";
import {
  HumanitarianOrgWithDetails,
  HumanitarianOrgFilterOptions,
  PaginationOptions
} from "@/lib/types/humanitarian-org-types";
import { getHumanitarianOrgs } from "@/actions/humanitarian-orgs/get";

export function useHumanitarianOrgs(
  filters: HumanitarianOrgFilterOptions = {},
  pagination: PaginationOptions = { page: 1, limit: 10 }
) {
  // Data state
  const [humanitarianOrgs, setHumanitarianOrgs] = useState<HumanitarianOrgWithDetails[]>([]);
  const [totalCount, setTotalCount] = useState<number>(0);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<Error | null>(null);
  
  // Create a stable reference for comparing objects
  const filterKey = JSON.stringify(filters);
  const paginationKey = JSON.stringify(pagination);
  
  // Fetch organizations based on current filters and pagination
  const fetchOrgs = useCallbac
```

## `use-humanitarian-renewals.ts`

```ts
// /hooks/use-humanitarian-renewals.ts
import { useState, useEffect, useCallback } from 'react';
import { toast } from 'sonner';
import {
  HumanitarianRenewalWithRelations,
  HumanitarianRenewalsList,
  RenewalStatistics,
  RenewalFilters
} from '@/lib/types/humanitarian-renewal-types';
import {
  CreateHumanitarianRenewalInput,
  UpdateHumanitarianRenewalInput
} from '@/schemas/humanitarian-renewal';

interface UseHumanitarianRenewalsOptions {
  initialFilters?: Partial<RenewalFilters>;
  autoFetch?: boolean;
}

export function useHumanitarianRenewals(options: UseHumanitarianRenewalsOptions = {}) {
  const { initialFilters = {}, autoFetch = true } = options;

  const [renewals, setRenewals] = useState<HumanitarianRenewalWithRelations[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const [pagination, setPagination] = useState({
    page: 1,
    limit: 10,
    total: 0,
    totalPages: 0
  });
  const [filters, setFi
```

## `use-operators.ts`

```ts
// hooks/use-operators.ts

import { useState, useEffect } from "react";
import { useToast } from "@/components/ui/use-toast";
import { OperatorWithContractCount, OperatorFilters, OperatorPaginationParams } from "@/lib/types/operator-types";

interface UseOperatorsOptions {
  initialPage?: number;
  initialLimit?: number;
  initialFilters?: OperatorFilters;
  fetchOnMount?: boolean;
}

interface OperatorsResponse {
  data: OperatorWithContractCount[];
  meta: {
    total: number;
    page: number;
    limit: number;
    pages: number;
  };
}

export function useOperators(options: UseOperatorsOptions = {}) {
  const {
    initialPage = 1,
    initialLimit = 10,
    initialFilters = {},
    fetchOnMount = true,
  } = options;

  const [operators, setOperators] = useState<OperatorWithContractCount[]>([]);
  const [loading, setLoading] = useState<boolean>(false);
  const [error, setError] = useState<string | null>(null);
  const [pagination, setPagination] = useState({
    page: initialPage
```

## `use-parking-service-contracts.ts`

```ts
//hooks/use-parking-service-contracts.ts

import { useState, useEffect } from "react";
import { toast } from "sonner";
import { Contract } from "@prisma/client";

/**
 * Custom hook for managing contracts associated with a parking service
 */
export function useParkingServiceContracts(parkingServiceId: string | null) {
  const [contracts, setContracts] = useState<Contract[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const fetchContracts = async () => {
    if (!parkingServiceId) return;

    setIsLoading(true);
    setError(null);

    try {
      const response = await fetch(`/api/parking-services/${parkingServiceId}/contracts`);
      
      if (!response.ok) {
        throw new Error(`Error fetching contracts: ${response.statusText}`);
      }
      
      const data = await response.json();
      setContracts(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : "Failed to load co
```

## `use-parking-services.ts`

```ts
//hooks/use-parking-services.ts

"use client";

import { useState, useCallback } from "react";
import { useRouter } from "next/navigation";
import { toast } from "sonner";
import { 
  getAllParkingServices, 
  getParkingServices, 
  getParkingServiceById,
  create,
  update,
  deleteService
} from "@/actions/parking-services";
import { 
  ParkingServiceFilters, 
  CreateParkingServiceParams,
  UpdateParkingServiceParams,
  PaginatedParkingServices
} from "@/lib/types/parking-service-types";

export function useParkingServices() {
  const router = useRouter();
  const [isLoading, setIsLoading] = useState(false);
  const [paginatedData, setPaginatedData] = useState<PaginatedParkingServices | null>(null);

  // Fetch all parking services without pagination
  const fetchAll = useCallback(async () => {
    setIsLoading(true);
    try {
      const response = await getAllParkingServices();
      setIsLoading(false);
      
      if (!response.success) {
        toast.error(response.error || 
```

## `use-performance-metrics.ts`

```ts
// hooks/use-performance-metrics.ts
"use client";

import { useState, useEffect, useCallback } from "react";

// Definisanje očekivane strukture podataka iz API-ja
interface PerformanceDataPoint {
  timestamp: string; // Vreme kao string (može se konvertovati u Date po potrebi)
  responseTime: number; // Vreme odgovora API-ja u ms
  requestCount: number; // Broj zahteva
  errorRate: number; // Stopa grešaka (verovatno decimalna vrednost 0-1 ili procenat)
  cpuUsage: number | null; // Korišćenje CPU-a (npr. procenat)
  memoryUsage: number | null; // Korišćenje memorije (npr. u MB ili GB)
}

// Definisanje dostupnih vremenskih opsega
export type TimeRange = "1h" | "6h" | "24h" | "7d" | "30d" | "all";

interface UsePerformanceMetricsResult {
  data: PerformanceDataPoint[] | null; // Dohvaćeni podaci
  isLoading: boolean; // Stanje učitavanja
  error: string | null; // Poruka o grešci
  refresh: () => void; // Funkcija za ručno osvežavanje podataka
}

// Hook za dohvatanje metrika performa
```

## `use-products.ts`

```ts
// /hooks/use-product.ts
'use client';

import { useState, useEffect, useCallback } from 'react';
// Uvozimo ažurirani tip za Proizvod sa detaljima (usklađen sa schema.prisma)
import { ProductWithDetails } from '@/lib/types/product-types';
// Uvozimo server akciju za dohvatanje pojedinačnog proizvoda (AŽURIRANU)
import { getProductById } from '@/actions/products/get'; // Ova akcija je ažurirana u 5.3

interface UseProductResult {
  product: ProductWithDetails | null;
  loading: boolean;
  error: Error | null;
  // refresh: () => void; // Opciono: funkcija za osvežavanje podataka
}

/**
 * Hook za dohvatanje pojedinačnog proizvoda po ID-u.
 * Koristi AŽURIRANU Server Akciju actions/products/get.ts (getProductById).
 * Usklađen sa Product modelom u schema.prisma i ažuriranim tipovima.
 * @param productId - ID proizvoda koji se dohvaća. Može biti null ili undefined pre nego što je ID poznat.
 * @returns Objekat sa podacima o proizvodu, statusom učitavanja i greškom.
 */
export function us
```

## `use-products-by-service.ts`

```ts
// hooks/use-products-by-service.ts

import { useState, useEffect } from 'react';
import { getProductsByService } from '@/actions/products/get-by-service';
import { Product } from '@/lib/types/interfaces';

interface UseProductsByServiceResult {
  products: Product[];
  isLoading: boolean;
  error: Error | null;
  mutate: () => Promise<void>;
}

export function useProductsByService(serviceId?: string): UseProductsByServiceResult {
  const [products, setProducts] = useState<Product[]>([]);
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [error, setError] = useState<Error | null>(null);

  const fetchProducts = async () => {
    if (!serviceId) {
      setProducts([]);
      return;
    }

    try {
      setIsLoading(true);
      setError(null);
      const data = await getProductsByService({ serviceId });
      setProducts(data);
    } catch (err) {
      console.error('Error fetching products for service:', err);
      setError(err instanceof Error ? err : new Er
```

## `use-provider-contracts.ts`

```ts
// /hooks/use-provider-contracts.ts
"use client";

import { useState, useEffect, useCallback } from "react";
import { ProviderContractItem } from "@/components/providers/ProviderContracts"; // Import the type from the component

interface UseProviderContractsResult {
  contracts: ProviderContractItem[] | null;
  isLoading: boolean;
  error: string | null;
  refreshContracts: () => Promise<void>;
}

export function useProviderContracts(providerId: string): UseProviderContractsResult {
  const [contracts, setContracts] = useState<ProviderContractItem[] | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  // useCallback memoizes the fetch function to prevent unnecessary re-creations
  const fetchContracts = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    try {
      // *** IMPORTANT: You need to create this API route handler ***
      // This route should fetch contracts for the given pro
```

## `use-provider-logs.ts`

```ts
// /hooks/use-provider-logs.ts
"use client";

import { useState, useEffect, useCallback } from "react";
import { LogEntryItem } from "@/components/providers/ProviderLogList";
import { LogEntityType, LogActionType, LogStatus } from "@prisma/client";

import { getLogEntries, GetLogEntriesInput } from "@/actions/log/getLogEntries";

interface PaginationState {
    page: number;
    limit: number;
}

interface LogFiltersState {
    action?: LogActionType | 'ALL';
    status?: LogStatus | 'ALL';
    subjectKeyword?: string;
    dateFrom?: Date;
    dateTo?: Date;
}

interface UseProviderLogsParams {
    filters: LogFiltersState;
    pagination: PaginationState;
    logRefreshKey: number;
}

interface UseProviderLogsResult {
    logs: LogEntryItem[] | null;
    total: number;
    loading: boolean;
    error: string | null;
    refreshData: () => void;
}

export function useProviderLogs({ filters, pagination, logRefreshKey }: UseProviderLogsParams): UseProviderLogsResult {
  const [logs, setL
```

## `use-providers.ts`

```ts
// hooks/use-providers.ts
"use client";

import { useState, useEffect, useCallback, useRef } from "react";

export interface Provider {
  id: string;
  name: string;
  contactName?: string | null;
  email?: string | null;
  phone?: string | null;
  isActive: boolean;
  imageUrl?: string | null;
  _count?: {
    contracts?: number;
    complaints?: number;
    vasServices?: number;
    bulkServices?: number;
  };
}

export interface PaginationOptions {
  page: number;
  limit: number;
}

export interface FilterOptions {
  search?: string;
  isActive?: boolean;
  hasContracts?: boolean;
  hasComplaints?: boolean;
  sortBy?: string;
  sortDirection?: "asc" | "desc";
}

export function useProviders(
  initialFilters: FilterOptions = {},
  initialPagination: PaginationOptions = { page: 1, limit: 12 }
) {
  const [providers, setProviders] = useState<Provider[]>([]);
  const [total, setTotal] = useState(0);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<s
```

## `use-providers-by-service.ts`

```ts
// hooks/use-providers-by-service.ts

import { useState, useEffect } from 'react';
import { getProvidersByService } from '@/actions/providers/get-by-service';
import { Provider } from '@/lib/types/interfaces';

interface UseProvidersByServiceResult {
  providers: Provider[];
  isLoading: boolean;
  error: Error | null;
  mutate: () => Promise<void>;
}

export function useProvidersByService(serviceId?: string): UseProvidersByServiceResult {
  const [providers, setProviders] = useState<Provider[]>([]);
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [error, setError] = useState<Error | null>(null);

  const fetchProviders = async () => {
    if (!serviceId) {
      setProviders([]);
      return;
    }

    try {
      setIsLoading(true);
      setError(null);
      const data = await getProvidersByService({ serviceId });
      setProviders(data);
    } catch (err) {
      console.error('Error fetching providers for service:', err);
      setError(err instanceof Err
```

## `use-revenue-breakdown.ts`

```ts
// hooks/use-revenue-breakdown.ts
"use client";

import { useState, useEffect } from "react";
import { getRevenueBreakdown } from "@/lib/analytics/revenue-utils";
import { ServiceType } from "@prisma/client";

type RevenueBreakdownData = {
  type: ServiceType;
  value: number;
}[];

export function useRevenueBreakdown() {
  const [data, setData] = useState<RevenueBreakdownData | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setIsLoading(true);
        const result = await getRevenueBreakdown();
        setData(result);
        setError(null);
      } catch (err) {
        setError(err instanceof Error ? err : new Error("Failed to fetch revenue breakdown"));
      } finally {
        setIsLoading(false);
      }
    };

    fetchData();
  }, []);

  return { data, isLoading, error };
}
```

## `use-sales-data.ts`

```ts
// hooks/use-sales-data.ts
"use client";

import { useState, useEffect } from "react";

export type SalesPeriod = "daily" | "weekly" | "monthly" | "quarterly" | "yearly";

interface SalesDataPoint {
  date: string;
  revenue: number;
  transactions: number;
  providerName?: string;
  serviceName?: string;
  serviceType?: string;
}

interface SalesMetrics {
  totalRevenue: number;
  totalTransactions: number;
  averageTransactionValue: number;
  growthRate: number;
  topProviders: { name: string; revenue: number; transactions: number }[];
  topServices: { name: string; revenue: number; transactions: number }[];
}

export function useSalesData(
  period: SalesPeriod = "monthly",
  startDate?: Date,
  endDate?: Date,
  serviceType?: string,
  providerId?: string
) {
  const [salesData, setSalesData] = useState<SalesDataPoint[]>([]);
  const [metrics, setMetrics] = useState<SalesMetrics | null>(null);
  const [isLoading, setIsLoading] = useState<boolean>(true);
  const [error, setError] = 
```

## `use-sender-blacklist.ts`

```ts
// hooks/use-sender-blacklist.ts
// hooks/use-sender-blacklist.ts
import { useState, useEffect, useCallback } from "react";
import { SenderBlacklistWithProvider } from "@/lib/types/blacklist";

export interface BlacklistPagination {
  page: number;
  pageSize: number;
  total: number;
  totalPages: number;
}

export interface BlacklistFilters {
  senderName?: string;
  providerId?: string;
  isActive?: boolean;
  dateFrom?: Date;
  dateTo?: Date;
}

export function useSenderBlacklist() {
  const [entries, setEntries] = useState<SenderBlacklistWithProvider[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const [pagination, setPagination] = useState<BlacklistPagination>({
    page: 1,
    pageSize: 10,
    total: 0,
    totalPages: 1,
  });
  const [filters, setFilters] = useState<BlacklistFilters>({});
  const [refreshKey, setRefreshKey] = useState(0);

  // Add refresh function
  const handleRefresh = useCallback(
```

## `use-service-categories.ts`

```ts
// hooks/use-service-categories.ts
import { useState, useEffect } from "react";
import { ServiceType } from "@/lib/types/enums";
import { Service } from "@/lib/types/interfaces";

interface UseServiceCategoriesResult {
  categories: ServiceType[];
  services: Service[];
  isLoading: boolean;
  error: string | null;
}

export function useServiceCategories(): UseServiceCategoriesResult {
  const [categories, setCategories] = useState<ServiceType[]>([]);
  const [services, setServices] = useState<Service[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchServiceCategories = async () => {
      try {
        setIsLoading(true);
        setError(null);
        
        const response = await fetch("/api/services/categories", {
          credentials: 'include' // Ensure cookies are sent
        });
        
        if (!response.ok) {
          if (response.status === 401) {
            th
```

## `use-service-performance.ts`

```ts
// hooks/use-service-performance.ts
"use client";

import { useState, useEffect } from "react";
import { Service, VasService, BulkService } from "@prisma/client";

// Define the return type for service performance data
type ServicePerformanceData = {
  serviceId: string;
  serviceName: string;
  serviceType: string;
  transactionCount: number;
  revenue: number;
  requests?: number;
  messageParts?: number;
  conversion?: number; // For VAS services: naplacen_iznos / fakturisan_iznos
  growth?: number; // Percentage growth from previous period
  serviceData: VasService[] | BulkService[];
};

export function useServicePerformance(
  period: "month" | "quarter" | "year" = "month",
  serviceType?: "VAS" | "BULK" | "HUMANITARIAN" | "PARKING",
  startDate?: Date,
  endDate?: Date
) {
  const [performanceData, setPerformanceData] = useState<ServicePerformanceData[]>([]);
  const [isLoading, setIsLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

 
```

## `use-services.ts`

```ts
// /hooks/use-services.ts
'use client';

import { useState, useEffect, useCallback } from 'react';
// Uvozimo ažurirane tipove (ServiceFilterOptions sada koristi 'type')
import { ServiceWithDetails, ServiceFilterOptions, ServicesApiResponse } from '@/lib/types/service-types';
// Uvozimo server akciju za dohvatanje servisa (AŽURIRANU)
import { getServices } from '@/actions/services/get';

interface UseServicesResult {
  services: ServiceWithDetails[];
  totalCount: number;
  loading: boolean;
  error: Error | null;
  filters: ServiceFilterOptions; // Izlažemo trenutne filtere
  pagination: { page: number; limit: number }; // Izlažemo trenutnu paginaciju
   setFilters: (filters: ServiceFilterOptions) => void; // Funkcija za ažuriranje filtera
   setPagination: (pagination: { page: number; limit: number }) => void; // Funkcija za ažuriranje paginacije
   refresh: () => void; // Funkcija za ručno osvežavanje
}

/**
 * Hook za dohvatanje, filtriranje i paginaciju liste servisa/usluga.
 * Ko
```

## `use-service-stats.ts`

```ts
// /hooks/use-service-stats.ts
'use client';

import { useState, useEffect, useCallback } from 'react';
// Uvozimo funkcije za statistiku iz lib foldera (nadamo se da su usklađene sa schema.prisma)
import { getServiceRevenueSummary, getProductComplaintStats } from '@/lib/services/statistics'; // Funkcije koje treba da budu usklađene
// Uvozimo tipove ako su potrebni (npr. za strukturu statistika)
// import { ServiceStatistics, RevenueSummary, ComplaintCostSummary } from '@/lib/services/statistics'; // Ako su tamo definisani tipovi rezultata

// Definišite tipove za statističke podatke koje ovaj hook treba da vrati
// Usklađeno sa onim što getServiceRevenueSummary i getProductComplaintStats vraćaju i vašom šemom
interface RevenueSummary {
    id: string;
    name: string;
    totalRevenue: number;
    contractsCount: number;
}

interface ComplaintCostSummary {
    id: string;
    name: string;
    averageCost: number;
    totalComplaints: number;
}

interface ServiceStatistics {
    rev
```

## `use-toast.ts`

```ts
// hooks/use-toast.ts

"use client"

// Inspired by react-hot-toast library
import * as React from "react"

import type {
  ToastActionElement,
  ToastProps,
} from "@/components/ui/toast"

const TOAST_LIMIT = 1
const TOAST_REMOVE_DELAY = 1000000

type ToasterToast = ToastProps & {
  id: string
  title?: React.ReactNode
  description?: React.ReactNode
  action?: ToastActionElement
}

const actionTypes = {
  ADD_TOAST: "ADD_TOAST",
  UPDATE_TOAST: "UPDATE_TOAST",
  DISMISS_TOAST: "DISMISS_TOAST",
  REMOVE_TOAST: "REMOVE_TOAST",
} as const

let count = 0

function genId() {
  count = (count + 1) % Number.MAX_SAFE_INTEGER
  return count.toString()
}

type ActionType = typeof actionTypes

type Action =
  | {
      type: ActionType["ADD_TOAST"]
      toast: ToasterToast
    }
  | {
      type: ActionType["UPDATE_TOAST"]
      toast: Partial<ToasterToast>
    }
  | {
      type: ActionType["DISMISS_TOAST"]
      toastId?: ToasterToast["id"]
    }
  | {
      type: ActionType["REMOVE_TOAST"]

```


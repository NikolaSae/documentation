# Sekcija: app

## `layout.tsx`

```ts
// app/layout.tsx - Updated version
import type { Metadata } from "next";
import localFont from "next/font/local";
import React from 'react';
import { themeScript } from "@/utils/theme-script";
import "./globals.css";
import { Toaster } from "@/components/ui/sonner";

const geistSans = localFont({
  src: "./fonts/GeistVF.woff",
  variable: "--font-geist-sans",
  weight: "100 900",
});

const geistMono = localFont({
  src: "./fonts/GeistMonoVF.woff",
  variable: "--font-geist-mono",
  weight: "100 900",
});

export const metadata: Metadata = {
  title: "Fin-app-Hub",
  description: "Finansijski izvestaji, vas servisi, reklamacije, parking i jos mnogo toga.",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en" suppressHydrationWarning>
      <head>
        <script dangerouslySetInnerHTML={{ __html: themeScript }} />
      </head>
      <body
        className={`${geistSans.variable} ${geistMono.variable} ant
```

## `page.tsx`

```ts
// app/page.tsx
import { LoginButton } from "@/components/auth/login-button";
import { Button } from "@/components/ui/button";

export default async function Home() {
  return (
    <main className="flex h-full flex-col items-center justify-center bg-gradient-to-br from-slate-900 to-slate-800">
      <div className="space-y-6 text-center">
        <h1 className="text-6xl font-bold text-white drop-shadow-[0_4px_12px_rgba(255,255,255,0.3)]">
          Fin-App-Hub
        </h1>
        <p className="text-slate-100 text-lg font-light tracking-wide">
          Finansijski izveštaji, reklamacije, parking...
        </p>
        <div className="pt-4">
          <LoginButton mode="modal" asChild>
            <Button 
              variant={"default"} 
              size={"lg"}
              className="shadow-container hover:shadow-hover bg-white text-slate-900 hover:bg-slate-100 transition-all"
            >
              Prijava
            </Button>
          </LoginButton>
        </div>
  
```

## `layout.tsx`

```ts
// app/(protected)/layout.tsx
import { SessionProvider } from "next-auth/react";
import { auth } from "@/auth";
import { Navbar } from "./_components/navbar";
import { ThemeToggle } from "@/components/theme-toggle";
import { ThemeProvider } from "@/contexts/theme-context";
import { themeScript } from "@/utils/theme-script";
import Script from "next/script";
import React from 'react';

interface ProtectedLayoutProps {
  children: React.ReactNode;
}

export default async function ProtectedLayout({
  children,
}: ProtectedLayoutProps) {
  const session = await auth();
  
  return (
    <>
      {/* Inline theme script for immediate execution */}
      <Script
        id="theme-script"
        strategy="beforeInteractive"
        dangerouslySetInnerHTML={{ __html: themeScript }}
      />
      
      <ThemeProvider>
        <SessionProvider session={session}>
          <div className="min-h-screen flex flex-col bg-background text-foreground theme-transition">
            <header className=
```

## `navbar.tsx`

```ts
// Path: /app/(protected)/_components/navbar.tsx
"use client";

import * as React from "react";
import Link from "next/link";
import { usePathname, useRouter } from "next/navigation";
import { ChevronDown } from "lucide-react";

import { cn } from "@/lib/utils";
import { ClientSideUserButton } from "@/components/auth/client-side-user-button";

interface DropdownItem {
  href: string;
  title: string;
  description?: string;
}

interface CustomDropdownProps {
  trigger: string;
  items: DropdownItem[];
  isActive: boolean;
}

const CustomDropdown: React.FC<CustomDropdownProps> = ({ trigger, items, isActive }) => {
  const [isOpen, setIsOpen] = React.useState(false);
  const timeoutRef = React.useRef<NodeJS.Timeout>();
  const router = useRouter();

  const handleMouseEnter = () => {
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current);
    }
    setIsOpen(true);
  };

  const handleMouseLeave = () => {
    timeoutRef.current = setTimeout(() => {
      setIsOpen(false);
 
```

## `newbar.tsx`

```ts
"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";
import { Button } from "@/components/ui/button";
import { UserButton } from "@/components/auth/user-button";

export const Newbar = () => {
  const pathname = usePathname();
  return (
    <nav className="bg-secondary flex justify-between items-center p-4 rounded-xl w-[600px] shadow-sm">
      <div className="flex gap-x-2">
        <Button
          variant={pathname === "/dashboard/vas-servisi" ? "default" : "outline"}
          asChild
        >
          <Link href="/dashboard/vas-servisi">Vas-servisi</Link>
        </Button>
        <Button
          variant={pathname === "/dashboard/humanitarni" ? "default" : "outline"}
          asChild
        >
          <Link href="/dashboard/humanitarni">humanitarni</Link>
        </Button>
        <Button variant={pathname === "/dashboard/statistika" ? "default" : "outline"} asChild>
          <Link href="/dashboard/statistika">statistika</Link>
     
```

## `sidebar.tsx`

```ts
////app/(protected)/_components/sidebar.tsx


"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";
import { Button } from "@/components/ui/button";

interface SidebarProps {
  links: { href: string; label: string }[];
}

export const Sidebar = ({ links }: SidebarProps) => {
  const pathname = usePathname();

  return (
    <div className="flex flex-col p-4 space-y-2 min-h-screen w-64 border"> {/* Stilovi za sidebar */}
      {links.map(link => (
        <Button
          key={link.href}
          variant={pathname === link.href ? "default" : "ghost"}
          asChild
        >
          <Link href={link.href}>
            {link.label}
          </Link>
        </Button>
      ))}
    </div>
  );
};
```

## `page.tsx`

```ts
//app/(protected)/admin/page.tsx

"use client";

import { admin } from "@/actions/admin";
import { RoleGate } from "@/components/auth/role-gate";
import { FormSuccess } from "@/components/form-success";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader } from "@/components/ui/card";
import { UserRole } from "@prisma/client";
import { toast } from "sonner";

export default function AdminPage() {
  const onApiRouteClick = () => {
    fetch("/api/admin").then((response) => {
      if (response.ok) {
        toast.success("Allowed API Route!");
      } else {
        toast.error("Forbidden API Route!");
      }
    });
  };

  const onServerActionClick = () => {
    admin().then((data) => {
      if (data.error) {
        toast.error(data.error);
      }

      if (data.success) {
        toast.success(data.success);
      }
    });
  };
  return (
    <Card className="w-[600px]">
      <CardHeader>
        <p className="font-bold text-center">Admin</p
```

## `page.tsx`

```ts
// app/(protected)/admin/complaints/page.tsx

"use client";

import { useState, useEffect } from "react";
import { useSearchParams } from "next/navigation";
import { ComplaintList } from "@/components/complaints/ComplaintList";
import { ComplaintFilters } from "@/components/complaints/ComplaintFilters";
import { StatisticsCard } from "@/components/complaints/StatisticsCard";
import { NotificationBanner } from "@/components/complaints/NotificationBanner";
import { useComplaints } from "@/hooks/use-complaints";
import { useServiceCategories } from "@/hooks/use-service-categories";
import { Button } from "@/components/ui/button";
import { 
  Download, 
  Upload, 
  RefreshCcw, 
  AlertTriangle, 
  CheckCircle, 
  Clock 
} from "lucide-react";
import Link from "next/link";
import { CsvImport } from "@/components/complaints/CsvImport";
import { exportComplaints } from "@/actions/complaints/export";

export default function AdminComplaintsPage() {
  const searchParams = useSearchParams();
  
```

## `page.tsx`

```ts
// app/(protected)/admin/complaints/reports/page.tsx

"use client";

import { useState } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Button } from "@/components/ui/button";
import { ExcelExport } from "@/components/complaints/reports/ExcelExport";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Calendar, Download, FileText, RefreshCcw, Filter, List } from "lucide-react";
import { DateRangeFilter } from "@/components/complaints/DateRangeFilter";
import { NotificationBanner } from "@/components/complaints/NotificationBanner";
import Link from "next/link";

// Mock data for reports - in a real implementation these would come from an API
const REPORTS = [
  { id: '1', name: 'Monthly Complaint Summary', type: 'monthly', date: '2025-03-31', status: 'Ready', url: '#' },
  { id: '2', name: '
```

## `page.tsx`

```ts
// app/(protected)/admin/complaints/statistics/page.tsx


"use client";

import { useState } from "react";
import { DateRangeFilter } from "@/components/complaints/DateRangeFilter";
import { TrendChart } from "@/components/complaints/charts/TrendChart";
import { StatusChart } from "@/components/complaints/charts/StatusChart";
import { MonthlyComparisonChart } from "@/components/complaints/charts/MonthlyComparisonChart";
import { ServiceCategoryBreakdown } from "@/components/complaints/charts/ServiceCategoryBreakdown";
import { ProviderPerformance } from "@/components/complaints/charts/ProviderPerformance";
import { KpiDashboard } from "@/components/complaints/reports/KpiDashboard";
import { Button } from "@/components/ui/button";
import { Loader2, Download, Filter } from "lucide-react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import Link from "next/link";
import { useRouter } from "next/navigation";

export default function ComplaintsStatisticsP
```

## `page.tsx`

```ts
// Path: app/(protected)/admin/notifications/page.tsx

import { Suspense } from 'react';
import { getCurrentUser, isAdmin } from '@/lib/security/auth-helpers';
import AdminNotificationControls from '@/components/notifications/AdminNotificationControls';
import { redirect } from 'next/navigation';
import { Loader2 } from "lucide-react";

export const metadata = {
    title: 'Admin Notifications | Service Management',
    description: 'Administrative notification management',
};

// Definisaćemo listu svih mogućih korisničkih uloga ovde.
// PRILAGODITE OVAJ NIZ da sadrži STVARNE uloge koje imate u vašoj aplikaciji.
const ALL_USER_ROLES = ['ADMIN', 'MANAGER', 'PROVIDER', 'USER', 'AGENT']; // Primer uloga


export default async function AdminNotificationsPage() {
    const user = await getCurrentUser();
    const isAdminUser = await isAdmin();

    if (!user || !isAdminUser) {
        redirect('/auth/login');
    }

    return (
        <div className="container mx-auto py-8">
            
```

## `page.tsx`

```ts
// Path: app/(protected)/admin/security/page.tsx

import { Suspense } from 'react';
import { auth } from '@/auth';
import { redirect } from 'next/navigation';
import { Metadata } from 'next';
import Link from 'next/link';

import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import ActivityLog from '@/components/security/ActivityLog';
import PermissionGate from '@/components/security/PermissionGate';
import { Button } from '@/components/ui/button';
import { Loader2, AlertTriangle, Shield, Users, LineChart, Clock } from 'lucide-react';

import {
  getCriticalAlertMetrics,
  getWarningEventMetrics,
  getRecentActiveUserCount,
} from '@/actions/security/metrics';


export const metadata: Metadata = {
  title: 'Security Dashboard | Admin',
  description: 'Monitor and manage application security',
};

async function SecurityMetricCard({
  title,
  value,

```

## `page.tsx`

```ts
// Path: app/(protected)/admin/security/activity-logs/page.tsx

import { db } from "@/lib/db";
import { auth } from "@/auth";
import ActivityLog from "@/components/security/ActivityLog";
import { z } from "zod";

const initialLogsParamsSchema = z.object({
  page: z.coerce.number().int().positive().optional().default(1),
  limit: z.coerce.number().int().min(1).max(100).optional().default(20),
});

export default async function ActivityLogsPage({
  searchParams,
}: {
  searchParams?: { [key: string]: string | string[] | undefined };
}) {
  const session = await auth();

  if (!session?.user || (session.user.role !== "ADMIN" && session.user.role !== "MANAGER")) {
    return <div>Unauthorized</div>;
  }

  // Await searchParams before accessing properties
  const awaitedSearchParams = await searchParams;

  const initialParams = {
    page: awaitedSearchParams?.page,
    limit: awaitedSearchParams?.limit,
  };

  const validatedInitialParams = initialLogsParamsSchema.parse(initialParams);

```

## `page.tsx`

```ts
// app/(protected)/admin/security/performance/page.tsx
// Ova stranica će sada biti Client Component da bi podržala interaktivni odabir vremenskog opsega
"use client";

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
// Importujte Client komponente
import PerformanceMetrics from "@/components/security/PerformanceMetrics"; // Default import
// Pretpostavljamo da su i ove komponente Client komponente koje koriste usePerformanceMetrics ili primaju podatke
import SystemResources from "@/components/security/SystemResources";
import APIResponseTimes from "@/components/security/APIResponseTimes";
import DatabasePerformance from "@/components/security/DatabasePerformance";

// Uklonjeni server-side importi za auth i redirect
// import { auth } from "@/auth";
// import { redirect } from "next/navigation";

import { Metadata } from "next"; // Metadata se i dalje defi
```

## `page.tsx`

```ts
////app/(protected)/admin/security/user-roles/page.tsx


import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import  UserRoleManagement  from "@/components/security/UserRoleManagement";
import  RolePermissions  from "@/components/security/RolePermissions";
import { auth } from "@/auth";
import { redirect } from "next/navigation";
import  ActivityLog  from "@/components/security/ActivityLog";

export default async function UserRolesPage() {
  const session = await auth();
  
  if (!session || session.user.role !== "ADMIN") {
    redirect("/unauthorized");
  }
  
  return (
    <div className="container mx-auto py-6 space-y-6">
      <div className="flex justify-between items-center">
        <div>
          <h1 className="text-3xl font-bold tracking-tight">User Role Management</h1>
          <p className="text-muted-foreground">
            Manage user roles and
```

## `layout.tsx`

```ts
////app/(protected)/analytics/layout.tsx


import { Sidebar } from "../_components/sidebar";

interface AnalyticsLayoutProps {
  children: React.ReactNode;
}

// Definišite linkove specifične za Analytics sekciju
const analyticsLinks = [
  { href: "/analytics", label: "Overview" }, // Link ka /analytics/page.tsx
  { href: "/analytics/financials", label: "Financials" },
  { href: "/analytics/providers", label: "Providers" },
  { href: "/analytics/sales", label: "Sales" },
  { href: "/analytics/complaints", label: "Complaints" },
];

export default function AnalyticsLayout({ children }: AnalyticsLayoutProps) {
  return (
    <div className="flex h-full"> 
      <Sidebar links={analyticsLinks} /> 
      <main className="flex-1 p-6 overflow-y-auto top-0">
        {children} 
      </main>
    </div>
  );
}
```

## `page.tsx`

```ts
// app/(protected)/analytics/page.tsx
import { Suspense } from "react";
import { Metadata } from "next";
import { getFinancialData } from "@/actions/analytics/get-financial-data";
import { getSalesMetrics } from "@/actions/analytics/get-sales-metrics";
import { getComplaintStats } from "@/actions/analytics/get-complaint-stats";
import KpiDashboard from "@/components/analytics/KpiDashboard";
import FinancialOverview from "@/components/analytics/FinancialOverview";
import { SalesChart } from "@/components/analytics/SalesChart";
import ComplaintAnalytics from "@/components/analytics/ComplaintAnalytics";
import { DataFilters } from "@/components/analytics/DataFilters";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Skeleton } from "@/components/ui/skeleton";
import { canAccessAnalyticsPage } from "@/lib/security/permission-checker";
import { redirect 
```

## `page.tsx`

```ts
// Path: app/(protected)/analytics/complaints/page.tsx

import { auth } from "@/auth";
import { Metadata } from "next";
import { db } from '@/lib/db';
import { Suspense } from "react";

import { DataFilters, DataFilterOptions } from "@/components/analytics/DataFilters";
import ComplaintAnalytics from "@/components/analytics/ComplaintAnalytics";
import { AnomalyDetection } from "@/components/analytics/AnomalyDetection";
import { Card } from "@/components/ui/card";

import { getComplaintStats, ComplaintStatsParams, ComplaintStats } from "@/actions/analytics/get-complaint-stats";

export const dynamic = 'force-dynamic';

// Update types to reflect that searchParams and params are Promises
interface ComplaintAnalyticsPageProps {
    params: Promise<{ [key: string]: string | string[] | undefined }>; // Assuming params is also a Promise based on docs
    searchParams: Promise<{ [key: string]: string | string[] | undefined }>;
}

export const metadata: Metadata = {
  title: "Complaint Analyti
```

## `actions.ts`

```ts
// app/(protected)/analytics/financials/actions.ts

"use server";

import { db } from "@/lib/db";
import { currentUser } from "@/lib/auth";
import { canViewFinancialData } from "@/lib/security/permission-checker";

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
  endDate,
  serviceType,
  providerId,
}: FinancialDataParams = {}): Promise<FinancialMetrics> {
  const hasPermission = await canViewFinancial
```

## `page.tsx`

```ts
// app/(protected)/analytics/financials/page.tsx
import { Suspense } from "react";
import { DataFilters, DataFilterOptions } from "@/components/analytics/DataFilters";
import FinancialOverview from "@/components/analytics/FinancialOverview";
import { auth } from "@/auth";
import { getFinancialData } from "./actions";
import { db } from '@/lib/db';

interface FinancialAnalyticsPageProps {
  searchParams: {
    [key: string]: string | string[] | undefined;
  };
}

async function fetchProvidersForFilters() {
     try {
         console.log("Attempting to fetch providers for filters...");
          const providers = await db.provider.findMany({
              select: { id: true, name: true },
              orderBy: { name: 'asc' },
          });
          console.log("Successfully fetched providers:", providers.length, "items");
          console.log("Provider data sample:", providers.slice(0, 5));
          return providers;
         console.warn("Placeholder fetchProvidersForFilters used 
```

## `page.tsx`

```ts
///app/(protected)/analytics/providers/page.tsx


import { Metadata } from "next";
import { Card } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { DataFilters } from "@/components/analytics/DataFilters";
import { AnomalyDetection } from "@/components/analytics/AnomalyDetection";
import ServicePerformance from "@/components/analytics/ServicePerformance";

export const metadata: Metadata = {
  title: "Provider Analytics | Dashboard",
  description: "Analysis and performance metrics for service providers",
};

export default async function ProviderAnalyticsPage() {
  return (
    <div className="flex flex-col gap-5">
      <div className="flex justify-between items-center">
        <div>
          <h1 className="text-3xl font-bold tracking-tight">Provider Analytics</h1>
          <p className="text-muted-foreground">
            Monitor provider performance, revenue generation, and service quality metrics
          </p
```

## `page.tsx`

```ts
///app/(protected)/analytics/sales/page.tsx

import { auth } from "@/auth";
import { SalesChart } from "@/components/analytics/SalesChart";
import { DataFilters } from "@/components/analytics/DataFilters";
import { Metadata } from "next";
import { db } from '@/lib/db';

export const metadata = {
  title: "Sales Analytics",
  description: "Sales performance metrics and trend analysis",
};

interface SalesAnalyticsPageProps {
    searchParams: { [key: string]: string | string[] | undefined };
}

async function fetchProvidersForFilters() {
     try {
         console.log("Attempting to fetch providers for filters...");
          const providers = await db.provider.findMany({
              select: { id: true, name: true },
              orderBy: { name: 'asc' },
          });
          console.log("Successfully fetched providers:", providers.length, "items");
          console.log("Provider data sample:", providers.slice(0, 5));
          return providers;
         console.warn("Placeholde
```

## `page.tsx`

```ts
///app/(protected)/audit-logs/page.tsx

import { getBlacklistLogs } from "@/actions/blacklist/get-blacklist-logs";
import { BlackLogTable } from "@/components/security/BlackLogTable";
import { auth } from "@/auth";

export default async function AuditLogsPage() {
  const session = await auth();
  
  if (!session || !session.user) {
    return (
      <div className="w-[90vw] mx-auto min-h-[90vh] flex justify-center items-center">
        <div className="text-center p-8 bg-white rounded-lg shadow-md">
          <h2 className="text-xl font-bold mb-4">Unauthorized</h2>
          <p>You must be signed in to view this page.</p>
        </div>
      </div>
    );
  }
  
  // Only allow admins to view audit logs
  if (session.user.role !== "ADMIN") {
    return (
      <div className="w-[90vw] mx-auto min-h-[90vh] flex justify-center items-center">
        <div className="text-center p-8 bg-white rounded-lg shadow-md">
          <h2 className="text-xl font-bold mb-4">Access Denied</h2>
      
```

## `page.tsx`

```ts
//app/(protected)/bulk-services/page.tsx

"use client";

import { Separator } from "@/components/ui/separator";
import { BulkServiceFilters } from "@/components/bulk-services/BulkServiceFilters";
import BulkServiceList from "@/components/bulk-services/BulkServiceList";
import { useState } from "react";

export default function BulkServicesPage() {
  // Define empty arrays for the required props
  const [providers, setProviders] = useState([]);
  const [services, setServices] = useState([]);

  return (
    <div className="container py-8">
      <div className="flex justify-between items-center mb-6">
        <h1 className="text-3xl font-bold">Bulk Services</h1>
        {/* Add any action buttons here if needed */}
      </div>
      <Separator />
      <BulkServiceFilters providers={providers} services={services} />
      <BulkServiceList />
    </div>
  );
}
```

## `page.tsx`

```ts
//app/(protected)/bulk-services/[id]/page.tsx


import { Metadata } from "next";
import { notFound } from "next/navigation";
import { Heading } from "@/components/ui/heading";
import { Separator } from "@/components/ui/separator";
import { Button } from "@/components/ui/button";
import { Edit, ArrowLeft } from "lucide-react";
import Link from "next/link";
import BulkServiceDetails from "@/components/bulk-services/BulkServiceDetails";
import BulkServiceStats from "@/components/bulk-services/BulkServiceStats";
import { getBulkServiceById } from "@/actions/bulk-services/getBulkServiceById";

export const metadata: Metadata = {
  title: "Bulk Service Details",
  description: "View bulk service details",
};

export default async function BulkServiceDetailsPage({
  params,
}: {
  params: { id: string };
}) {
  const bulkService = await getBulkServiceById(params.id);

  if (!bulkService) {
    notFound();
  }

  return (
    <div className="flex-1 space-y-4 p-4 md:p-8 pt-6">
      <div classN
```

## `page.tsx`

```ts
//app/(protected)/bulk-services/[id]/edit/page.tsx


import { Metadata } from "next";
import { notFound } from "next/navigation";
import { Heading } from "@/components/ui/heading";
import { Separator } from "@/components/ui/separator";
import { Button } from "@/components/ui/button";
import { ArrowLeft } from "lucide-react";
import Link from "next/link";
import BulkServiceForm from "@/components/bulk-services/BulkServiceForm";
import { getBulkServiceById } from "@/actions/bulk-services/getBulkServiceById";
import { getAllServices } from "@/actions/services/getAllServices";
import { getAllProviders } from "@/actions/providers/getAllProviders";

export const metadata: Metadata = {
  title: "Edit Bulk Service",
  description: "Modify bulk service details",
};

export default async function EditBulkServicePage({
  params,
}: {
  params: { id: string };
}) {
  // Fetch the bulk service, services, and providers
  const [bulkService, services, providers] = await Promise.all([
    getBulkServi
```

## `page.tsx`

```ts
///app/(protected)/bulk-services/import/page.tsx

import { Metadata } from "next";
import { Heading } from "@/components/ui/heading";
import { Separator } from "@/components/ui/separator";
import ImportForm from "@/components/bulk-services/ImportForm";
import { getAllServices } from "@/actions/services/getAllServices";
import { getAllProviders } from "@/actions/providers/getAllProviders";

export const metadata: Metadata = {
  title: "Import Bulk Services",
  description: "Import bulk services from CSV file",
};

export default async function ImportBulkServicesPage() {
  // Fetch services and providers for mapping during import
  const services = await getAllServices({ type: "BULK" });
  const providers = await getAllProviders({ isActive: true });

  return (
    <div className="flex-1 space-y-4 p-4 md:p-8 pt-6">
      <div className="flex items-center justify-between">
        <Heading
          title="Import Bulk Services"
          description="Upload a CSV file to import bulk servi
```

## `page.tsx`

```ts
//app/(protected)/bulk-services/new/page.tsx

import { Metadata } from "next";
import { Heading } from "@/components/ui/heading";
import { Separator } from "@/components/ui/separator";
import { BulkServiceForm } from "@/components/bulk-services/BulkServiceForm";
import { getAllServices } from "@/actions/services/getAllServices";
import { getAllProviders } from "@/actions/providers/getAllProviders";

export const metadata: Metadata = {
  title: "Create Bulk Service",
  description: "Add a new bulk service to the system",
};

export default async function NewBulkServicePage() {
  // Fetch services and providers for the form
  const services = await getAllServices({ type: "BULK" });
  const providers = await getAllProviders({ isActive: true });

  return (
    <div className="flex-1 space-y-4 p-4 md:p-8 pt-6">
      <div className="flex items-center justify-between">
        <Heading
          title="Create New Bulk Service"
          description="Add a new bulk service to the system"
   
```

## `page.tsx`

```ts
"use client";

import { UserInfo } from "@/components/user-info";
import { useCurrentUser } from "@/hooks/use-current-user";

export default function ClientPage() {
  const user = useCurrentUser();

  return <UserInfo user={user} label="Client component" />;
}

```

## `page.tsx`

```ts
// app/(protected)/complaints/page.tsx
"use client";

import { useState, useEffect, useCallback, useMemo } from "react";
import { useRouter, useSearchParams } from "next/navigation";
import { ComplaintList } from "@/components/complaints/ComplaintList";
import { ComplaintFilters, ComplaintFiltersState } from "@/components/complaints/ComplaintFilters";
import { NotificationBanner } from "@/components/complaints/NotificationBanner";
import { Button } from "@/components/ui/button";
import { Loader2, PlusCircle } from "lucide-react";
import { useComplaints } from "@/hooks/use-complaints";
import { ComplaintStatus } from "@/lib/types/enums";

export default function ComplaintsPage() {
  const router = useRouter();
  const searchParams = useSearchParams();

  // --- Helper funkcija za bezbedno kreiranje Date objekata ---
  const safeDate = useCallback((dateString: string | null | undefined): Date | undefined => {
    if (!dateString) return undefined;
    const date = new Date(dateString);
 
```

## `ComplaintDetailPageClient.tsx`

```ts
// Path: app/(protected)/complaints/[id]/ComplaintDetailPageClient.tsx
"use client";

import { useState, useEffect } from "react";
import { useRouter, useParams, notFound } from "next/navigation";
import { useSession } from "next-auth/react"; // Assuming you use next-auth for session management
import { Button } from "@/components/ui/button";
import { ArrowLeft, Edit, Trash2, Send } from "lucide-react";
import StatusBadge from "@/components/complaints/StatusBadge"; // Custom component for status badge
import CommentSection from "@/components/complaints/CommentSection"; // Custom component for comments
import { ComplaintTimeline } from "@/components/complaints/ComplaintTimeline"; // Custom component for timeline
import { FileUpload } from "@/components/complaints/FileUpload"; // Custom component for file uploads
import { Separator } from "@/components/ui/separator"; // Shadcn UI Separator
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"; // Shadcn UI Tabs

```

## `page.tsx`

```ts
// Path: app/(protected)/complaints/[id]/page.tsx

// This is a Server Component, used for fetching data and passing it to Client Components

import { Suspense } from "react";
import { Metadata } from "next";
import { notFound } from "next/navigation"; // Import notFound
import { auth } from "@/auth"; // Assuming auth is used for session on server
import { getComplaintById } from "@/actions/complaints/get-by-id"; // Import the action to fetch complaint details
import { getAssignableUsers } from "@/actions/users/get-assignable-users"; // Import the action to fetch assignable users
import ComplaintDetailPageClient from "./ComplaintDetailPageClient"; // Import the Client Component


// Define metadata for the page
export const metadata: Metadata = {
  title: "Complaint Details",
  description: "View and manage complaint details",
};

// Fetch data on the server
export default async function ComplaintDetailPageServer({ params }: { params: { id: string } }) {
    // KORIGOVANO: Await params
```

## `page.tsx`

```ts
// app/(protected)/complaints/[id]/edit/page.tsx


"use client";

import { useState, useEffect } from "react";
import { useRouter } from "next/navigation";
import { useParams } from "next/navigation";
import { ComplaintForm } from "@/components/complaints/ComplaintForm";
import { NotificationBanner } from "@/components/complaints/NotificationBanner";
import { getComplaintById } from "@/lib/actions/complaints";
import { updateComplaint } from "@/actions/complaints/update";
import { Complaint } from "@/lib/types/interfaces";
import { Loader2 } from "lucide-react";

export default function EditComplaintPage() {
  const params = useParams();
  const router = useRouter();
  const [complaint, setComplaint] = useState<Complaint | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  const [notification, setNotification] = useState<{
    type: "success" | "error";
    message: string;
  } | null>(null);

  useEffect(() => {
  
```

## `page.tsx`

```ts
// Path: app/(protected)/complaints/new/page.tsx

import { Metadata } from "next";
import { getProviders } from "@/actions/complaints/providers";
import { ComplaintFormWrapper } from "@/components/complaints/ComplaintFormWrapper";
// Import the functions to get humanitarian orgs and parking services data
import { getHumanitarianOrgs } from "@/actions/complaints/humanitarian";
import { getParkingServices } from "@/actions/complaints/parking";

export const metadata: Metadata = {
  title: "Submit New Complaint",
  description: "Form to submit a new complaint",
};

export default async function NewComplaintPage() {
  // Fetch all required data in parallel for better performance
  const [providersData, humanitarianOrgsData, parkingServicesData] = await Promise.all([
    getProviders(),
    getHumanitarianOrgs(),
    getParkingServices()
  ]);

  // Log loaded data for debugging
  console.log(`Loaded providers: ${providersData.length}`);
  console.log(`Loaded humanitarian orgs: ${humanitari
```

## `page.tsx`

```ts
// Path: /app/(protected)/contracts/page.tsx
import { Metadata } from "next";
import Link from "next/link";
import { ContractsSection } from "@/components/contracts/ContractsSection";
import { Button } from "@/components/ui/button";
import { PlusCircle, FileSpreadsheet, Clock } from "lucide-react";
import { db } from "@/lib/db";
import { Contract as ContractType, ContractStatus, ContractType as PrismaContractType } from "@prisma/client";

export const metadata: Metadata = {
  title: "Contracts Management | Management Dashboard",
  description: "View and manage all contracts with advanced filtering and search capabilities.",
};

// Funkcija za dobijanje ugovora iz baze
async function getContracts(searchParams: {
  page?: string;
  limit?: string;
  search?: string;
  status?: string;
  type?: string;
  partner?: string;
}): Promise<{
  contracts: ContractType[]; // Koristiti ContractType za jasnoću
  totalCount: number;
  totalPages: number;
  currentPage: number;
  useServerPagination:
```

## `page.tsx`

```ts
///app/(protected)/contracts/[id]/page.tsx

import { notFound } from "next/navigation";
import { Suspense } from "react";
import { ContractDetails } from "@/components/contracts/ContractDetails";
import { ContractStatusBadge } from "@/components/contracts/ContractStatusBadge";
import { AttachmentList } from "@/components/contracts/AttachmentList";
import { AttachmentUpload } from "@/components/contracts/AttachmentUpload";
import { ExpiryWarning } from "@/components/contracts/ExpiryWarning";
import { Metadata } from "next";
import { db } from "@/lib/db";
import { calculateContractRevenue } from "@/lib/contracts/revenue-calculator";
import { RevenueBreakdown } from "@/components/contracts/RevenueBreakdown";

export async function generateMetadata({ params }: { params: { id: string } }): Promise<Metadata> {
  try {
    const { id } = await params;

    const contract = await db.contract.findUnique({
      where: { id },
      select: {
        name: true,
        contractNumber: true
    
```

## `page.tsx`

```ts
// /app/(protected)/contracts/[id]/edit/page.tsx
import { ContractForm } from "@/components/contracts/ContractForm";
import { notFound, redirect } from "next/navigation";
import { Metadata } from "next";
import { db } from "@/lib/db";
import { auth } from "@/auth";

export async function generateMetadata(props: { params: { id: string } }): Promise<Metadata> {
  const params = await props.params;
  const id = params.id;
  
  try {
    const contract = await db.contract.findUnique({
      where: { id },
    });

    if (!contract) {
      return {
        title: "Contract Not Found",
      };
    }

    return {
      title: `Edit ${contract.name} | Contract Management`,
    };
  } catch (error) {
    return {
      title: "Error Loading Contract",
    };
  }
}

interface EditContractPageProps {
  params: Promise<{
    id: string;
  }>;
}

async function getContract(id: string, currentUserId?: string, userRole?: string) {
  try {
    const contract = await db.contract.findUnique({
      
```

## `page.tsx`

```ts
// app/(protected)/contracts/expiring/page.tsx
"use client";

import React, { useState, useEffect, useCallback } from 'react';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { 
  CalendarDays, 
  FileText, 
  AlertCircle, 
  CheckCircle, 
  Clock, 
  Filter, 
  Loader2, 
  ArrowLeft,
  RefreshCw,
  Download,
  AlertTriangle,
  Calendar,
  Building,
  DollarSign
} from 'lucide-react';
import { ExpiryTimelineChart } from "@/components/contracts/charts/ExpiryTimelineChart";
import { EnhancedContractList } from "@/components/contracts/enhanced-contract-list";
import Link from "next/link";
import { useRouter } from 'next/navigation';
import { to
```

## `page.tsx`

```ts
///app/(protected)/contracts/new/page.tsx

import { ContractForm } from "@/components/contracts/ContractForm";
import { Metadata } from "next";
import { getHumanitarianOrgs } from "@/actions/organizations/get-humanitarian";
import { getProviders } from "@/actions/providers/get-providers";
import { getParkingServices } from "@/actions/services/get-parking-services";
import { getAllOperators } from "@/actions/operators";

export const metadata: Metadata = {
  title: "Create New Contract | Management Dashboard",
  description: "Create a new contract in the system",
};

export default async function NewContractPage() {

  const humanitarianOrgs = await getHumanitarianOrgs();
  const providers = await getProviders();
  const parkingServices = await getParkingServices();
  const operators = await getAllOperators();
  return (
    <div className="container mx-auto py-8">
      <div className="mb-8">
        <h1 className="text-2xl font-bold mb-2">Create New Contract</h1>
        <p className=
```

## `page.tsx`

```ts
///app/(protected)/contracts/providers/page.tsx


import { Suspense } from "react";
import { ContractList } from "@/components/contracts/ContractList";
import { ContractTypeDistribution } from "@/components/contracts/charts/ContractTypeDistribution";
import { Metadata } from "next";

export const metadata: Metadata = {
  title: "Provider Contracts | Management Dashboard",
  description: "View all provider contracts",
};

export default function ProviderContractsPage() {
  return (
    <div className="p-6 space-y-6">
      <div>
        <h1 className="text-2xl font-bold tracking-tight">Provider Contracts</h1>
        <p className="text-gray-500">
          View and manage all provider contracts
        </p>
      </div>
      
      <div className="bg-white rounded-lg shadow p-6">
        <Suspense fallback={<div>Loading contract statistics...</div>}>
          <ContractTypeDistribution type="PROVIDER" />
        </Suspense>
      </div>
      
      <Suspense fallback={<div>Loading pro
```

## `page.tsx`

```ts
// app/(protected)/dashboard/page.tsx
import { Metadata } from "next";
import { auth } from "@/auth";
import { redirect } from "next/navigation";
import DashboardHeader from "@/components/dashboard/DashboardHeader";
import DashboardShell from "@/components/dashboard/DashboardShell";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import Link from "next/link";
import { getUserRole } from "@/lib/auth/auth-utils";
import { UserRole } from "@prisma/client";

export const metadata: Metadata = {
  title: "Dashboard",
  description: "Main dashboard for the application.",
};

export default async function DashboardPage() {
  const session = await auth();
  
  if (!session) {
    redirect("/auth/login");
  }

  const userRole = await getUserRole();
  
  console.log("[DASHBOARD] User accessing dashboard:", {
    email: session.user?.email,
    role: userRole,
    sessionRole: (session.user as any)?.role
  });

  //
```

## `page.tsx`

```ts
// /app/(protected)/humanitarian-orgs/page.tsx
import { Metadata } from "next";
import { HumanitarianOrgList } from "@/components/humanitarian-orgs/HumanitarianOrgList";
import { Button } from "@/components/ui/button";
import Link from "next/link";
import { Plus, RefreshCw } from "lucide-react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";

export const metadata: Metadata = {
  title: "Humanitarian Organizations | Management Dashboard",
  description: "View and manage all humanitarian organizations in the system",
};

export default function HumanitarianOrgsPage() {
  return (
    <div className="container mx-auto p-6">
      <div className="flex flex-col lg:flex-row lg:items-center lg:justify-between gap-4 p-6 rounded-lg shadow-sm border mb-6">
        <div className="space-y-1">
          <h1 className="text-3xl font-bold tracking-tight">
            Humanitarian Organizations
          </h1>
          <p className="max-w-2xl">
            View and
```

## `page.tsx`

```ts
// /app/(protected)/humanitarian-orgs/[id]/page.tsx

import { Suspense } from "react";
import { Metadata } from "next";
import { notFound } from 'next/navigation';
import Link from 'next/link';
import { db } from '@/lib/db';
import { HumanitarianOrgWithDetails } from '@/lib/types/humanitarian-org-types';
import { HumanitarianOrgDetails } from "@/components/humanitarian-orgs/HumanitarianOrgDetails";
import { HumanitarianOrgContracts } from "@/components/humanitarian-orgs/HumanitarianOrgContracts";
import { Button } from '@/components/ui/button';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { Card, CardContent, CardHeader, CardTitle, CardFooter } from '@/components/ui/card'; // Added CardFooter
import { Badge } from '@/components/ui/badge'; // Added Badge import
import DetailSkeleton from "@/components/skeletons/DetailSkeleton";
import { Banknote, FileText, ClipboardList, AlertCircle } from 'lucide-react'; // Added Banknote

interface Humanitari
```

## `page.tsx`

```ts
// /app/(protected)/humanitarian-orgs/[id]/edit/page.tsx

import { Metadata } from "next";
import { notFound } from 'next/navigation';
import { db } from '@/lib/db';
import { HumanitarianOrg } from '@prisma/client';
import { HumanitarianOrgForm } from "@/components/humanitarian-orgs/HumanitarianOrgForm";

interface EditHumanitarianOrgPageProps {
    params: {
        id: string;
    };
}

async function getHumanitarianOrgDetails(orgId: string): Promise<HumanitarianOrg | null> {
    try {
        return await db.humanitarianOrg.findUnique({
            where: { id: orgId },
        });
    } catch (error) {
        console.error(`Error fetching humanitarian organization ${orgId} details for editing from DB:`, error);
        return null;
    }
}

export async function generateMetadata({ params }: EditHumanitarianOrgPageProps): Promise<Metadata> {
     const { id } = await params;
     const organization = await getHumanitarianOrgDetails(id);

     return {
         title: organization ?
```

## `page.tsx`

```ts
// /app/(protected)/humanitarian-orgs/new/page.tsx
import { Metadata } from "next";
import { HumanitarianOrgForm } from "@/components/humanitarian-orgs/HumanitarianOrgForm";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import Link from "next/link";
import { Button } from "@/components/ui/button";
import { ChevronLeft } from "lucide-react";

export const metadata: Metadata = {
  title: "Create New Humanitarian Organization | Management Dashboard",
  description: "Create a new humanitarian organization in the system",
};

export default function NewHumanitarianOrgPage() {
  return (
    <div className="container mx-auto p-6">
      <div className="mb-6">
        <Button asChild variant="outline" className="mb-4">
          <Link href="/humanitarian-orgs">
            <ChevronLeft className="mr-2 h-4 w-4" />
            Back to Organizations
          </Link>
        </Button>
        
        <div className="flex items-center justify-between">
        
```

## `page.tsx`

```ts
// /app/(protected)/humanitarian-renewals/page.tsx
"use client";

import React, { useState, useEffect, useCallback } from 'react'; // Dodajte useCallback ovde
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Textarea } from '@/components/ui/textarea';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { Dialog, DialogContent, DialogDescription, DialogFooter, DialogHeader, DialogTitle, DialogTrigger } from '@/components/ui/dialog';
import { Checkbox } from '@/components/ui/checkbox';
import { CalendarDays, FileText, AlertCircle, CheckCircle, Clock, Plus, Edit, Eye, Filter, Loader2, Trash2 } from 'lucide-react';
import { useHumanitarianRenewals, useRenewalStatistics } from '@/hooks/use
```

## `page.tsx`

```ts
// Path: app/(protected)/notifications/page.tsx
import { Suspense } from 'react';
import { Metadata } from 'next';
import { getCurrentUser } from '@/lib/security/auth-helpers';
import NotificationList from '@/components/notifications/NotificationList';
import { Loader2 } from "lucide-react";
import { getNotificationsByUserId } from '@/actions/notifications/get-by-user-id';

export const metadata: Metadata = {
  title: 'Notifications | Service Management',
  description: 'View and manage your notifications',
};

export default async function NotificationsPage() {
  const user = await getCurrentUser();

  if (!user) {
    return <div>Unauthorized</div>;
  }

  const notificationsResult = await getNotificationsByUserId(user.id);

  if (notificationsResult && 'error' in notificationsResult) {
      console.error("Failed to fetch notifications:", notificationsResult.error);
      return <div className="container mx-auto py-8 text-center text-red-500">Error loading notifications.</div>;
  }

```

## `page.tsx`

```ts
// Path: app/(protected)/notifications/settings/page.tsx
import { Metadata } from 'next';
import { getCurrentUser } from '@/lib/security/auth-helpers';
import NotificationSettings from '@/components/notifications/NotificationSettings'; // Import the settings component
import { NotificationPreferences } from '@/lib/types/notification-types'; // Import the updated type
import { getUserNotificationPreferences } from '@/actions/notifications/get-preferences'; // !!! YOU NEED TO IMPLEMENT THIS ACTION !!!


export const metadata: Metadata = {
  title: 'Notification Settings | Service Management',
  description: 'Manage your notification preferences',
};

// Define a default set of preferences matching the updated interface
const defaultNotificationPreferences: NotificationPreferences = {
    userId: '', // This will be set by the action or backend
    contractExpiring: { inApp: true, email: false },
    contractRenewalStatusChange: { inApp: true, email: false },
    complaintAssigned: { inAp
```

## `page.tsx`

```ts
// Path: app/(protected)/operators/page.tsx
import { getOperators } from "@/actions/operators";
import { OperatorList } from "@/components/operators/OperatorList";
import { OperatorFilters } from "@/components/operators/OperatorFilters";
import { Suspense } from "react";
import { Metadata } from "next";
import { Button } from "@/components/ui/button";
import Link from "next/link";
import { Plus } from "lucide-react";
import { Skeleton } from "@/components/ui/skeleton";
import { Separator } from "@/components/ui/separator";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";

export const metadata: Metadata = {
  title: "Operators | Management Platform",
  description: "Manage operators in the system",
};

export default async function OperatorsPage({
  searchParams,
}: {
  searchParams: {
    search?: string;
    active?: "all" | "active" | "inactive";
    sortBy?: "name" | "code" | "createdAt";
    sortOrder?: "asc" | "desc";
    page?: string;
    pageSiz
```

## `page.tsx`

```ts
// app/(protected)/operators/[id]/page.tsx

import { Metadata } from "next";
import { notFound } from "next/navigation";

import { getOperatorById } from "@/actions/operators";
import DashboardHeader from "@/components/dashboard/DashboardHeader";
import DashboardShell from "@/components/dashboard/DashboardShell";
import { OperatorDetails } from "@/components/operators/OperatorDetails";
import { OperatorContracts } from "@/components/operators/OperatorContracts";
import { buttonVariants } from "@/components/ui/button";
import Link from "next/link";
import { cn } from "@/lib/utils";
import { BackButton } from "@/components/BackButton";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";

export const metadata: Metadata = {
  title: "Operator Details",
  description: "View operator details",
};

interface OperatorDetailsPageProps {
  params: {
    id: string;
  };
}

export default async function OperatorDetailsPage({ params }: OperatorDetailsPageProps) {
  co
```

## `page.tsx`

```ts
// app/(protected)/operators/[id]/edit/page.tsx

import { Metadata } from "next";
import { notFound, redirect } from "next/navigation";

import { getOperatorById } from "@/actions/operators";
import DashboardHeader from "@/components/dashboard/DashboardHeader";
import DashboardShell from "@/components/dashboard/DashboardShell";
import { OperatorForm } from "@/components/operators/OperatorForm";
import { buttonVariants } from "@/components/ui/button";
import Link from "next/link";
import { cn } from "@/lib/utils";
import { getUserRole } from "@/lib/auth/auth-utils";
import { UserRole } from "@prisma/client";

export const metadata: Metadata = {
  title: "Edit Operator",
  description: "Edit operator details",
};

interface EditOperatorPageProps {
  params: {
    id: string;
  };
}

export default async function EditOperatorPage({ params }: EditOperatorPageProps) {
  const { id } = await params;
  const userRole = await getUserRole();
  
  // Only ADMIN and MANAGER can edit operators
  i
```

## `page.tsx`

```ts
// app/(protected)/operators/new/page.tsx

import { Metadata } from "next";
import { redirect } from "next/navigation";

import { OperatorForm } from "@/components/operators/OperatorForm";
import DashboardHeader from "@/components/dashboard/DashboardHeader";
import DashboardShell from "@/components/dashboard/DashboardShell";
import { Button } from "@/components/ui/button";
import Link from "next/link";
import { getUserRole, getCurrentUser, hasRequiredRole } from "@/lib/auth/auth-utils";
import { UserRole } from "@prisma/client";

export const metadata: Metadata = {
  title: "New Operator",
  description: "Create a new operator in the system.",
};

export default async function NewOperatorPage() {
  console.log("[NEW_OPERATOR_PAGE] Starting page load");
  
  try {
    // Debug: Check multiple ways to get user info
    const userRole = await getUserRole();
    const currentUser = await getCurrentUser();
    const hasAccess = await hasRequiredRole([UserRole.ADMIN, UserRole.MANAGER]);
    
```

## `page.tsx`

```ts
//app/(protected)/parking-services/page.tsx
import { Suspense } from "react";
import { Metadata } from "next";
import { getParkingServices } from "@/actions/parking-services/getParkingServices";
import ParkingServiceList from "@/components/parking-services/ParkingServiceList";
import ParkingServiceFilters from "@/components/parking-services/ParkingServiceFilters";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { PlusCircle } from "lucide-react";
import Link from "next/link";
import PageHeader from "@/components/PageHeader";
import ListSkeleton from "@/components/skeletons/ListSkeleton";

export const metadata: Metadata = {
  title: "Parking Services | Contract Management System",
  description: "Manage parking services in the contract management system",
};

interface ParkingServiceFilters {
  searchTerm?: string;
  isActive?: boolean | undefined;
  page: number;
  pageSize: number;
  sortBy?: str
```

## `page.tsx`

```ts
//app/(protected)/parking-services/[id]/page.tsx
import { Metadata } from "next";
import { notFound } from "next/navigation";
import { Suspense } from "react";
import { 
  getParkingServiceById,
  getContractsCountForParkingService,
  getServicesCountForParkingService,
  getActiveContractsCount,
  getTotalParkingRevenue,
  getAvgDailyParkingRevenue,
  getParkingServiceStats,
  getMonthlyRevenueStats
} from "@/actions/parking-services";
import ParkingServiceDetails from "@/components/parking-services/ParkingServiceDetails";
import ParkingServiceContracts from "@/components/parking-services/ParkingServiceContracts";
import ParkingServiceServicesOverview from "@/components/parking-services/ParkingServiceServicesOverview";
import ParkingServiceReports from "@/components/parking-services/ParkingServiceReports";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } 
```

## `page.tsx`

```ts
//app/(protected)/parking-services/[id]/edit/page.tsx

import { Metadata } from "next";
import { notFound } from "next/navigation";
import { getParkingServiceById } from "@/actions/parking-services/getParkingServiceById";
import ParkingServiceForm from "@/components/parking-services/ParkingServiceForm";
import PageHeader from "@/components/PageHeader";

export const metadata: Metadata = {
  title: "Edit Parking Service | Contract Management System",
  description: "Edit parking service in the contract management system",
};

export default async function EditParkingServicePage({
  params,
}: {
  params: { id: string };
}) {
  const { id } = params;
  const parkingServiceResult = await getParkingServiceById(id);
  
  if (!parkingServiceResult.success || !parkingServiceResult.data) {
    notFound();
  }
  
  const parkingService = parkingServiceResult.data;

  return (
    <div className="container mx-auto py-6 space-y-6">
      <PageHeader
        title="Edit Parking Service"
        de
```

## `page.tsx`

```ts
//app/(protected)/parking-services/new/page.tsx

import { Metadata } from "next";
import { redirect } from "next/navigation";
import { z } from "zod";
import { create } from "@/actions/parking-services/create";
import ParkingServiceForm from "@/components/parking-services/ParkingServiceForm";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import PageHeader from "@/components/PageHeader";

export const metadata: Metadata = {
  title: "Create Parking Service | Contract Management System",
  description: "Create a new parking service in the contract management system",
};

export default function CreateParkingServicePage() {
  // Server action to create a new parking service
  async function createParkingService(formData: FormData) {
    "use server";
    
    const rawData = Object.fromEntries(formData.entries());
    
    // Process boolean fields
    const data = {
      ...rawData,
      isActive: rawData.isActive === "on" || rawData.isActive === "tru
```

## `page.tsx`

```ts
// /app/(protected)/products/page.tsx

import { Metadata } from "next";
import Link from "next/link";
// Uvozimo AŽURIRANU ProductList komponentu
import { ProductList } from "@/components/products/ProductList"; // Ova komponenta je ažurirana
// Uvozimo UI komponente
import { Button } from "@/components/ui/button";
import { PlusCircle } from "lucide-react"; // Ikona

export const metadata: Metadata = {
  title: "Products List | Management Dashboard",
  description: "View and manage all products.",
};

// Stranica za prikaz liste svih proizvoda
// Ovo je Server Komponenta koja renderuje ProductList komponentu (Client Component)
export default function ProductsPage() {
  return (
    <div className="p-6 space-y-6">
      <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
        <div>
          <h1 className="text-2xl font-bold tracking-tight">Products</h1>
          <p className="text-gray-500">
            Manage your organization's products.
          
```

## `page.tsx`

```ts
// /app/(protected)/products/[id]/page.tsx

import { Metadata } from "next";
// Uvozimo AŽURIRANU ProductDetails komponentu
import { ProductDetails } from "@/components/products/ProductDetails"; // Ova komponenta je ažurirana
// Uvozimo AŽURIRANU Server Akciju za dohvatanje pojedinačnog proizvoda
import { getProductById } from '@/actions/products/get';


interface ProductDetailsPageProps {
    params: {
        id: string; // ID proizvoda iz URL-a
    };
}

// Dinamička generacija metapodataka
export async function generateMetadata(
    { params }: ProductDetailsPageProps,
): Promise<Metadata> {
    // Dohvatanje proizvoda za naslov (može biti null ako nije pronađen)
    const result = await getProductById(params.id);
    const product = result.data;

    return {
        title: product ? `${product.name} Details | Management Dashboard` : "Product Details | Management Dashboard",
        description: product ? `Details for product: ${product.name} (Code: ${product.code})` : "Product de
```

## `page.tsx`

```ts
// app/(protected)/providers/page.tsx
import { Metadata } from "next";
import { ProviderList } from "@/components/providers/ProviderList";
import { BlacklistSection } from "@/components/blacklist/BlacklistSection";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";
import { Suspense } from "react";
import { Skeleton } from "@/components/ui/skeleton";

export const metadata: Metadata = {
  title: "Providers | Management Dashboard",
  description: "View and manage all providers and blacklist in the system",
};

export default async function ProvidersPage() {
  const session = await auth();
  
  if (!session) {
    return <div>Unauthenticated</div>;
  }

  const userRole = session.user?.role as UserRole;
  
  if (![UserRole.ADMIN, UserRole.MANAGER].includes(userRole)) {
    return <div>Unauthorized</div>;
  }
  
  return (
    <div className="p-6 space-y-8 top-0">
      {/* Header */}
      <div className="flex flex-col md:flex-row md:items-center md:justify-between
```

## `page.tsx`

```ts
// /app/(protected)/providers/[id]/page.tsx
 
import { Metadata } from "next";
import { notFound } from 'next/navigation';
import { Suspense } from 'react';

import { getProviderDetails } from '@/actions/providers/getProviderDetails';

import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Pencil } from "lucide-react";
import Link from "next/link";
import PageHeader from "@/components/PageHeader";
import DetailSkeleton from "@/components/skeletons/DetailSkeleton";

import ProviderDetails from "@/components/providers/ProviderDetails";
import ProviderContracts from "@/components/providers/ProviderContracts";
// Uvezite preimenovanu komponentu
import ProviderServicesOverview from "@/components/providers/ProviderServicesOverview";


interface ProviderDetailsPageProps {
    params: {
        id: string;
    };
}

export async function generateMetadata
```

## `page.tsx`

```ts
// /app/(protected)/providers/[id]/edit/page.tsx
import { Metadata } from "next";
import { notFound } from 'next/navigation';
import { db } from '@/lib/db';
import { Provider } from '@prisma/client'; // Uvoz Provider modela

import { ProviderForm } from "@/components/providers/ProviderForm";


interface EditProviderPageProps {
    params: {
        id: string;
    };
}

// Funkcija za dohvatanje detalja provajdera na serveru
async function getProviderDetails(providerId: string): Promise<Provider | null> {
    try {
        const provider = await db.provider.findUnique({
            where: { id: providerId },
            // Za formu za editovanje, obično nam ne trebaju include relacije, samo osnovni podaci
        });

        return provider;

    } catch (error) {
        console.error(`Error fetching provider ${providerId} details for editing from DB:`, error);
        return null;
    }
}

// Generisanje metadata za stranicu
export async function generateMetadata({ params }: EditPro
```

## `page.tsx`

```ts
// /app/(protected)/providers/new/page.tsx
import { Metadata } from "next";
import { ProviderForm } from "@/components/providers/ProviderForm"; // Uvoz ProviderForm

export const metadata: Metadata = {
  title: "Create New Provider | Management Dashboard",
  description: "Create a new provider in the system",
};

// Server Komponenta za stranicu kreiranja novog provajdera
export default function NewProviderPage() {
  return (
    <div className="p-6 space-y-6">
      <div>
        <h1 className="text-2xl font-bold tracking-tight">Create New Provider</h1>
        <p className="text-gray-500">
          Add a new provider to the system
        </p>
      </div>

      <div className="bg-white rounded-lg shadow p-6">
        <ProviderForm />
      </div>

    </div>
  );
}
```

## `page.tsx`

```ts
// app/(protected)/reports/page.tsx
import { Metadata } from "next";
import Link from "next/link";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { getRecentReports } from "@/actions/reports/get-recent-reports";
import { CalendarIcon, FileText, Clock, BarChart } from "lucide-react";
// CORRECTED: Use named import for ReportPreview
import { ReportPreview } from "@/components/reports/ReportPreview";
import { format } from "date-fns"; // Import format for date display if needed elsewhere

export const metadata: Metadata = {
  title: "Reports | Dashboard",
  description: "Generate and manage custom reports",
};

export default async function ReportsPage() {
  // This would typically fetch recent reports from your database
  const recentReports = await getRecentReports();

  return (
    <div className="fl
```

## `page.tsx`

```ts
///app/(protected)/reports/generate/page.tsx

import { Metadata } from "next";
import { Card } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { ArrowLeft } from "lucide-react";
import Link from "next/link";
import { Button } from "@/components/ui/button";
import { ExportOptions } from "@/components/reports/ExportOptions";

export const metadata: Metadata = {
  title: "Generate Report | Dashboard",
  description: "Create custom reports with various data sets and formats",
};

export default async function GenerateReportPage() {
  return (
    <div className="flex flex-col gap-5">
      <div className="flex items-center gap-2 mb-2">
        <Button variant="ghost" size="sm" asChild className="h-8 w-8 p-0">
          <Link href="/reports">
            <ArrowLeft className="h-4 w-4" />
            <span className="sr-only">Back</span>
          </Link>
        </Button>
        <div>
          <h1 className="text-3xl fon
```

## `page.tsx`

```ts
///app/(protected)/reports/scheduled/page.tsx


import { Metadata } from "next";
import Link from "next/link";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Plus, ArrowLeft, Calendar, Clock, AlertCircle, CheckCircle } from "lucide-react";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { getScheduledReports } from "@/actions/reports/get-scheduled-reports";
import ScheduleForm from "@/components/reports/ScheduleForm";

export const metadata: Metadata = {
  title: "Scheduled Reports | Dashboard",
  description: "Manage and schedule automatic report generation",
};

export default async function ScheduledReportsPage() {
  // This would typically fetch scheduled reports from your database
  const scheduledReports = await getScheduledReports();
  
  return (
    <div className="flex flex-col gap-5">
      <div className="flex items-center gap-
```

## `page.tsx`

```ts
import { UserInfo } from "@/components/user-info";
import { currentUser } from "@/lib/auth";

export default async function ServerPage() {
  const user = await currentUser();

  return <UserInfo user={user} label="Server component" />;
}

```

## `page.tsx`

```ts
// /app/(protected)/services/page.tsx
import { Metadata } from "next";
import Link from "next/link";
import { ServiceList } from "@/components/services/ServiceList";
import { Button } from "@/components/ui/button";
import { PlusCircle, FileSpreadsheet } from "lucide-react";

export const metadata: Metadata = {
  title: "Services List | Management Dashboard",
  description: "View and manage all services with advanced filtering and search capabilities.",
};

export default function ServicesPage() {
  return (
    <div className="min-h-screen bg-card">
      <div className="container mx-auto p-6 space-y-6">
        {/* Header sekcija */}
        <div className="flex flex-col lg:flex-row lg:items-center lg:justify-between gap-4 p-6 rounded-lg shadow-sm border">
          <div className="space-y-1">
            <h1 className="text-3xl font-bold tracking-tight">
              Services Management
            </h1>
            <p className="max-w-2xl">
              Manage your organization's 
```

## `page.tsx`

```ts
// /app/(protected)/services/[serviceType]/page.tsx

import { Metadata } from "next";
import Link from "next/link";
import { ServiceList } from "@/components/services/ServiceList";
import { Button } from "@/components/ui/button";
import { PlusCircle } from "lucide-react";
import { ServiceType } from "@prisma/client";


interface ServicesByTypePageProps {
    params: {
        serviceType: string;
    };
}

export async function generateMetadata(
    { params }: ServicesByTypePageProps,
): Promise<Metadata> {
    const { serviceType: paramServiceType } = await params;

    const getServiceTypeLabel = (type: ServiceType) => {
         return type.replace(/_/g, ' ');
     };

    const type = paramServiceType.toUpperCase() as ServiceType;


     const formattedType = Object.values(ServiceType).includes(type) ? getServiceTypeLabel(type) : 'Unknown';


    return {
        title: `${formattedType} Services | Management Dashboard`,
        description: `List and manage ${formattedType} servi
```

## `page.tsx`

```ts
// /app/(protected)/services/[serviceType]/[id]/page.tsx
import { Metadata } from "next";
import { ServiceDetails } from "@/components/services/ServiceDetails";
import { getServiceById } from '@/actions/services/get';
// ServiceType import je uklonjen jer nije korišćen direktno u ovom delu koda


interface ServiceDetailsPageProps {
    // params je Promise u async Server Komponentama
    params: Promise<{
        serviceType: string;
        id: string;
    }>;
}

export async function generateMetadata(
    { params }: ServiceDetailsPageProps,
): Promise<Metadata> {
    // Awaitujte params prop
    const resolvedParams = await params;
    // Sada pristupite svojstvima
    const { id: paramId } = resolvedParams;

    const result = await getServiceById(paramId);
    const service = result.data;
    
    return {
        title: service ? `${service.name} Details | Management Dashboard` : "Service Details | Management Dashboard",
        description: service ? `Details for service: ${serv
```

## `page.tsx`

```ts
// /app/(protected)/services/[serviceType]/[id]/edit/page.tsx

import { Metadata } from "next";
import { ServiceForm } from "@/components/services/ServiceForm";
import { getServiceById } from '@/actions/services/get';
import { ServiceType } from "@prisma/client";


interface EditServicePageProps {
    params: {
        serviceType: string;
        id: string;
    };
}

export async function generateMetadata(
    { params }: EditServicePageProps,
): Promise<Metadata> {
    const { id: paramId } = await params;

    const result = await getServiceById(paramId);
    const service = result.data;

    return {
        title: service ? `Edit ${service.name} | Management Dashboard` : "Edit Service | Management Dashboard",
        description: service ? `Edit details for service: ${service.name}` : "Edit service details page.",
    };
}


export default async function EditServicePage({ params }: EditServicePageProps) {

    const { serviceType: urlServiceType, id } = await params;


    const 
```

## `page.tsx`

```ts
// Path: /app/(protected)/services/import/page.tsx
import { Metadata } from "next";
import { ImportForm } from "@/components/services/ImportForm";
import { NewParkingProcessorForm  } from "@/components/services/ParkingServiceProcessorForm"; // Nova komponenta
import { ProviderProcessorForm } from "@/components/services/ProviderProcessorForm";
import { VasServiceProcessorForm } from "@/components/services/VasServiceProcessorForm";

export const metadata: Metadata = {
  title: "Import VAS Data | Management Dashboard",
  description: "Import VAS service usage data from a CSV file.",
};

export default function ImportServicesPage() {
  return (
    <div className="p-6 space-y-6">
      {/* Postojeći kontejner */}
      <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
        <div>
          <h1 className="text-2xl font-bold tracking-tight">Import VAS Service Data</h1>
          <p className="text-gray-500">
            Upload a CSV file to import VAS ser
```

## `page.tsx`

```ts
// Path: app/(protected)/services/new/page.tsx

import { Metadata } from "next";
// UKLONJENO: Import ComplaintFormWrapper
// import { ComplaintFormWrapper } from "@/components/complaints/ComplaintFormWrapper";
// UVEZENO: Import ServiceForm
import { ServiceForm } from "@/components/services/ServiceForm";
// UKLONJENO: Import getProviders jer ServiceForm ne zahteva listu provajdera
// import { getProviders } from "@/actions/complaints/providers";
// UKLONJENO: Import auth jer se ne koristi direktno u ovoj komponenti
// import { auth } from "@/auth";


export const metadata: Metadata = {
  title: "Create New Service",
  description: "Form to create a new service",
};

// Ova Server Komponenta sada samo renderuje ServiceForm
export default async function NewServicePage() {
  // UKLONJENO: Dohvatanje providersData jer ComplaintFormWrapper više nije ovde
  // const providersData = await getProviders();

  return (
    // Renderujemo ServiceForm komponentu
    // ServiceForm ne zahteva prop
```

## `page.tsx`

```ts
"use client";

import * as z from "zod";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";

import { useTransition, useState } from "react";
import { useSession } from "next-auth/react";

import { Input } from "@/components/ui/input";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Card, CardContent, CardHeader } from "@/components/ui/card";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Switch } from "@/components/ui/switch";
import { Button } from "@/components/ui/button";
import { FormError } from "@/components/form-error";
import { FormSuccess } from "@/components/form-success";

import { settings } from "@/actions/settings";
import { SettingsSchema } from "@/schemas";
import { useCurrentUser } from "@/hooks/use-current-user";
import { UserRole } from "@prisma/clie
```

## `route.ts`

```ts
//app/api/admin/route.ts


import { NextResponse } from "next/server";
import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client";

export async function GET() {
  const role = await currentRole();

  if (role === UserRole.ADMIN) {
    return new NextResponse(null, { status: 200 });
  }

  return new NextResponse(null, { status: 403 });
}

```

## `route.ts`

```ts
///app/api/analytics/financials/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { calculateFinancialMetrics } from "@/lib/analytics/financial-calculations";
import { auth } from "@/auth";

export async function GET(request: NextRequest) {
  try {
    // Check authentication
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }
    
    // Get query parameters
    const searchParams = request.nextUrl.searchParams;
    const startDate = searchParams.get("startDate");
    const endDate = searchParams.get("endDate");
    const providerId = searchParams.get("providerId");
    
    // Build query conditions
    const where: any = {};
    
    if (startDate && endDate) {
      where.mesec_pruzanja_usluge = {
        gte: new Date(startDate),
        lte: new Date(endDate),
      };
    }
    
    if (providerId) {
      where.provajderId = providerId
```

## `route.ts`

```ts
///app/api/analytics/sales/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";

export async function GET(request: NextRequest) {
  try {
    // Check authentication
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }
    
    // Get query parameters
    const searchParams = request.nextUrl.searchParams;
    const startDate = searchParams.get("startDate");
    const endDate = searchParams.get("endDate");
    const providerId = searchParams.get("providerId");
    const serviceId = searchParams.get("serviceId");
    
    // Parse time period
    let dateFilter = {};
    if (startDate && endDate) {
      dateFilter = {
        mesec_pruzanja_usluge: {
          gte: new Date(startDate),
          lte: new Date(endDate),
        },
      };
    }
    
    // Build query filters
    const filters: any = { ...dateFilter };
    
```

## `route.ts`

```ts
// app/api/auth/[...nextauth]/route.ts

import { handlers } from "@/auth";
export const { GET, POST } = handlers;

```

## `route.ts`

```ts
// app/api/blacklist/route.ts
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    
    if (!session?.user?.id) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const { searchParams } = new URL(request.url);
    const page = parseInt(searchParams.get('page') || '1');
    const limit = parseInt(searchParams.get('limit') || '10');
    const senderName = searchParams.get('senderName');
    const providerId = searchParams.get('providerId');
    const isActive = searchParams.get('isActive');
    const effectiveDate = searchParams.get('effectiveDate');

    const skip = (page - 1) * limit;

    // Build where clause
    const where: any = {};
    
    if (senderName) {
      where.senderName = {
        contains: senderName,
        mode: 'insensitive'
      };
    }
    
    if (providerId) 
```

## `route.ts`

```ts
///app/api/bulk-services/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { getCurrentUser } from '@/lib/session';
import { getBulkServices } from '@/actions/bulk-services/getBulkServices';
import { createBulkService } from '@/actions/bulk-services/create';

export async function GET(req: NextRequest) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    // Get query parameters
    const url = new URL(req.url);
    const page = Number(url.searchParams.get('page')) || 1;
    const limit = Number(url.searchParams.get('limit')) || 10;
    const providerId = url.searchParams.get('providerId') || undefined;
    const serviceId = url.searchParams.get('serviceId') || undefined;
    const providerName = url.searchParams.get('providerName') || undefined;
    const serviceName = url.searchParams.get('serviceName') || undefined;
    const sortBy = ur
```

## `route.ts`

```ts
//app/api/bulk-services/[id]/route.ts


import { NextRequest, NextResponse } from 'next/server';
import { getCurrentUser } from '@/lib/session';
import { getBulkServiceById } from '@/actions/bulk-services/getBulkServiceById';
import { updateBulkService } from '@/actions/bulk-services/update';
import { deleteBulkService } from '@/actions/bulk-services/delete';

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    const bulkService = await getBulkServiceById(params.id);
    
    if (!bulkService) {
      return NextResponse.json({ error: 'Bulk service not found' }, { status: 404 });
    }
    
    return NextResponse.json(bulkService);
  } catch (error) {
    console.error('[BULK_SERVICE_BY_ID_API]', error);
    return NextResponse.json({ error: 'Internal Server Error' }, { st
```

## `route.ts`

```ts
///app/api/bulk-services/export/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { getCurrentUser } from '@/lib/session';
import { exportBulkServices } from '@/actions/bulk-services/export';

export async function GET(req: NextRequest) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    // Get query parameters for filtering
    const url = new URL(req.url);
    const providerId = url.searchParams.get('providerId') || undefined;
    const serviceId = url.searchParams.get('serviceId') || undefined;
    const providerName = url.searchParams.get('providerName') || undefined;
    const serviceName = url.searchParams.get('serviceName') || undefined;
    
    const result = await exportBulkServices({
      providerId,
      serviceId,
      providerName,
      serviceName,
    });
    
    // Set headers for file download
    const headers = ne
```

## `route.ts`

```ts
///app/api/bulk-services/import/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { getCurrentUser } from '@/lib/session';
import { importBulkServices } from '@/actions/bulk-services/import';

export async function POST(req: NextRequest) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    const formData = await req.formData();
    const file = formData.get('file') as File;
    
    if (!file) {
      return NextResponse.json({ error: 'No file provided' }, { status: 400 });
    }
    
    // Check file type
    if (!file.name.endsWith('.csv')) {
      return NextResponse.json({ error: 'Invalid file format. Only CSV files are supported.' }, { status: 400 });
    }
    
    // Read file content
    const fileContent = await file.text();
    
    // Process the file
    const result = await importBulkServices(fileContent);
    
    return Nex
```

## `route.ts`

```ts
// Path: app/api/complaints/route.ts

import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";


// GET - Get all complaints with filtering and pagination
export async function GET(request: NextRequest) {
  try {
    const session = await auth();

    if (!session || !session.user) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const { searchParams } = new URL(request.url);

    // --- Extract & Parse Filters and Pagination ---
    const filters: any = {}; // Koristimo 'any' jer dinamički gradimo filter

    // Basic filters
    const status = searchParams.get("status");
    if (status) filters.status = status;

    const priority = searchParams.get("priority");
    if (priority) {
        const priorityInt = parseInt(priority);
        if (!isNaN(priorityInt)) filters.priority = priorityInt;
    }

    const service
```

## `route.ts`

```ts
  // /app/api/complaints/[id]/route.ts:

  import { NextRequest, NextResponse } from "next/server";
  import { z } from "zod";
  import { db } from "@/lib/db";
  import { complaintUpdateSchema } from "@/schemas/complaint";
  import { auth } from "@/auth";

  // GET - Get a specific complaint by ID
  export async function GET(
    request: NextRequest,
    { params }: { params: { id: string } }
  ) {
    try {
      const session = await auth();
      
      if (!session || !session.user) {
        return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
      }
      
      const { id } = await params;
      
      const complaint = await db.complaint.findUnique({
        where: { id },
        include: {
          submittedBy: {
            select: {
              id: true,
              name: true,
              email: true
            }
          },
          assignedAgent: {
            select: {
              id: true,
              name: true,
              email: tr
```

## `route.ts`

```ts
// /app/api/complaints/[id]/attachments/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { uploadFile, deleteFile } from "@/lib/storage"; // You'll need to implement this

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }

    const complaintId = params.id;

    // Check if complaint exists
    const complaint = await db.complaint.findUnique({
      where: { id: complaintId },
    });

    if (!complaint) {
      return new NextResponse("Complaint not found", { status: 404 });
    }

    // Check if user has access to this complaint
    const canAccess = 
      session.user.id === complaint.submittedById ||
      session.user.id === complaint.assignedAgentId ||
      ["ADMIN", "MANAGER", "AGENT"].includes((await db.user.find
```

## `route.ts`

```ts
// /app/api/complaints/[id]/comments/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";

const commentSchema = z.object({
  text: z.string().min(1, "Comment text is required"),
  isInternal: z.boolean().default(false),
});

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }

    const complaintId = params.id;

    // Check if complaint exists
    const complaint = await db.complaint.findUnique({
      where: { id: complaintId },
    });

    if (!complaint) {
      return new NextResponse("Complaint not found", { status: 404 });
    }

    // Determine if user can see internal comments
    const user = await db.user.findUnique({
      where: { id: session.user.id },
    });

    const isStaff = user?.role === 
```

## `route.ts`

```ts
// /app/api/complaints/[id]/status/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";
import { ComplaintStatus } from "@prisma/client";

const statusUpdateSchema = z.object({
  status: z.nativeEnum(ComplaintStatus),
  notes: z.string().optional(),
});

export async function PUT(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }

    const complaintId = params.id;
    const body = await req.json();
    
    const validatedData = statusUpdateSchema.safeParse(body);
    
    if (!validatedData.success) {
      return NextResponse.json(validatedData.error.format(), { status: 400 });
    }

    // Check if complaint exists
    const complaint = await db.complaint.findUnique({
      where: { id: complaintId },
    });

    if (!complaint) {

```

## `route.ts`

```ts
// /app/api/complaints/export/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import * as XLSX from 'xlsx';

export async function GET(req: NextRequest) {
  try {
    const session = await auth();
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
    
    // Check if user has permission to export data
    const user = await db.user.findUnique({
      where: { id: session.user.id },
    });
    
    if (!["ADMIN", "MANAGER"].includes(user?.role || "")) {
      return new NextResponse("Not authorized to export data", { status: 403 });
    }

    const searchParams = new URL(req.url).searchParams;
    const format = searchParams.get("format") || "xlsx";
    const startDate = searchParams.get("startDate");
    const endDate = searchParams.get("endDate");
    const status = searchParams.get("status");
    const serviceId = searchParams.get("serviceId");
    const pro
```

## `route.ts`

```ts
// /app/api/complaints/statistics/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { ComplaintStatus } from "@prisma/client";

export async function GET(req: NextRequest) {
  try {
    const session = await auth();
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
    
    // Check if user has permission to view statistics
    const user = await db.user.findUnique({
      where: { id: session.user.id },
    });
    
    if (!["ADMIN", "MANAGER"].includes(user?.role || "")) {
      return new NextResponse("Not authorized to view statistics", { status: 403 });
    }

    const searchParams = new URL(req.url).searchParams;
    const period = searchParams.get("period") || "month"; // day, week, month, year, all
    const startDate = searchParams.get("startDate");
    const endDate = searchParams.get("endDate");
    
    // Calculate date range based on period 
```

## `route.ts`

```ts
// /app/api/contracts/route.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db';
import { ContractStatus, ContractType } from '@prisma/client';
import { addDays } from 'date-fns';

class CacheManager {
  private static instance: CacheManager;
  private cache = new Map<string, CacheEntry>();
  private cleanupInterval: NodeJS.Timeout | null = null;

  private constructor() {
    this.startCleanup();
  }

  static getInstance(): CacheManager {
    if (!CacheManager.instance) {
      CacheManager.instance = new CacheManager();
    }
    return CacheManager.instance;
  }

  private startCleanup() {
    if (this.cleanupInterval) return;
    
    this.cleanupInterval = setInterval(() => {
      const now = Date.now();
      for (const [key, value] of this.cache.entries()) {
        if (now - value.timestamp > value.ttl) {
          this.cache.delete(key);
        }
      }
    }, 60000);

    const cleanup = () => {
    
```

## `route.ts`

```ts
// /app/api/contracts/[id]/route.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta

import { auth } from '@/auth';

// Importovanje Server Akcija za PUT i DELETE
import { updateContract } from '@/actions/contracts/update'; // Akcija koju smo prethodno kreirali
import { deleteContract } from '@/actions/contracts/delete'; // Akcija koju smo prethodno kreirali

// Handler za GET za dohvatanje pojedinačnog ugovora po ID-u
export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const session = await auth();
  if (!session?.user) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  const { id } = params; // Dobijanje ID-a iz URL-a

  // Osnovna validacija ID-a (opciono, Prisma će baciti grešku za nevalidan format)
   if (!id || typeof id !== 'string') {
        return NextResponse.json({ error: 'Inv
```

## `route.ts`

```ts
// /app/api/contracts/[id]/attachments/route.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
// U realnoj aplikaciji, verovatno biste imali middleware ili helper za proveru autentifikacije/autorizacije za /api/ rute
// import { auth } from '@/auth';

// Importovanje Server Akcije za POST
import { addContractAttachment } from '@/actions/contracts/add-attachment'; // Akcija koju smo prethodno kreirali

// Handler za GET za dohvatanje priloga ugovora
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } } // Hvatanje ID-a ugovora iz URL-a
) {
  // U realnoj aplikaciji, dodali biste proveru autentifikacije/autorizacije
  // const session = await auth();
  // if (!session?.user) {
  //   return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  // }

  const { id } = params; // Dobijanje ID-a ugovora

  try {
    
```

## `route.ts`

```ts
// /app/api/contracts/[id]/edit/route.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { auth } from '@/auth';
import { updateContract } from '@/actions/contracts/update';

interface RouteContext {
  params: Promise<{ id: string }>;
}

export async function POST(
  request: NextRequest,
  context: RouteContext
) {
  try {
    // Await the params promise
    const params = await context.params;
    const { id } = params;

    console.log("[API_EDIT_ROUTE] Processing update for contract:", id);

    // Check authentication
    const session = await auth();
    if (!session?.user) {
      console.error("[API_EDIT_ROUTE] No session found");
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }

    // Validate contract ID
    if (!id || typeof id !== 'string') {
      console.error("[API_EDIT_ROUTE] Invalid contract ID:", id);
      return NextResponse.json({ error: 'Invalid contract ID format' }, { status: 40
```

## `route.ts`

```ts
// /app/api/contracts/[id]/reminders/route.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
// U realnoj aplikaciji, verovatno biste imali middleware ili helper za proveru autentifikacije/autorizacije za /api/ rute
// import { auth } from '@/auth';

// Importovanje Server Akcije za POST
import { createContractReminder } from '@/actions/contracts/create-reminder'; // Akcija koju smo prethodno kreirali

// Handler za GET za dohvatanje podsetnika ugovora
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } } // Hvatanje ID-a ugovora iz URL-a
) {
  // U realnoj aplikaciji, dodali biste proveru autentifikacije/autorizacije
  // const session = await auth();
  // if (!session?.user) {
  //   return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  // }

  const { id } = params; // Dobijanje ID-a ugovora

  try {
 
```

## `route.ts`

```ts
// /app/api/contracts/[id]/renewal/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { auth } from '@/auth'; // Updated import path
import { db } from '@/lib/db';
import { ContractRenewalSubStatus } from '@prisma/client';

export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }

    const contractId = params.id;

    const renewal = await db.contractRenewal.findFirst({
      where: { contractId },
      include: {
        attachments: {
          include: {
            uploadedBy: {
              select: { name: true, email: true }
            }
          },
          orderBy: { uploadedAt: 'desc' }
        },
        createdBy: {
          select: { name: true, email: true }
        },
        lastModifiedBy: {
          select: { name: true, email: true }
        }
      },

```

## `route.ts`

```ts
// /app/api/contracts/[id]/renewal/attachments/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { auth } from '@/lib/auth';
import { db } from '@/lib/db';
import { writeFile, mkdir } from 'fs/promises';
import path from 'path';

export async function POST(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }

    const contractId = params.id;
    const formData = await request.formData();
    
    const file = formData.get('file') as File;
    const description = formData.get('description') as string;

    if (!file) {
      return NextResponse.json({ error: 'No file provided' }, { status: 400 });
    }

    // Validacija tipova datoteka
    const allowedTypes = ['application/pdf', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document'];
    if (!allowedTypes.includes(file.type)) 
```

## `route.ts`

```ts
// /app/api/contracts/[id]/renewal/attachments/[attachmentId]/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { auth } from '@/lib/auth';
import { db } from '@/lib/db';
import { unlink } from 'fs/promises';
import path from 'path';

export async function DELETE(
  request: NextRequest,
  { params }: { params: { id: string; attachmentId: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }

    const { id: contractId, attachmentId } = params;

    // Find the attachment
    const attachment = await db.contractRenewalAttachment.findUnique({
      where: { id: attachmentId },
      include: {
        renewal: {
          select: { contractId: true }
        }
      }
    });

    if (!attachment) {
      return NextResponse.json({ error: 'Attachment not found' }, { status: 404 });
    }

    // Verify the attachment belongs to the correct contract
    if
```

## `route.ts`

```ts
// /app/api/contracts/[id]/status/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { auth } from '@/auth'; // Updated import path
import { db } from '@/lib/db';

export async function PUT(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    const contractId = params.id;
    const { status, comments } = await request.json();
    
    // Validate status
    const validStatuses = [
      'ACTIVE',
      'EXPIRED',
      'TERMINATED',
      'PENDING',
      'RENEWAL_IN_PROGRESS',
      'RENEWED'
    ];
    
    if (!validStatuses.includes(status)) {
      return NextResponse.json({ error: 'Invalid status' }, { status: 400 });
    }
    
    // Check if contract exists
    const existingContract = await db.contract.findUnique({
      where: { id: contractId },
      include: {
        renewals:
```

## `route.ts`

```ts
// /app/api/contracts/[id]/services/route.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
// U realnoj aplikaciji, verovatno biste imali middleware ili helper za proveru autentifikacije/autorizacije za /api/ rute
// import { auth } from '@/auth';

// Importovanje Server Akcije za POST
import { addContractService } from '@/actions/contracts/add-service'; // Akcija koju smo prethodno kreirali

// Handler za GET za dohvatanje servisa povezanih sa ugovorom
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } } // Hvatanje ID-a ugovora iz URL-a
) {
  // U realnoj aplikaciji, dodali biste proveru autentifikacije/autorizacije
  // const session = await auth();
  // if (!session?.user) {
  //   return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  // }

  const { id } = params; // Dobijanje ID-a ugovora

  try {
```

## `route.ts`

```ts
// app/api/contracts/[id]/status/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { db } from '@/lib/db';
import { ContractStatus, ContractRenewalSubStatus } from '@prisma/client';

export async function PATCH(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const contractId = params.id;
    const body = await request.json();
    const { newStatus, comments } = body;

    console.log('🔍 API Route - Starting updateContractStatus:', { contractId, newStatus, comments });

    // Validacija input parametara
    if (!contractId || !contractId.trim()) {
      console.error('❌ Invalid contractId:', contractId);
      return NextResponse.json({
        success: false,
        message: 'Contract ID is required'
      }, { status: 400 });
    }

    if (!Object.values(ContractStatus).includes(newStatus)) {
      console.error('❌ Invalid contract status:', newStatus);
      return NextResponse.json({
        success: false,
        message:
```

## `route.ts`

```ts
// app/api/contracts/statistics/expiry/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/lib/auth";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const now = new Date();
    const thirtyDaysFromNow = new Date(now.getTime() + (30 * 24 * 60 * 60 * 1000));
    const sixtyDaysFromNow = new Date(now.getTime() + (60 * 24 * 60 * 60 * 1000));

    // Get total contracts expiring in the next 60 days or already expired
    const totalExpiringContracts = await db.contract.findMany({
      where: {
        OR: [
          {
            endDate: {
              lte: sixtyDaysFromNow
            }
          },
          {
            endDate: {
              lt: now
            }
          }
        ]
      },
      include: {
        renewals: true,
        provider: { se
```

## `route.ts`

```ts
// app/api/contracts/export/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { Prisma } from "@prisma/client";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return NextResponse.json(
        { error: "Unauthorized" },
        { status: 401 }
      );
    }

    const { searchParams } = new URL(request.url);
    
    // Filter parameters (same as main contracts route)
    const status = searchParams.get("status");
    const type = searchParams.get("type");
    const search = searchParams.get("search");
    const expiringWithin = searchParams.get("expiringWithin");
    const includeExpired = searchParams.get("includeExpired") === "true";

    // Build where clause
    const where: Prisma.ContractWhereInput = {};

    if (status && status !== "all") {
      where.status = status as any;
    }

    if (type && type !== "al
```

## `route.ts`

```ts
// app/api/contracts/statistics/expiry/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return NextResponse.json(
        { error: "Unauthorized" },
        { status: 401 }
      );
    }

    const now = new Date();
    const thirtyDaysFromNow = new Date();
    thirtyDaysFromNow.setDate(now.getDate() + 30);
    
    const sixtyDaysFromNow = new Date();
    sixtyDaysFromNow.setDate(now.getDate() + 60);

    // Get all contracts for statistics
    const allContracts = await db.contract.findMany({
      where: {
        // Include all contracts that are expiring within 60 days or already expired
        endDate: {
          lte: sixtyDaysFromNow
        }
      },
      select: {
        id: true,
        type: true,
        status: true,
        endDate: true,
        // Remove ren
```

## `route.ts`

```ts
// app/api/contracts/timeline/expiry/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { startOfMonth, endOfMonth, addMonths, format } from "date-fns";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return NextResponse.json(
        { error: "Unauthorized" },
        { status: 401 }
      );
    }

    const { searchParams } = new URL(request.url);
    const monthsAhead = parseInt(searchParams.get("monthsAhead") || "12");
    const includeExpired = searchParams.get("includeExpired") === "true";
    
    // Calculate date range
    const startDate = includeExpired 
      ? addMonths(new Date(), -3) // Include 3 months back for expired contracts
      : new Date();
    const endDate = addMonths(new Date(), monthsAhead);

    // Fetch contracts within the date range
    const contracts = await db.contract.findMany({
      w
```

## `route.ts`

```ts
// app/api/cron/check-expiring/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { checkExpiringContracts } from '@/actions/contracts/check-expiring';

export async function GET(request: NextRequest) {
  const authHeader = request.headers.get('authorization');
  if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  const result = await checkExpiringContracts();
  return NextResponse.json(result);
}
```

## `route.ts`

```ts
// /app/api/humanitarian-orgs/route.ts

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db';
import { HumanitarianOrgWithDetails, HumanitarianOrgFilterOptions, HumanitarianOrgsApiResponse } from '@/lib/types/humanitarian-org-types';
import { createHumanitarianOrg } from '@/actions/humanitarian-orgs/create';

// Uvozimo funkcije za proveru autentifikacije i uloge
import { auth } from '@/auth'; // Za dobijanje sesije
import { currentRole } from "@/lib/auth"; // Za dobijanje uloge
import { UserRole } from "@prisma/client"; // Uvozimo enum za uloge


// Handler za GET za dohvatanje liste humanitarnih organizacija
export async function GET(request: NextRequest): Promise<NextResponse<HumanitarianOrgsApiResponse | { error: string }>> {
    // Provera da li je korisnik ulogovan
     const session = await auth();
     if (!session?.user) {
       return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
     }

  
```

## `route.ts`

```ts
// /app/api/humanitarian-orgs/[id]/route.ts

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db';
import { HumanitarianOrgWithDetails } from '@/lib/types/humanitarian-org-types';
import { updateHumanitarianOrg } from '@/actions/humanitarian-orgs/update';
import { deleteHumanitarianOrg } from '@/actions/humanitarian-orgs/delete';

// Uvozimo funkcije za proveru autentifikacije i uloge
import { auth } from '@/auth'; // Za dobijanje sesije
import { currentRole } from "@/lib/auth"; // Za dobijanje uloge
import { UserRole } from "@prisma/client"; // Uvozimo enum za uloge


// Handler za GET za dohvatanje pojedinačne humanitarne organizacije po ID-u
export async function GET(
    request: NextRequest,
    { params }: { params: { id: string } }
): Promise<NextResponse<HumanitarianOrgWithDetails | { error: string }>> {
    // Provera da li je korisnik ulogovan
     const session = await auth();
     if (!session?.user) {
   
```

## `route.ts`

```ts
// Path: app/api/humanitarian-orgs/[id]/contracts/route.ts

import { NextRequest, NextResponse } from "next/server";
import { auth } from "@/auth"; // Assuming you have next-auth configured
import { db } from "@/lib/db"; // Assuming you have your Prisma client instance here

// GET - Retrieve all contracts associated with a specific humanitarian organization
export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    // Authenticate the user
    const session = await auth();

    // Check if the user is authenticated
    if (!session || !session.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }

    // Await params before accessing its properties
    const awaitedParams = await params;
    const organizationId = awaitedParams.id;

    // Provera da li je ID validan pre upita ka bazi
    if (!organizationId) {
         console.error("[HUMANITARIAN_ORG_CONTRACTS_GET] Missing organization ID in params");
         retu
```

## `route.ts`

```ts
// Path: app/api/humanitarian-orgs/[id]/services/route.ts

import { NextResponse } from "next/server";
import { db } from "@/lib/db";
import { ServiceType } from "@prisma/client";

export async function GET(
  req: Request,
  { params }: { params: { id: string } }
) {
  try {
    // Pravilno koristimo params.id
    const orgId = params.id;

    if (!orgId) {
      return new NextResponse("Humanitarian organization ID is required", { status: 400 });
    }

    // Proveravamo da li organizacija postoji
    const organization = await db.humanitarianOrg.findUnique({
      where: { id: orgId }
    });

    if (!organization) {
      return new NextResponse("Humanitarian organization not found", { status: 404 });
    }

    // Tražimo servise povezane sa humanitarnom organizacijom kroz ugovore
    const contracts = await db.contract.findMany({
      where: {
        humanitarianOrgId: orgId,
        status: "ACTIVE" // Uzimamo samo aktivne ugovore
      },
      include: {
        services: 
```

## `route.ts`

```ts
// /app/api/humanitarian-renewals/route.ts
import { NextRequest, NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { renewalFiltersSchema, createHumanitarianRenewalSchema } from "@/schemas/humanitarian-renewal";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return NextResponse.json({ error: "Neautorizovan pristup" }, { status: 401 });
    }

    const { searchParams } = new URL(request.url);
    const filters = Object.fromEntries(searchParams.entries());
    
    // Konvertuj string parametre u odgovarajuće tipove
    if (filters.page) filters.page = parseInt(filters.page);
    if (filters.limit) filters.limit = parseInt(filters.limit);

    const validatedFilters = renewalFiltersSchema.safeParse(filters);
    if (!validatedFilters.success) {
      return NextResponse.json(
        { error: "Neispravni parametri", details: validatedFilters.error.flatt
```

## `route.ts`

```ts
// /app/api/humanitarian-renewals/[id]/route.ts
import { NextRequest, NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { updateHumanitarianRenewalSchema } from "@/schemas/humanitarian-renewal";

export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return NextResponse.json({ error: "Neautorizovan pristup" }, { status: 401 });
    }

    const renewal = await db.humanitarianContractRenewal.findUnique({
      where: { id: params.id },
      include: {
        contract: {
          include: {
            humanitarianOrg: true
          }
        },
        humanitarianOrg: true,
        createdBy: {
          select: {
            id: true,
            name: true,
            email: true
          }
        },
        lastModifiedBy: {
          select: {
            id: true,
            name: true,
            emai
```

## `route.ts`

```ts
// /app/api/humanitarian-renewals/statistics/route.ts
import { NextRequest, NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    if (!session?.user?.id) {
      return NextResponse.json({ error: "Neautorizovan pristup" }, { status: 401 });
    }

    // Ukupan broj obnova
    const totalRenewals = await db.humanitarianContractRenewal.count();

    // Obnove u toku (nisu u završnom procesiranju)
    const inProgress = await db.humanitarianContractRenewal.count({
      where: {
        subStatus: {
          not: "FINAL_PROCESSING"
        }
      }
    });

    // Obnove koje čekaju potpis
    const awaitingSignature = await db.humanitarianContractRenewal.count({
      where: {
        subStatus: "AWAITING_SIGNATURE"
      }
    });

    // Završene obnove
    const completed = await db.humanitarianContractRenewal.count({
      where: {
        sub
```

## `route.ts`

```ts
////app/api/notifications/route.ts


import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { NotificationType } from "@prisma/client";

// Schema for creating a notification
const CreateNotificationSchema = z.object({
  title: z.string().min(1).max(255),
  message: z.string().min(1),
  type: z.nativeEnum(NotificationType),
  userId: z.string().cuid(),
  entityType: z.string().optional(),
  entityId: z.string().optional(),
});

// Schema for querying notifications
const GetNotificationsSchema = z.object({
  userId: z.string().cuid().optional(),
  type: z.nativeEnum(NotificationType).optional(),
  isRead: z.boolean().optional(),
  limit: z.number().int().positive().default(20),
  offset: z.number().int().nonnegative().default(0),
});

// GET handler to retrieve notifications
export async function GET(request: NextRequest) {
  try {
    // Check authentication
    const session = await auth();
```

## `route.ts`

```ts
///app/api/notifications/email/route.ts

import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { auth } from "@/auth";
import { prisma } from "@/lib/prisma";
import { sendEmail } from "@/lib/notifications/email-sender";
import { getEmailTemplate } from "@/lib/notifications/templates";
import { NotificationType } from "@prisma/client";

// Schema for sending an email notification
const EmailNotificationSchema = z.object({
  // User IDs to send to (if not providing emails directly)
  userIds: z.array(z.string()).optional(),
  
  // Direct email addresses (use either userIds or emails)
  emails: z.array(z.string().email()).optional(),
  
  // Notification content
  subject: z.string().min(1).max(255),
  templateId: z.string().optional(),
  templateType: z.nativeEnum(NotificationType).optional(),
  templateData: z.record(z.any()).optional(),
  
  // Custom HTML content (used if no template is specified)
  htmlContent: z.string().optional(),
  
  // Option
```

## `route.ts`

```ts
///app/api/notifications/push/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { checkPermission } from "@/actions/security/check-permission";
import { getCurrentUser } from "@/lib/auth";
import { NotificationType, LogSeverity } from "@prisma/client";
import { sendPushNotification } from "@/lib/notifications/in-app-notifier";
import { logActivity } from "@/lib/security/audit-logger";

export async function POST(req: NextRequest) {
  try {
    const currentUser = await getCurrentUser();
    
    if (!currentUser) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
    
    // ISPRAVKA: Promenjen redosled parametara - prvo permission name, pa userId
    const hasPermission = await checkPermission("send_system_notification", currentUser.id);
    
    if (!hasPermission) {
      return new NextResponse("Forbidden", { status: 403 });
    }
    
    const body = await req.json();
    const { userId, title, message, t
```

## `route.ts`

```ts
// app/api/operators/route.ts


import { NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { validateOperator } from "@/lib/operators/validators";

export async function GET(request: Request) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
    
    // Get query parameters
    const url = new URL(request.url);
    const page = parseInt(url.searchParams.get("page") || "1");
    const limit = parseInt(url.searchParams.get("limit") || "10");
    const name = url.searchParams.get("name") || undefined;
    const code = url.searchParams.get("code") || undefined;
    const active = url.searchParams.get("active") !== null 
      ? url.searchParams.get("active") === "true"
      : undefined;
    const sort = url.searchParams.get("sort") || "name";
    const order = url.searchParams.get("order") || "asc";
    
    // Define where clause for filteri
```

## `route.ts`

```ts
// app/api/operators/[id]/route.ts

import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { getServerSession } from "next-auth";

import { db } from "@/lib/db";
import { auth } from "@/lib/auth";
import { operatorSchema } from "@/schemas/operator";

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    
    if (!session) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const { id } = params;

    const operator = await db.operator.findUnique({
      where: { id },
    });

    if (!operator) {
      return NextResponse.json({ error: "Operator not found" }, { status: 404 });
    }

    return NextResponse.json(operator);
  } catch (error) {
    console.error("Error fetching operator:", error);
    return NextResponse.json({ error: "Failed to fetch operator" }, { status: 500 });
  }
}

export async function PATCH(
  req: NextReques
```

## `route.ts`

```ts
// app/api/operators/[id]/contracts/route.ts

import { NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
    
    const operatorId = params.id;
    
    // Check if operator exists
    const operator = await db.operator.findUnique({
      where: { id: operatorId },
    });
    
    if (!operator) {
      return NextResponse.json(
        { error: "Operator not found" },
        { status: 404 }
      );
    }
    
    // Get query parameters for pagination
    const url = new URL(request.url);
    const page = parseInt(url.searchParams.get("page") || "1");
    const limit = parseInt(url.searchParams.get("limit") || "10");
    const status = url.searchParams.get("status") || undefined;
    const sort = url.sea
```

## `route.ts`

```ts
// app/api/operators/contracts/route.ts

import { NextRequest, NextResponse } from "next/server";
import { getServerSession } from "next-auth";

import { db } from "@/lib/db";
import { auth } from "@/lib/auth";

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    
    if (!session) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const { id } = params;

    // Check if operator exists
    const operator = await db.operator.findUnique({
      where: { id },
    });

    if (!operator) {
      return NextResponse.json({ error: "Operator not found" }, { status: 404 });
    }

    // Get all contracts associated with this operator
    const contracts = await db.contract.findMany({
      where: { operatorId: id },
      orderBy: { startDate: "desc" },
      include: {
        provider: {
          select: {
            name: true,
          },
        },
        huma
```

## `route.ts`

```ts
//app/api/parking-services/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { parkingServiceSchema } from "@/lib/parking-services/validators";

export async function GET(req: NextRequest) {
  try {
    const { searchParams } = new URL(req.url);
    const name = searchParams.get("name") || undefined;
    const isActive = searchParams.has("isActive") 
      ? searchParams.get("isActive") === "true" 
      : undefined;
    const page = Number(searchParams.get("page")) || 1;
    const limit = Number(searchParams.get("limit")) || 10;
    const sortBy = searchParams.get("sortBy") || "name";
    const sortOrder = searchParams.get("sortOrder") || "asc";

    // Calculate pagination
    const skip = (page - 1) * limit;

    // Build filter conditions
    const where: any = {};
    
    if (name) {
      where.name = {
        contains: name,
        mode: "insensitive",
      };
    }
    
    if (isActive !=
```

## `route.ts`

```ts
// app/api/parking-services/[id]/route.ts
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/db';

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const service = await prisma.parkingService.findUnique({
      where: { id: params.id },
      include: {
        // Dodajte relacije koje vam trebaju
      }
    });

    if (!service) {
      return NextResponse.json({ error: 'Service not found' }, { status: 404 });
    }

    return NextResponse.json(service);
  } catch (error) {
    console.error('[PARKING_SERVICE_GET]', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}

export async function PATCH(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const body = await request.json();
    
    const updatedService = await prisma.parkingService.update({
      where: { id: params.id },
      data: body
    });

    return N
```

## `route.ts`

```ts
//app/api/parking-services/[id]/contracts/route.ts

import { NextRequest, NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();

    if (!session || !session.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }

    const awaitedParams = await params;
    const parkingServiceId = awaitedParams.id;

    const parkingService = await db.parkingService.findUnique({
      where: {
        id: parkingServiceId,
      },
    });

    if (!parkingService) {
      return new NextResponse("Parking service not found", { status: 404 });
    }

    const contracts = await db.contract.findMany({
      where: {
        parkingServiceId: parkingServiceId,
      },
      include: {
        createdBy: {
          select: {
            id: true,
            name: true,
            email: true,
          }
```

## `route.ts`

```ts
// app/api/parking-services/[id]/reports/route.ts
import { NextResponse } from "next/server";
import { db } from "@/lib/db";

export async function GET(
  req: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  try {
    // Properly await the params promise
    const { id } = await params;
    const parkingServiceId = id;

    if (!parkingServiceId) {
      return NextResponse.json(
        { error: "Missing parking service ID" },
        { status: 400 }
      );
    }

    // Ensure model name matches your Prisma schema
    const reports = await db.parkingReport.findMany({
      where: { parkingServiceId },
      orderBy: { createdAt: "desc" },
      select: {
        id: true,
        title: true,
        createdAt: true,
        status: true,
      },
    });

    return NextResponse.json({ reports });
  } catch (error) {
    console.error("Failed to fetch reports:", error);
    return NextResponse.json(
      { error: "Failed to fetch reports" },
      { status: 500 }
```

## `route.ts`

```ts
// Path: app/api/parking-services/[id]/services/route.ts

import { NextResponse } from "next/server";
import { db } from "@/lib/db";
import { ServiceType } from "@prisma/client";

export async function GET(
  req: Request,
  { params }: { params: { id: string } }
) {
  try {
    // Pravilno koristimo params.id
    const parkingId = params.id;

    if (!parkingId) {
      return new NextResponse("Parking service ID is required", { status: 400 });
    }

    // Proveravamo da li parking servis postoji
    const parkingService = await db.parkingService.findUnique({
      where: { id: parkingId }
    });

    if (!parkingService) {
      return new NextResponse("Parking service not found", { status: 404 });
    }

    // Tražimo servise povezane sa parking servisom kroz ugovore
    const contracts = await db.contract.findMany({
      where: {
        parkingServiceId: parkingId,
        status: "ACTIVE" // Uzimamo samo aktivne ugovore
      },
      include: {
        services: {
         
```

## `route.ts`

```ts
// app/api/parking-services/parking-import/route.ts
import { NextResponse } from "next/server";
import { spawn } from "child_process";
import path from "path";
import fs from "fs/promises";
import { auth } from "@/auth";
import { db } from "@/lib/db";


export async function POST(req: Request) {
  const session = await auth();
  
  if (!session?.user?.email) {
    return NextResponse.json(
      { error: "Niste prijavljeni" },
      { status: 401 }
    );
  }

  try {
    const body = await req.json();
    const userEmail = body.userEmail || session.user.email;
    const uploadedFilePath = body.uploadedFilePath;

    // Look up user
    const user = await db.user.findUnique({
      where: { email: userEmail },
      select: { id: true }
    });

    if (!user) {
      return NextResponse.json(
        { error: "Korisnik nije pronađen u bazi podataka" },
        { status: 404 }
      );
    }

    // Get file info
    let fileInfo = null;
    if (uploadedFilePath) {
      try {
        
```

## `route.ts`

```ts
// app/api/parking-services/rename-file/route.ts
import { NextRequest, NextResponse } from "next/server";
import fs from "fs/promises";
import path from "path";
import { auth } from "@/auth";

export async function POST(request: NextRequest) {
  const session = await auth();

  if (!session?.user?.email) {
    return NextResponse.json({ error: "Niste prijavljeni" }, { status: 401 });
  }

  try {
    const { filePath, parkingServiceId } = await request.json();

    if (!filePath || !parkingServiceId) {
      return NextResponse.json({ error: "Nedostaju podaci" }, { status: 400 });
    }

    const originalName = path.basename(filePath);
    const ext = path.extname(originalName);
    const timestamp = new Date().toISOString().split("T")[0]; // YYYY-MM-DD
    const newFileName = `${parkingServiceId}_${timestamp}${ext}`;

    const inputDir = path.join(process.cwd(), "scripts", "input");
    const newPath = path.join(inputDir, newFileName);

    await fs.rename(filePath, newPath);

    r
```

## `route.ts`

```ts
// app/api/parking-services/typescript-import/route.ts
import { NextResponse } from 'next/server';
import { ParkingServiceProcessor } from '@/scripts/vas-import/ParkingServiceProcessor';
import { auth } from '@/lib/auth';
import { db } from '@/lib/db';
import path from 'path';
import { promises as fs } from 'fs';

export const maxDuration = 300;
export const dynamic = 'force-dynamic';

export async function POST(req: Request) {
  const session = await auth();
  
  if (!session?.user?.email) {
    return NextResponse.json(
      { error: "Authentication required" },
      { status: 401 }
    );
  }

  let processor: ParkingServiceProcessor | null = null;

  try {
    const body = await req.json();
    const { filePath, parkingServiceId } = body;

    // Validacija file path
    if (!filePath) {
      return NextResponse.json(
        { error: "File path is required" },
        { status: 400 }
      );
    }

    // Provera postojanja fajla
    let fileExists = false;
    try {
      awa
```

## `route.ts`

```ts
// app/api/parking-services/typescript-import-stream/route.ts
import { NextResponse } from 'next/server';
import { ParkingServiceProcessor } from '@/scripts/vas-import/ParkingServiceProcessor';
import { db } from '@/lib/db';
import { promises as fs } from 'fs';

export const maxDuration = 300;
export const dynamic = 'force-dynamic';

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const filePath = searchParams.get('filePath');
  const userEmail = searchParams.get('userEmail');

  // Validate inputs
  if (!filePath || !userEmail) {
    return NextResponse.json(
      { error: 'Missing filePath or userEmail' },
      { status: 400 }
    );
  }

  // Provera fajla
  try {
    await fs.access(filePath);
  } catch {
    return NextResponse.json(
      { error: 'File not found' },
      { status: 404 }
    );
  }
  
  // Create a transform stream for SSE
  const stream = new TransformStream();
  const writer = stream.writable.getWriter();
  co
```

## `route.ts`

```ts
// app/api/parking-services/upload/route.ts
import { NextRequest, NextResponse } from "next/server";
import { writeFile } from "fs/promises";
import path from "path";
import { auth } from "@/auth";

export async function POST(request: NextRequest) {
  const session = await auth();
  
  if (!session?.user?.email) {
    return NextResponse.json(
      { error: "Niste prijavljeni" },
      { status: 401 }
    );
  }

  try {
    const data = await request.formData();
    const file: File | null = data.get("file") as unknown as File;
    const userEmail = data.get("userEmail") as string;
    const parkingServiceId = data.get("parkingServiceId") as string | null;

    if (!file) {
      return NextResponse.json(
        { error: "Nije pronađen fajl" },
        { status: 400 }
      );
    }

    // Validate file type
    const allowedTypes = [
      'application/vnd.ms-excel',
      'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    ];

    if (!allowedTypes.includes(fi
```

## `route.ts`

```ts
// /app/api/products/[id]/route.ts

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Direktno korišćenje DB za GET detalja
// Uvozimo Server Akcije za PUT i DELETE operacije
import { updateProduct } from '@/actions/products/update'; // Koristimo ažuriranu akciju za ažuriranje
import { deleteProduct } from '@/actions/products/delete'; // Koristimo ažuriranu akciju za brisanje
// Uvozimo ažuriranu Zod šemu i tip za PUT
import { productSchema, ProductFormData } from '@/schemas/product';
// Uvozimo ažurirani tip za GET detalja
import { ProductWithDetails } from '@/lib/types/product-types';
// Uvozimo auth funkcije za proveru autentifikacije/autorizacije
import { auth } from '@/auth';
import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client";
import { PrismaClientKnownRequestError } from '@prisma/client/runtime/library'; // Uvozimo za rukovanje 404/409


// Handler za GET za dohvatanje poj
```

## `route.ts`

```ts
// app/api/providers/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

export async function GET(req: NextRequest) {
  try {
    // Use getServerSession with authOptions
    const session = await auth();
    const user = session?.user;

    // Authorization check
    if (!session) {
      return NextResponse.json({ error: "Unauthenticated" }, { status: 401 });
    }

    if (![UserRole.ADMIN, UserRole.MANAGER].includes(user?.role as UserRole)) {
      return NextResponse.json({ error: "Forbidden" }, { status: 403 });
    }

    // Parse query parameters
    const searchParams = req.nextUrl.searchParams;
    const page = parseInt(searchParams.get("page") || "1", 10);
    const limit = parseInt(searchParams.get("limit") || "12", 10);
    const skip = (page - 1) * limit;
    
    // Get all parameters
    const searchTerm = searchParams.get("search") || undefined;
   
```

## `route.ts`

```ts
// /app/api/providers/[id]/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

export async function GET(req: NextRequest) {
  try {
    const session = await auth();
    const user = session?.user;

    if (!user || ![UserRole.ADMIN, UserRole.MANAGER].includes(user.role as UserRole)) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 403 });
    }

    const searchParams = req.nextUrl.searchParams;
    const page = Number(searchParams.get("page") || "1");
    const limit = Number(searchParams.get("limit") || "12");
    const search = searchParams.get("search") || undefined;
    const isActive = searchParams.get("isActive")
      ? searchParams.get("isActive") === "true"
      : undefined;
    const hasContracts = searchParams.get("hasContracts") === "true";
    const hasComplaints = searchParams.get("hasComplaints") === "true";
    const sortBy = s
```

## `route.ts`

```ts
// Path: app/api/providers/[id]/bulk-services/route.ts

// Path: app/api/providers/[id]/bulk-services/route.ts

import { NextResponse } from "next/server";
import { db } from "@/lib/db";
import { ServiceType } from "@prisma/client";

export async function GET(
  req: Request,
  { params: { id } }: { params: { id: string } } // IZMENA: Destrukturirano 'id' direktno iz params
) {
  try {
    const providerId = id; // Koristimo destrukturirani 'id'

    if (!providerId) {
      return new NextResponse("Provider ID is required", { status: 400 });
    }

    // Verify that the provider exists
    const provider = await db.provider.findUnique({
      where: { id: providerId },
    });

    if (!provider) {
      return new NextResponse("Provider not found", { status: 404 });
    }

    // Dohvati servise tipa 'BULK' koji su povezani sa ovim provajderom
    // preko relacije ka BulkService modelu.
    const services = await db.service.findMany({
      where: {
        type: ServiceType.BULK,

```

## `route.ts`

```ts
//app/api/providers/[id]/contracts/route.ts


import { NextRequest, NextResponse } from "next/server";
import { auth } from "@/auth"; // Assuming you have next-auth configured
import { db } from "@/lib/db"; // Assuming you have your Prisma client instance here

// GET - Retrieve all contracts associated with a specific provider
export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    // Authenticate the user
    const session = await auth();

    // Check if the user is authenticated
    if (!session || !session.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }

    // Await params before accessing its properties
    const awaitedParams = await params;
    const providerId = awaitedParams.id;

    // Optional: Check if the provider exists before fetching contracts
    // This adds an extra database call but can provide a more specific 404 error
    const provider = await db.provider.findUnique({
       where: { 
```

## `route.ts`

```ts
// app/api/providers/[id]/edit/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";
import { z } from "zod";
import { revalidatePath } from "next/cache";

// Define a schema for the update data
// This should match the fields you expect to update from the frontend form
const updateProviderSchema = z.object({
  name: z.string().min(1, { message: "Name is required." }),
  contactName: z.string().nullable().optional(),
  email: z.string().email({ message: "Invalid email format." }).nullable().optional().or(z.literal("")), // Allow empty string for optional email
  phone: z.string().nullable().optional(),
  address: z.string().nullable().optional(),
  description: z.string().nullable().optional(),
  isActive: z.boolean(),
  imageUrl: z.string().url({ message: "Invalid URL format." }).nullable().optional().or(z.literal("")), // Allow empty string for optional imageUrl
});


```

## `route.ts`

```ts
// app/api/providers/[id]/renew-contract/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

export async function POST(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    const user = session?.user;

    // Провера ауторизације
    if (!user || ![UserRole.ADMIN, UserRole.MANAGER].includes(user.role as UserRole)) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 403 });
    }

    // Тражење провајдера
    const provider = await db.provider.findUnique({
      where: { id: params.id },
      include: {
        contracts: {
          orderBy: { endDate: 'desc' },
          take: 1,
        },
      },
    });

    if (!provider) {
      return NextResponse.json({ error: "Provider not found" }, { status: 404 });
    }

    // Ако провајдер има постојеће уговоре, користити последњи за референц
```

## `route.ts`

```ts
// app/api/providers/[id]/status/route.ts
import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";

export async function PATCH(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const session = await auth();
    const user = session?.user;

    // Провера ауторизације
    if (!user || ![UserRole.ADMIN, UserRole.MANAGER].includes(user.role as UserRole)) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 403 });
    }

    const { isActive } = await req.json();

    // Ажурирање статуса провајдера
    const updatedProvider = await db.provider.update({
      where: { id: params.id },
      data: { isActive },
    });

    return NextResponse.json(updatedProvider);
  } catch (error) {
    console.error("Failed to update provider status:", error);
    return NextResponse.json(
      { error: "Failed to update provider status" },
      { stat
```

## `route.ts`

```ts
// Path: app/api/providers/[id]/vas-services/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { ServiceType } from "@prisma/client";

export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const awaitedParams = await params;
    const providerId = awaitedParams.id;

    if (!providerId) {
      return new NextResponse("Provider ID is required", { status: 400 });
    }

    const provider = await db.provider.findUnique({
      where: { id: providerId },
    });

    if (!provider) {
      return new NextResponse("Provider not found", { status: 404 });
    }

    const services = await db.service.findMany({
      where: {
        type: ServiceType.VAS,
        vasServices: {
          some: {
            provajderId: providerId,
          },
        },
      },
      select: {
        id: true,
        name: true,
        description: true,
      },
      orderBy: {
        name: "asc
```

## `route.ts`

```ts
// app/api/providers/upload/route.ts
import { NextRequest, NextResponse } from "next/server";
import { writeFile } from "fs/promises";
import path from "path";
import { auth } from "@/auth";

export async function POST(request: NextRequest) {
  const session = await auth();
  
  if (!session?.user?.email) {
    return NextResponse.json(
      { error: "Niste prijavljeni" },
      { status: 401 }
    );
  }

  try {
    const data = await request.formData();
    const file: File | null = data.get("file") as unknown as File;
    const userEmail = data.get("userEmail") as string;

    if (!file) {
      return NextResponse.json(
        { error: "Nije pronađen fajl" },
        { status: 400 }
      );
    }

    const allowedTypes = [
      'application/vnd.ms-excel',
      'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    ];

    if (!allowedTypes.includes(file.type) && !file.name.match(/\.(xls|xlsx)$/i)) {
      return NextResponse.json(
        { error: "Dozvoljen
```

## `route.ts`

```ts
// app/api/providers/vas-import/route.ts
import { NextResponse } from "next/server";
import { promises as fs } from 'fs';
import path from 'path';
import { auth } from "@/auth";
import { db } from "@/lib/db";
import { VasImportService } from "@/scripts/vas-import/VasImportService";

const PROJECT_ROOT = process.cwd();
const SCRIPTS_DIR = path.join(PROJECT_ROOT, 'scripts');
const ERROR_FOLDER = path.join(SCRIPTS_DIR, 'errors');

export async function POST(req: Request) {
  const session = await auth();
  
  if (!session?.user?.email) {
    return NextResponse.json(
      { error: "Niste prijavljeni" },
      { status: 401 }
    );
  }

  try {
    const body = await req.json();
    const userEmail = body.userEmail || session.user.email;
    const uploadedFilePath = body.uploadedFilePath;

    const user = await db.user.findUnique({
      where: { email: userEmail },
      select: { id: true }
    });

    if (!user) {
      return NextResponse.json(
        { error: "Korisnik nije prona
```

## `route.ts`

```ts
///app/api/reports/generate/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db"; // Assuming your Prisma client is exported as db
import { auth } from "@/auth"; // Assuming your NextAuth options are here
import { generateExcelReport } from "@/lib/reports/excel-generator"; // Assuming this function exists
import { logActivity } from "@/actions/security/log-event"; // Assuming this action exists
import { ReportStatus } from "@prisma/client"; // Assuming you have a ReportStatus enum in Prisma

export async function POST(request: NextRequest) {
  const session = await auth();
  const userId = session?.user?.id; // Get the user ID from the session

  // Prepare activity log payload
  const activityLogPayload = {
    userId: userId || null, // Log null if user is not authenticated
    entityType: 'Report', // or 'ReportGeneration'
    entityId: null, // Will be set after report is saved to DB
    activityType: 'GENERATE',
    details: {} as any, /
```

## `route.ts`

```ts
// Path: app/api/security/logs/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { LogSeverity } from "@prisma/client";
import { z } from "zod";

const createLogSchema = z.object({
  action: z.string().min(1, "Action is required"),
  entityType: z.string().min(1, "Entity type is required"),
  entityId: z.string().optional().nullable(),
  details: z.string().optional().nullable(),
  severity: z.enum(["INFO", "WARNING", "ERROR", "CRITICAL"]).default("INFO"),
});


// Ažurirana Zod schema za logs filtering sa userName
const logsFilterSchema = z.object({
  action: z.string().nullable().optional(),
  entityType: z.string().nullable().optional(),
  entityId: z.string().nullable().optional(),
  userId: z.string().nullable().optional(),
  userName: z.string().nullable().optional(), // Dodajemo userName u Zod šemu
  severity: z.enum(["INFO", "WARNING", "ERROR", "CRITICAL"]).nullable().optional(),
  startDate: 
```

## `route.ts`

```ts
// Path: app/api/security/performance/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";
import { subHours, subDays, subWeeks, subMonths } from "date-fns";
// Uklonjen import za rateLimit jer se ne koristi Redis
// import { rateLimit } from "@/lib/security/rate-limiter";
import { UserRole } from "@prisma/client";

interface PerformanceDataPoint {
  timestamp: Date;
  responseTime: number;
  requestCount: number;
  errorRate: number;
  cpuUsage: number | null;
  memoryUsage: number | null;
}

const timeRangeSchema = z.enum(["1h", "6h", "24h", "7d", "30d", "all"]).default("24h");

export async function GET(req: NextRequest) {
  try {
    // Uklonjen sav kod za rate limiting jer se ne koristi Redis
    // const identifier = req.ip || "anonymous";
    // const rateLimited = await rateLimit(req, `performance-metrics-api-${identifier}`, { maxRequests: 30, window: 60 });
    // if (!rateLimi
```

## `route.ts`

```ts
// Path: app/api/security/performance/summary/route.ts

import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { z } from "zod";
import { subHours, subDays, subWeeks, subMonths } from "date-fns";
import { rateLimiter } from "@/lib/security/rate-limiter";
import { UserRole } from "@prisma/client";

interface PerformanceSummary {
  totalRequests: number;
  avgResponseTime: number | null;
  errorRate: number | null;
  avgCpuUsage: number | null;
  avgMemoryUsage: number | null;
}

const timeRangeSchema = z.enum(["1h", "6h", "24h", "7d", "30d", "all"]).default("24h");

export async function GET(req: NextRequest) {
  try {
    const identifier = req.ip || "anonymous";
    const rateLimited = await rateLimiter(`performance-summary-api-${identifier}`, 30, 60);

    if (rateLimited) {
      return NextResponse.json(
        { error: "Previše zahteva. Pokušajte ponovo kasnije." },
        { status: 429 }
      );
    }

    
```

## `route.ts`

```ts
///app/api/security/permissions/route.ts


import { NextRequest, NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { UserRole } from "@prisma/client";
import { z } from "zod";

// Define permissions schema based on role
const permissionsByRole = {
  ADMIN: {
    // Admin has all permissions
    contracts: ["view", "create", "update", "delete", "manage"],
    providers: ["view", "create", "update", "delete", "manage"],
    services: ["view", "create", "update", "delete", "manage"],
    complaints: ["view", "create", "update", "delete", "assign", "resolve", "manage"],
    reports: ["view", "create", "schedule", "manage"],
    analytics: ["view", "export", "manage"],
    users: ["view", "create", "update", "delete", "manage"],
    securityLogs: ["view", "purge", "manage"],
    notifications: ["view", "create", "manage"],
    humanitarianOrgs: ["view", "create", "update", "delete", "manage"],
    parkingServices: ["view", "create", "upd
```

## `route.ts`

```ts
// app/api/sender-blacklist/route.ts
// app/api/sender-blacklist/route.ts
import { db } from "@/lib/db";
import { NextResponse } from "next/server";
import { SenderBlacklistWithProvider } from "@/lib/types/blacklist";

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const page = parseInt(searchParams.get("page") || "1");
  const pageSize = parseInt(searchParams.get("pageSize") || "10");
  const senderName = searchParams.get("senderName") || "";
  const providerId = searchParams.get("providerId") || "";
  const isActiveParam = searchParams.get("isActive");
  const dateFromParam = searchParams.get("dateFrom");
  const dateToParam = searchParams.get("dateTo");

  try {
    const skip = (page - 1) * pageSize;
    
    const where: any = {};
    
    // Filter by sender name
    if (senderName) {
      where.senderName = {
        contains: senderName,
        mode: "insensitive",
      };
    }
    
    // Filter by provider ID
    if (provi
```

## `route.ts`

```ts
// /app/api/services/route.ts
'use server'; // API rute mogu biti Server ili Edge, ali interakcija sa bazom sugeriše Server okruženje

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Pretpostavljena putanja do vašeg Prisma klijenta
import { ServiceType } from '@prisma/client'; // Uvozimo enum ServiceType iz Prisma klijenta

// Uvozimo Zod šemu i tip forme za Service ako su definisani (spadaju pod Sekciju 5.5 Schemas)
// import { serviceSchema, ServiceFormData } from '@/schemas/service'; // Pretpostavljena putanja
// Uvozimo Server Akciju za kreiranje servisa ako je definisana (spada pod Sekciju 5.3 Actions)
// import { createService } from '@/actions/services/create'; // Pretpostavljena putanja

// U realnoj aplikaciji, dodali biste proveru autentifikacije/autorizacije
import { auth } from '@/auth';
// import { currentRole } from "@/lib/auth";
// import { UserRole } from "@prisma/client";


// Handler za GET 
```

## `route.ts`

```ts
// /app/api/services/[id]/route.ts

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { db } from '@/lib/db'; // Direktno korišćenje DB za GET detalja
// Uvozimo Server Akcije za PUT i DELETE operacije
import { updateService } from '@/actions/services/update'; // Koristimo ažuriranu akciju za ažuriranje
import { deleteService } from '@/actions/services/delete'; // Koristimo ažuriranu akciju za brisanje
// Uvozimo ažuriranu Zod šemu i tip za PUT
import { serviceSchema, ServiceFormData } from '@/schemas/service';
// Uvozimo ažurirani tip za GET detalja
import { ServiceWithDetails } from '@/lib/types/service-types';
// Uvozimo auth funkcije za proveru autentifikacije/autorizacije
import { auth } from '@/auth';
import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client";

// Handler za GET za dohvatanje pojedinačnog servisa po ID-u
// Dohvata više detalja (sa relacijama) nego GET na /api/services
// Usklađen sa Servic
```

## `route.ts`

```ts
import { NextRequest, NextResponse } from 'next/server';
import { db } from '@/lib/db';
import { auth } from '@/auth';

// Handler for getting bulk services associated with a provider
export async function GET(
  request: NextRequest,
  { params }: { params: { providerId: string } }
) {
  try {
    const session = await auth();
    
    if (!session || !session.user) {
      return new NextResponse(JSON.stringify({ error: 'Unauthorized' }), {
        status: 401,
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    const providerId = params.providerId;
    
    if (!providerId) {
      return new NextResponse(JSON.stringify({ error: 'Provider ID is required' }), {
        status: 400,
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    // Find bulk services associated with this provider
    const services = await db.service.findMany({
      where: {
        type: 'BULK',
        bulkServices: {
          some: {
            pro
```

## `route.ts`

```ts
// /app/api/services/categories/route.ts
// /app/api/services/categories/route.ts
import { NextResponse } from "next/server";
import { db } from "@/lib/db";
import { auth } from "@/auth";
import { ServiceType } from "@prisma/client";

export async function GET() {
  try {
    const session = await auth();
    
    // Enhanced session validation
    if (!session) {
      console.error('No session found');
      return new NextResponse("Unauthorized - No session", { status: 401 });
    }
    
    if (!session.user) {
      console.error('Session found but no user object');
      return new NextResponse("Unauthorized - No user", { status: 401 });
    }
    
    // Use email as fallback identifier if ID is missing
    const userId = session.user.id || session.user.email;
    
    if (!userId) {
      console.error('User object exists but ID and email are missing:', session.user);
      return new NextResponse("Unauthorized - Missing identifier", { status: 401 });
    }

    // Get all serv
```

## `route.ts`

```ts
import { NextRequest, NextResponse } from 'next/server';
import { db } from '@/lib/db';
import { auth } from '@/lib/auth';

// Handler for getting humanitarian services associated with a humanitarian organization
export async function GET(
  request: NextRequest,
  { params }: { params: { orgId: string } }
) {
  try {
    const session = await auth();
    
    if (!session || !session.user) {
      return new NextResponse(JSON.stringify({ error: 'Unauthorized' }), {
        status: 401,
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    const orgId = params.orgId;
    
    if (!orgId) {
      return new NextResponse(JSON.stringify({ error: 'Humanitarian Organization ID is required' }), {
        status: 400,
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    // Find services associated with this humanitarian organization through contracts
    const services = await db.service.findMany({
      where: {
        type: 'HUMANITA
```

## `route.ts`

```ts
// /app/api/services/import/route.ts

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
// Uvozimo ažuriranu Server Akciju za import
import { importServices } from '@/actions/services/import'; // Koristimo ažuriranu akciju
// Uvozimo auth funkcije za proveru autentifikacije/autorizacije
import { auth } from '@/auth';
import { currentRole } from "@/lib/auth";
import { UserRole } from "@prisma/client";

// Handler za POST za import servisa iz CSV-a
// Koristi ažuriranu actions/services/import.ts akciju
export async function POST(request: NextRequest): Promise<NextResponse> { // Vraća NextResponse
    // Provera autorizacije - samo ADMIN ili MANAGER mogu importovati
     const role = await currentRole();
     if (role !== UserRole.ADMIN && role !== UserRole.MANAGER) { // Prilagodite uloge
       return NextResponse.json({ error: 'Forbidden' }, { status: 403 });
     }

    try {
        // Čitanje tela zahteva kao teksta (očekujemo CSV string)
   
```

## `route.ts`

```ts
import { NextRequest, NextResponse } from 'next/server';
import { db } from '@/lib/db';
import { auth } from '@/auth';

// Handler for getting parking services associated with a parking service provider
export async function GET(
  request: NextRequest,
  { params }: { params: { parkingId: string } }
) {
  try {
    const session = await auth();
    
    if (!session || !session.user) {
      return new NextResponse(JSON.stringify({ error: 'Unauthorized' }), {
        status: 401,
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    const parkingId = params.parkingId;
    
    if (!parkingId) {
      return new NextResponse(JSON.stringify({ error: 'Parking Service ID is required' }), {
        status: 400,
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    // Find services associated with this parking service provider
    const services = await db.service.findMany({
      where: {
        type: 'PARKING',
        contracts: {

```

## `route.ts`

```ts
// Path: app/api/test/route.ts

import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";

// Definisimo jednostavnu Zod šemu sa opcionim poljima
const testParamsSchema = z.object({
    // Dva opciona string polja
    param1: z.preprocess(val => (val === null || val === "" ? undefined : val), z.string()).optional(),
    param2: z.preprocess(val => (val === null || val === "" ? undefined : val), z.string()).optional(),

    // Jedno opciono brojčano polje sa default vrednošću (ako je prisutno u URL-u)
    optionalNumber: z.coerce.number().optional().default(0),

    // Jedno opciono boolean polje (true/false)
    isEnabled: z.preprocess(val => {
        if (val === "true") return true;
        if (val === "false") return false;
        return undefined; // Tretiraj sve ostalo kao undefined
    }, z.boolean()).optional(),
});

// GET metoda - samo prima parametre i validira ih
export async function GET(request: NextRequest) {
    try {
        const url = new URL(
```

## `route.ts`

```ts
// app/api/users/route.ts
import { NextResponse } from "next/server";
import { auth } from "@/auth";
import { db } from "@/lib/db";

export async function GET() {
  try {
    const session = await auth();
    
    // Check authentication
    if (!session || !session.user) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
    
    // Only allow admins or agents to fetch user list
    if (session.user.role !== "ADMIN") {
      return new NextResponse("Forbidden", { status: 403 });
    }
    
    // Fetch users (bez in filtera koji koristi nevalidne vrednosti)
    const users = await db.user.findMany({
      select: {
        id: true,
        name: true,
        role: true // Dodajemo role za filtriranje na frontend-u
      },
      orderBy: {
        name: 'asc'
      }
    });
    
    // Za svaki slučaj možemo ovde filtrirati korisnike sa AGENT ulogom
    // ako postoji u vašem sistemu
    const agentUsers = users.filter(user => user.role === "ADMIN", "AGENT");
 
```

## `route.ts`

```ts
// app/api/users/[id]/route.ts

import { getUserById } from "@/data/user";
import { NextResponse } from "next/server";

// Named export for GET method
export async function GET(req: Request, { params }: { params: Promise<{ id: string }> }) {
  try {
    // Await the params to resolve
    const resolvedParams = await params;

    const user = await getUserById(resolvedParams.id);

    if (!user) {
      return NextResponse.json({ error: "User not found" }, { status: 404 });
    }

    return NextResponse.json({ name: user.name });
  } catch (error) {
    console.error("Failed to fetch user:", error);
    return NextResponse.json({ error: "Internal Server Error" }, { status: 500 });
  }
}
```

## `route.ts`

```ts
// app/api/vas-services/postpaid-import-stream/route.ts
import { NextResponse } from 'next/server';
import { PostpaidServiceProcessor } from '@/scripts/vas-import/PostpaidServiceProcessor';
import { db } from '@/lib/db';
import { promises as fs } from 'fs';

export const maxDuration = 300;
export const dynamic = 'force-dynamic';

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const filePath = searchParams.get('filePath');
  const userEmail = searchParams.get('userEmail');

  // Validate inputs
  if (!filePath || !userEmail) {
    return NextResponse.json(
      { error: 'Missing filePath or userEmail' },
      { status: 400 }
    );
  }

  // Check file existence
  try {
    await fs.access(filePath);
  } catch {
    return NextResponse.json(
      { error: 'File not found' },
      { status: 404 }
    );
  }

  // Create SSE transform stream
  const stream = new TransformStream();
  const writer = stream.writable.getWriter();
  const e
```

## `route.ts`

```ts
// app/api/vas-services/upload/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { auth } from '@/auth';
import { db } from '@/lib/db';
import path from 'path';
import fs from 'fs/promises';
import { randomUUID } from 'crypto';

// Simple MIME type mapping for Excel files
const EXCEL_MIME_TYPES: Record<string, string> = {
  '.xls': 'application/vnd.ms-excel',
  '.xlsx': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
};

export async function POST(request: NextRequest) {
  try {
    // Authentication check
    const session = await auth();
    if (!session || !session.user?.email) {
      return NextResponse.json(
        { error: 'Morate biti prijavljeni da biste uploadovali fajlove' },
        { status: 401 }
      );
    }

    // Parse form data
    const formData = await request.formData();
    const file = formData.get('file') as File | null;
    const userEmail = formData.get('userEmail') as string | null;

    // Validate inputs
    i
```

## `layout.tsx`

```ts
export default function AuthLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="h-full flex items-center justify-center">{children}</div>
  );
}

```

## `page.tsx`

```ts
import { ErrorCard } from "@/components/auth/error-card";

export default function ErrorPage() {
  return <ErrorCard />;
}

```

## `page.tsx`

```ts
import { Suspense } from "react";
import { LoginForm } from "@/components/auth/login-form";
import { Loader2 } from "lucide-react";

export default function LoginPage() {
  return (
    <Suspense fallback={<Loader2 className="h-5 w-5 animate-spin" />}>
      <LoginForm />
    </Suspense>
  );
}

```

## `page.tsx`

```ts
import { Suspense } from "react";
import { NewPasswordForm } from "@/components/auth/new-password-form";
import { Loader2 } from "lucide-react";

export default function NewVerificationPage() {
  return (
    <Suspense fallback={<Loader2 className="h-5 w-5 animate-spin" />}>
      <NewPasswordForm />
    </Suspense>
  );
}

```

## `page.tsx`

```ts
import { Suspense } from "react";
import { NewVerifictionForm } from "@/components/auth/new-verification-form";
import { Loader2 } from "lucide-react";

export default function NewVerificationPage() {
  return (
    <Suspense fallback={<Loader2 className="h-5 w-5 animate-spin" />}>
      <NewVerifictionForm />
    </Suspense>
  );
}

```

## `page.tsx`

```ts
import { Suspense } from "react";
import { RegisterForm } from "@/components/auth/register-form";
import { Loader2 } from "lucide-react";

export default function RegisterPage() {
  return (
    <Suspense fallback={<Loader2 className="h-5 w-5 animate-spin" />}>
      <RegisterForm />
    </Suspense>
  );
}

```

## `page.tsx`

```ts
import { ResetForm } from "@/components/auth/reset-form";

export default function ResetPage() {
  return <ResetForm />;
}

```

## `page.tsx`

```ts
// /app/test/[testParam]/page.tsx

import { Metadata } from "next";

interface TestPageProps {
    params: {
        testParam: string; // Dinamički parametar iz URL-a
    };
}

// Async pomoćna (helper) funkcija za "siguran" pristup svojstvu parametra
// Cilj je da se zadovolji "params should be awaited" provera
// uvođenjem eksplicitne async granice pre pristupa svojstvu.
// Iako je `p.testParam` sam po sebi sinhron, `await` na poziv
// funkcije bi mogao da pruži potreban kontekst razrešenja za Next.js 15+.
async function getTestParamValue(p: { testParam: string }): Promise<string> {
    // Pristup svojstvu unutar async helpera
    return p.testParam;
}


// Generisanje metapodataka
export async function generateMetadata(
    { params }: TestPageProps,
): Promise<Metadata> {
    // Koristimo async helper funkciju i await-ujemo njen rezultat
    const paramValue = await getTestParamValue(params);

    return {
        title: `Test Page | Param: ${paramValue}`,
        description: `Te
```


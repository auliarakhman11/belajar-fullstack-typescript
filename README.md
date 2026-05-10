# Peta Jalan (Roadmap) Pembuatan POS Multi-Outlet Enterprise

Materi ini disusun untuk membangun aplikasi Point of Sale (POS) dengan arsitektur modern menggunakan Next.js, Express.js (TypeScript), PostgreSQL, dan pendekatan Offline-First.

## BAB 1: Fundamental & Arsitektur Sistem

- [1.1. Konsep Pemisahan Monolith ke API-Driven](./01-Fundamental-dan-Arsitektur/1.1-Konsep-Arsitektur.md)
- [1.2. Anatomi JWT: Access Token, Refresh Token, dan Keamanan Stateless](./01-Fundamental-dan-Arsitektur/1.2-Anatomi-JWT.md)
- [1.3. Setup Environment: PostgreSQL, Node.js, dan Repositori Git](./01-Fundamental-dan-Arsitektur/1.3-Setup-Environment.md)

## BAB 2: Desain Database Relasional Multi-Outlet

- [2.1. Teori Normalisasi Database untuk Skala Multi-Cabang](./02-Desain-Database/2.1-Teori-Normalisasi.md)

- [2.2. Pemodelan Skema Prisma (User, Outlet, Role, Product, Transaction)](./02-Desain-Database/2.2-Pemodelan-Skema-Prisma.md)

* 2.3. Eksekusi Migrasi dan Prisma Studio

## BAB 3: Backend Foundation (Express.js & TypeScript)

- 3.1. Setup Express.js dengan TypeScript Compiler
- 3.2. Implementasi Clean Architecture (Routes, Controllers, Services)
- 3.3. Pembuatan Middleware Global (Error Handling & JWT Validation)

## BAB 4: Modul Authentication & Authorization

- 4.1. Pembuatan Endpoint Login & Register
- 4.2. Logika Rotasi Token (Refresh Token) di HTTP-Only Cookies
- 4.3. Implementasi Role-Based Access Control (RBAC) untuk Super User & Admin

## BAB 5: Frontend Foundation (Next.js)

- 5.1. Struktur Folder Next.js (App Router), `components/`, dan `features/`
- 5.2. Setup Tailwind CSS, shadcn/ui, dan Tema Global
- 5.3. Konfigurasi Axios Interceptor untuk Auto-Refresh JWT

## BAB 6: Modul Dashboard Admin (Online)

- 6.1. Layouting Multi-Role (Sidebar, Header Dinamis)
- 6.2. Manajemen Master Data (Produk, User, Outlet, Kasir) dengan React Hook Form
- 6.3. Visualisasi Data Penjualan (Grafik & Report)

## BAB 7: Modul POS & Transaksi Kasir (Inti Sistem)

- 7.1. UI/UX Kasir: State Management Buka/Tutup Shift
- 7.2. Implementasi Shopping Cart dengan Zustand
- 7.3. Optimasi UI: Lazy Loading Gambar Produk menggunakan Next/Image

## BAB 8: Sinkronisasi Offline-First

- 8.1. Teori Service Worker dan Konsep Offline Web App
- 8.2. Menyimpan Transaksi ke IndexedDB saat Offline
- 8.3. Membuat Queue System untuk Auto-Sync ke Backend saat Online

## BAB 9: Integrasi Eksternal (Hardware & API)

- 9.1. Koneksi Web Bluetooth API untuk Thermal Printer (ESC/POS Commands)
- 9.2. Format Struk dan Logika Print Klien
- 9.3. Integrasi WhatsApp URL Scheme / API untuk Kirim Link Invoice

## BAB 10: Finalisasi & Deployment

- 10.1. Audit Performa dan Security
- 10.2. Deployment Backend & Database ke VPS
- 10.3. Deployment Frontend ke Vercel/VPS

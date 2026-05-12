# 🚀 Dokumentasi Pembangunan POS Enterprise

Sistem Point of Sale Multi-Cabang | Fullstack TypeScript | PostgreSQL

---

## 📚 Daftar Isi (E-Book)

### **Bagian 1: Fondasi & Persiapan**

#### **Bab 1: Arsitektur & Spesifikasi Sistem**

- [1.1 Gambaran Umum Aplikasi POS & Admin](./docs/01-01-gambaran-umum.md)
- [1.2 Memahami Alur Kerja Multi-Cabang & Multi-Role](./docs/01-02-alur-kerja.md)
- [1.3 Analisis Tech Stack](./docs/01-03-tech-stack.md)

#### **Bab 2: Mastering PostgreSQL dari Nol**

- [2.1 Instalasi PostgreSQL & Setup Environment](./docs/02-01-instalasi-db.md)
- [2.2 Berkenalan dengan DBeaver (GUI Database)](./docs/02-02-setup-dbeaver.md)
- [2.3 Dasar-Dasar SQL (Create, Table, Querying)](./docs/02-03-dasar-sql.md)
- [2.4 Perancangan Database (ERD)](./docs/02-04-perancangan-erd.md)

---

### **Bagian 2: Backend Development (Express.js & TypeScript)**

#### **Bab 3: Inisiasi Proyek Backend**

- [3.1 Setup Project Express.js & TypeScript](./docs/03-01-setup-express-ts.md)
- [3.2 Struktur Folder Berbasis Domain](./docs/03-02-struktur-folder.md)
- [3.3 Manajemen Environment Variable (.env)](./docs/03-03-manajemen-env.md)

#### **Bab 4: Data Modeling dengan Prisma ORM**

- [4.1 Instalasi & Inisialisasi Prisma](./docs/04-01-inisialisasi-prisma.md)
- [4.2 Menulis Schema Prisma untuk POS](./docs/04-02-prisma-schema.md)
- [4.3 Database Migration (Deploy Schema)](./docs/04-03-migration.md)
- [4.4 Seeding Data Dummy](./docs/04-04-seeding.md)

#### **Bab 5: Keamanan & Autentikasi JWT**

- [5.1 Implementasi Register & Login](./docs/05-01-register-login.md)
- [5.2 Mekanisme Access & Refresh Token](./docs/05-02-jwt-mechanism.md)
- [5.3 Middleware Role-Based Access Control (RBAC)](./docs/05-03-rbac-middleware.md)

#### **Bab 6: API Core: Manajemen Master Data**

- [6.1 CRUD Cabang, Kategori, dan Produk](./docs/06-01-crud-master.md)
- [6.2 Manajemen User & Hak Akses Cabang](./docs/06-02-user-management.md)
- [6.3 Implementasi Upload Gambar](./docs/06-03-upload-image.md)

#### **Bab 7: API Core: Inventory & Transaksi**

- [7.1 Logika Mutasi Stok (Stock In/Out)](./docs/07-01-inventory-logic.md)
- [7.2 Engine Transaksi & Kalkulasi Harga](./docs/07-02-transaction-engine.md)
- [7.3 Integrasi Fonnte WA Gateway](./docs/07-03-fonnte-integration.md)
- [7.4 Testing API dengan Postman/Thunder Client](./docs/07-04-api-testing.md)

---

### **Bagian 3: Frontend Development (Next.js & Tailwind)**

#### **Bab 8: Inisiasi Proyek Frontend**

- [8.1 Instalasi Next.js & Tailwind CSS](./docs/08-01-setup-nextjs.md)
- [8.2 Konfigurasi Axios & Interceptors](./docs/08-02-axios-config.md)
- [8.3 Setup State Management dengan Zustand](./docs/08-03-zustand-setup.md)

#### **Bab 9: Membangun UI Admin Dashboard**

- [9.1 Layout Sidebar & Navbar Responsif](./docs/09-01-admin-layout.md)
- [9.2 Reusable Components (UI Library)](./docs/09-02-reusable-components.md)
- [9.3 Visualisasi Data dengan Chart.js](./docs/09-03-data-visualization.md)

#### **Bab 10: Membangun Smart POS Client**

- [10.1 UI/UX Kasir: Search & Grid View](./docs/10-01-pos-ui.md)
- [10.2 Manajemen Keranjang Belanja (Cart Logic)](./docs/10-02-cart-logic.md)
- [10.3 Fitur Offline-First dengan IndexedDB](./docs/10-03-offline-mode.md)
- [10.4 Sinkronisasi Otomatis ke Backend](./docs/10-04-sync-logic.md)

#### **Bab 11: Hardware & Integrasi Akhir**

- [11.1 Cetak Struk via Bluetooth Printer](./docs/11-01-bluetooth-print.md)
- [11.2 Fitur Kamera (Selfie & Outlet)](./docs/11-02-camera-api.md)
- [11.3 Final Review & Performance Audit](./docs/11-03-final-review.md)

---

### **Bagian 4: Penutup**

- [12.1 Tips Deployment (VPS/Vercel)](./docs/12-01-deployment.md)
- [12.2 Langkah Pengembangan Selanjutnya](./docs/12-02-future-plan.md)

# 1.3 Analisis Tech Stack

Pemilihan teknologi (stack) untuk proyek ini didasarkan pada tiga pilar utama: **Keamanan Data**, **Kecepatan Pengembangan**, dan **Skalabilitas**. Berikut adalah alasan mendalam mengapa kita menggunakan kombinasi TypeScript, Express, Next.js, dan PostgreSQL.

## 1. Database: PostgreSQL
PostgreSQL bukan sekadar database relasional biasa; ia adalah database tingkat *enterprise*.
*   **Integritas Data (ACID Compliance):** Menjamin bahwa transaksi keuangan (pembayaran kasir) tersimpan dengan sempurna atau tidak sama sekali (mencegah data gantung).
*   **Keamanan UUID:** Kita menggunakan UUID (*Universally Unique Identifier*) sebagai Primary Key, bukan ID angka (1, 2, 3). Ini sangat penting untuk fitur **Offline-First**, karena mencegah konflik ID ketika dua cabang berbeda membuat transaksi di saat yang sama tanpa koneksi internet.

## 2. Backend: Express.js dengan TypeScript
Express.js adalah standar industri untuk Node.js, namun penambahan **TypeScript** adalah pengubah permainan (*game changer*).
*   **Type Safety:** Kita dapat menangkap kesalahan (*error*) saat proses *coding*, bukan saat aplikasi sudah di tangan kasir. Contoh: Mencegah kita memasukkan teks ke dalam kolom harga.
*   **Maintainability:** Dengan struktur folder yang rapi dan fitur *interface/type*, kode menjadi sangat mudah dibaca meskipun sudah mencapai ribuan baris.

## 3. ORM: Prisma
Prisma bertindak sebagai jembatan cerdas antara kode Express.js dan database PostgreSQL.
*   **Auto-Generated Client:** Prisma secara otomatis mengenali tabel di database dan memberikan saran kode (*auto-complete*) di VS Code kita.
*   **Migration System:** Memudahkan kita mengubah struktur tabel database (misal: menambah kolom diskon) hanya melalui file skema, tanpa perlu menyentuh query SQL yang rumit.

## 4. Frontend: Next.js & Tailwind CSS
Next.js (App Router) memberikan kekuatan React dengan performa yang dioptimasi.
*   **Server & Client Components:** Kita bisa memproses laporan berat di sisi server (cepat) dan interaksi kasir di sisi client (responsif).
*   **Tailwind CSS:** Memungkinkan kita membangun UI Kasir yang modern dan kustom tanpa harus menulis file CSS ribuan baris secara manual.

## 5. Keamanan & Validasi: JWT & Zod
*   **JWT (JSON Web Token):** Standar keamanan untuk komunikasi API yang *stateless*. Kita akan menggunakan mekanisme *Access Token* dan *Refresh Token* agar user tidak sering *logout* secara paksa namun tetap aman.
*   **Zod:** Library validasi yang memastikan data yang dikirim oleh user (misal: email atau nominal uang) benar-benar valid sebelum diproses oleh database.

---
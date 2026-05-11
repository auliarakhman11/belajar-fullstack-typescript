# 1.1 Gambaran Umum Aplikasi POS & Admin

Aplikasi **Point of Sale (POS) & Admin Management** ini dirancang sebagai solusi *enterprise* untuk mengelola bisnis retail atau *food & beverage* yang memiliki banyak cabang. Fokus utama dari sistem ini adalah menjembatani kecepatan transaksi di sisi kasir dengan akurasi pengawasan di sisi manajemen pusat.

## Visi Proyek
Membangun ekosistem aplikasi yang tangguh (*robust*), memiliki keamanan data yang tinggi, dan mampu beroperasi dalam kondisi internet yang tidak stabil (*offline-first*).

## Komponen Arsitektur Utama

Sistem ini terdiri dari tiga pilar utama yang saling terintegrasi:

1.  **Backend API (The Brain):**
    *   Dibangun menggunakan **Express.js** dan **TypeScript**.
    *   Mengelola seluruh logika bisnis, perhitungan transaksi, mutasi stok, dan keamanan (Autentikasi).
    *   Bertindak sebagai sumber data tunggal (*Single Source of Truth*) bagi aplikasi frontend.

2.  **Admin Dashboard (The Management):**
    *   Dibangun menggunakan **Next.js**.
    *   Digunakan oleh Super User dan Admin untuk mengontrol data master (Produk, Cabang, User) dan memantau performa bisnis melalui grafik analitik.

3.  **POS Client (The Operation):**
    *   Dibangun menggunakan **Next.js** (dioptimasi untuk penggunaan kasir).
    *   Fokus pada *User Experience* (UX) yang cepat, mendukung pemindaian barcode, integrasi printer bluetooth, dan penyimpanan data lokal sementara (IndexedDB) untuk fitur *offline*.

## Tujuan Fungsional
*   **Sentralisasi Data:** Seluruh data dari berbagai cabang terkumpul di satu database PostgreSQL pusat.
*   **Otomasi Inventaris:** Setiap transaksi yang berhasil secara otomatis memicu mutasi stok pada cabang yang bersangkutan.
*   **Validasi Operasional:** Adanya fitur dokumentasi foto untuk memastikan kesiapan operasional cabang setiap harinya.
*   **Komunikasi Pelanggan:** Pengiriman struk digital secara otomatis melalui WhatsApp untuk meningkatkan kepuasan pelanggan dan efisiensi kertas.

---
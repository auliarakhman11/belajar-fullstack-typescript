# 1.2 Memahami Alur Kerja Multi-Cabang & Multi-Role

Sistem ini dirancang untuk menangani struktur organisasi yang kompleks, di mana satu entitas bisnis bisa memiliki banyak titik penjualan (cabang). Kunci dari sistem ini adalah **Isolasi Data** dan **Hierarki Hak Akses**.

## Struktur Multi-Cabang

Setiap data di dalam database (transaksi, stok, user kasir) akan selalu terikat pada sebuah `branch_id`. Hal ini memastikan:
*   Kasir di Cabang A tidak bisa melihat transaksi di Cabang B.
*   Stok barang dikelola secara independen di tiap lokasi.
*   Laporan penjualan dapat difilter per cabang maupun dikonsolidasi secara global.

## Definisi Role & Hak Akses

Kita akan mengimplementasikan **Role-Based Access Control (RBAC)** dengan pembagian peran sebagai berikut:

| Peran (Role) | Ruang Lingkup Cabang | Fitur Utama |
| :--- | :--- | :--- |
| **Super User** | Global (Semua Cabang) | Manajemen Cabang, Manajemen User, Pengaturan Sistem, Laporan Global, Mutasi Stok antar cabang. |
| **Admin** | Multi-Cabang (Sesuai Izin) | Manajemen Produk, Manajemen Stok, Monitoring Penjualan Cabang tertentu, Audit transaksi. |
| **Kasir** | Single Cabang (Hanya 1) | Buka/Tutup Kasir, Transaksi Penjualan, Cetak Struk, Foto Dokumentasi Outlet. |

## Alur Kerja Operasional (Business Flow)

### 1. Fase Persiapan (Super User/Admin)
*   Mendaftarkan cabang-cabang baru.
*   Menginput data master produk dan kategori.
*   Mengalokasikan stok awal ke masing-masing cabang melalui fitur **Stock In**.

### 2. Fase Operasional (Kasir)
*   **Buka Kasir:** Kasir melakukan *selfie* dan memotret kondisi outlet. Kasir menginput modal awal laci kasir.
*   **Transaksi:** Kasir memilih produk, menginput data pelanggan (WA), dan memproses pembayaran.
*   **Finalisasi:** Struk dicetak via Bluetooth dan dikirim otomatis via WhatsApp Gateway.

### 3. Fase Penutupan & Monitoring (Kasir & Admin)
*   **Tutup Kasir:** Kasir menghitung total uang fisik dan melakukan rekonsiliasi di aplikasi.
*   **Laporan:** Admin memantau grafik penjualan dan sisa stok secara *real-time* melalui Dashboard untuk menentukan kapan harus melakukan restok.

---
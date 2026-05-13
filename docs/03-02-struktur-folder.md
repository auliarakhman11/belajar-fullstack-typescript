Baik, mari kita lanjutkan menyusun materi *e-book* atau dokumentasi Anda. Setelah pondasi *server* dan *environment variable* berdiri kokoh di sub-bab 3.1, sekarang saatnya kita menata "ruangan" di dalam proyek kita.

Silakan buat file **`docs/03-02-struktur-folder.md`** dan salin materi berikut ini. Materi ini sudah disesuaikan agar alurnya selaras dengan standar *Enterprise* yang baru saja kita terapkan.

---

```markdown
# 3.2 Struktur Folder Berbasis Domain (Layered Architecture)

Dalam membangun sistem POS skala menengah hingga besar, menaruh semua logika program di dalam satu atau dua file (seperti pola MVC tradisional yang terlalu sederhana) akan membuat kode sulit dipelihara. 

Oleh karena itu, kita menerapkan **Layered Architecture** (Arsitektur Berlapis) yang memisahkan tanggung jawab setiap bagian kode. Struktur ini akan sangat memudahkan penyusunan bab implementasi di laporan Tugas Akhir Anda nanti, sekaligus menjadi standar yang kokoh untuk portofolio proyek profesional.

## 1. Hierarki Direktori Utama

Seluruh kode aplikasi kita akan berada di dalam folder `src/`. Berikut adalah peta struktur folder yang kita gunakan:

```text
src/
├── config/         # Pengaturan global (Database, Environment, Cloudinary)
├── controllers/    # Menerima HTTP Request dan mengirimkan HTTP Response
├── interfaces/     # Definisi tipe data kustom (TypeScript Types/Interfaces)
├── middlewares/    # Penengah proses (Cek Token JWT, Error Handling otomatis)
├── routes/         # Definisi URL Endpoint API (Misal: /api/v1/products)
├── services/       # Otak Aplikasi (Berisi seluruh logika bisnis & pemanggilan database)
├── utils/          # Fungsi bantuan modular (Format Rupiah, Generator ID acak)
├── validations/    # Skema validasi input menggunakan Zod
└── index.ts        # Entry point (titik awal) jalannya server API

```

## 2. Mengapa Memisahkan Controllers dan Services?

Ini adalah konsep paling krusial dalam arsitektur yang kita bangun:

* **Controllers (Sebagai Resepsionis):**
Tugas controller hanyalah menerima data dari klien (kasir/admin), memvalidasi formatnya menggunakan skema dari folder `validations/`, lalu meneruskan data tersebut ke *Service*. Controller **tidak boleh** melakukan perhitungan kompleks atau query langsung ke database. Setelah *Service* selesai bekerja, Controller bertugas meracik respons JSON untuk dikembalikan ke klien.
* **Services (Sebagai Koki/Dapur):**
Di sinilah seluruh **logika bisnis** terjadi. Saat kasir melakukan checkout, *Service* bertugas memotong stok barang, menghitung total harga, mengecek diskon yang berlaku, dan menyimpan transaksi ke database menggunakan Prisma Client.

## 3. Alur Kerja Aplikasi (Data Flow)

Untuk memahami bagaimana folder-folder ini saling berinteraksi, mari kita lihat siklus hidup sebuah *request* (misalnya: menambahkan produk baru):

1. **Client** (Browser/Aplikasi Mobile) mengirim *request* POST ke `/api/v1/products`.
2. **Routes** menangkap URL tersebut dan mengarahkannya ke `ProductController`.
3. Sebelum sampai ke Controller, **Middlewares** mengecek apakah *user* sudah login dan memiliki akses sebagai Admin.
4. **Controllers** menerima data (nama barang, harga), lalu memvalidasinya dengan skema dari **Validations**.
5. Jika valid, Controller memanggil `ProductService.createProduct()`.
6. **Services** mengeksekusi logika (mengecek apakah kode barang sudah ada), lalu menggunakan `prisma` (dari **config**) untuk menyimpannya ke database PostgreSQL.
7. **Services** mengembalikan hasil ke **Controllers**.
8. **Controllers** mengirimkan respons sukses (Status 201) kembali ke **Client**.

## 4. Keuntungan Arsitektur Ini

1. **Mudah Diuji (Testable):** Karena *Service* berdiri sendiri, kita bisa menguji logika bisnis (seperti perhitungan kembalian uang) tanpa harus menjalankan server Express sama sekali.
2. **Modular & Terisolasi:** Jika ada *bug* pada fitur laporan penjualan, Anda sudah tahu pasti bahwa Anda hanya perlu mengecek folder `services/` dan `controllers/` yang berkaitan dengan laporan, tanpa takut merusak fitur manajemen karyawan.
3. **Standar Industri:** Pendekatan ini merupakan *best practice* dalam ekosistem Node.js/TypeScript moderen.

```

---

Setelah dokumentasi ini tersimpan, alur pemahaman sistem POS kita sudah sangat sistematis. 

Apakah materi ini sudah berhasil Anda simpan di file `docs/03-02-struktur-folder.md`? Jika sudah, kita bisa lanjut membuat materi **3.3: Konfigurasi Prisma ORM (Versi 7+)** untuk mendokumentasikan langkah inisialisasi database aman yang baru saja kita selesaikan!

```
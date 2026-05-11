Sekarang kita masuk ke **Bagian 1, Bab 2**. Ini adalah langkah teknis pertama Anda. Karena Anda akan membangun sistem yang mengandalkan integritas data tinggi, instalasi yang benar dan pengaturan *environment* adalah kunci agar backend Express.js Anda nantinya bisa berkomunikasi dengan lancar ke database.

Silakan buat file **`docs/02-01-instalasi-db.md`** dan masukkan konten panduan berikut:

---


# 2.1 Instalasi PostgreSQL & Setup Environment

PostgreSQL adalah sistem database relasional yang akan menjadi jantung penyimpanan data POS kita. Tahap ini akan memandu Anda melakukan instalasi hingga sistem siap digunakan oleh aplikasi backend.

## Langkah 1: Pengunduhan & Instalasi

1.  **Unduh Installer:** 
    Buka laman [EnterpriseDB (EDB) Downloads](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads). Pilih versi terbaru (misal: versi 16 atau 17) sesuai dengan sistem operasi yang Anda gunakan.
2.  **Proses Instalasi:**
    *   Jalankan installer `.exe`.
    *   **Select Components:** Pastikan semua komponen tercentang:
        *   PostgreSQL Server
        *   pgAdmin 4 (Tool manajemen bawaan)
        *   Stack Builder
        *   Command Line Tools
    *   **Password:** Masukkan password untuk superuser `postgres`. **Catat password ini**, karena akan digunakan di file `.env` proyek Express.js nanti.
    *   **Port:** Biarkan di port default `5432`.
    *   **Locale:** Pilih `[Default locale]` atau `English, United States`.

## Langkah 2: Konfigurasi Environment Variable (Windows)

Agar perintah PostgreSQL (seperti `psql`) dapat dijalankan dari terminal mana pun, termasuk terminal di dalam VS Code, Anda harus mendaftarkan folder `bin` ke dalam Path sistem.

1.  Buka **File Explorer**, cari lokasi instalasi PostgreSQL (biasanya di `C:\Program Files\PostgreSQL\[versi]\bin`). Copy alamat folder tersebut.
2.  Cari **"Edit the system environment variables"** di pencarian Windows.
3.  Klik tombol **Environment Variables**.
4.  Pada bagian **System Variables**, cari variabel bernama **Path**, lalu klik **Edit**.
5.  Klik **New**, lalu *paste* alamat folder `bin` yang tadi di-copy.
6.  Klik **OK** pada semua jendela yang terbuka.

## Langkah 3: Verifikasi Instalasi

Buka Terminal (Command Prompt atau PowerShell) baru, lalu jalankan perintah berikut:

```bash
psql --version

```

Jika muncul keterangan versi (misal: `psql (PostgreSQL) 16.x`), berarti database sudah terpasang dengan benar di sistem Anda.

## Langkah 4: Mencoba Akses Database via Terminal

Gunakan perintah berikut untuk masuk ke sistem database:

```bash
psql -U postgres

```

Masukkan password yang Anda buat saat instalasi. Jika berhasil, tampilan terminal akan berubah menjadi `postgres=#`. Ketik `\q` untuk keluar.

---

```

---

### Tips untuk Dokumentasi:
Jika Anda menemui kendala saat instalasi (misal: port 5432 sudah terpakai), Anda bisa menambahkan sub-bagian **"Troubleshooting"** di bawah file ini untuk mencatat bagaimana Anda mengatasinya. Catatan seperti itu sangat berguna dalam sebuah *e-book* teknis.

**Apakah instalasi PostgreSQL sudah berhasil dan perintah `psql --version` sudah jalan?** Jika sudah, kita akan lanjut ke **Sub-bab 2.2: Berkenalan dengan DBeaver** agar Anda memiliki alat visual yang lebih nyaman untuk mengelola database dibandingkan hanya menggunakan terminal.

```
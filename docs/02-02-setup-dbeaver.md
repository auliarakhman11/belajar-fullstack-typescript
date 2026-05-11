Sekarang kita masuk ke **Sub-bab 2.2**. Setelah berhasil memasang "mesin" database di sub-bab sebelumnya, sekarang kita butuh "kemudi" atau antarmuka visual agar kita tidak pusing berurusan dengan baris perintah (*command line*) terus-menerus.

Silakan buat file **`docs/02-02-setup-dbeaver.md`** dan masukkan konten panduan berikut:

---


# 2.2 Berkenalan dengan DBeaver (GUI Database)

Meskipun PostgreSQL memiliki tool bawaan bernama pgAdmin, banyak pengembang profesional lebih memilih **DBeaver**. DBeaver adalah aplikasi manajemen database universal yang jauh lebih ringan, modern, dan mendukung hampir semua jenis database (tidak hanya PostgreSQL).

## Mengapa Menggunakan DBeaver?
*   **Universal:** Jika suatu saat Anda ingin belajar MySQL atau MongoDB, Anda tidak perlu instal tool baru.
*   **ERD Viewer:** DBeaver bisa membuat diagram relasi tabel secara otomatis (Sangat membantu untuk dokumentasi e-book ini).
*   **SQL Editor Cerdas:** Memiliki fitur *auto-complete* yang sangat membantu saat kita menulis query manual.

## Langkah 1: Instalasi DBeaver
1.  Buka situs resmi [DBeaver.io](https://dbeaver.io/download/).
2.  Unduh **Community Edition** (Gratis) sesuai sistem operasi Anda.
3.  Lakukan instalasi seperti biasa (Next > Next > Finish).

## Langkah 2: Menghubungkan ke PostgreSQL
Setelah terinstal, kita harus menghubungkan DBeaver ke mesin PostgreSQL yang sudah kita pasang di Bab 2.1.

1.  Buka DBeaver.
2.  Klik ikon **"New Database Connection"** (ikon steker listrik di pojok kiri atas).
3.  Pilih **PostgreSQL**, lalu klik **Next**.
4.  Isi pengaturan koneksi sebagai berikut:
    *   **Host:** `localhost`
    *   **Port:** `5432`
    *   **Database:** `postgres`
    *   **Username:** `postgres`
    *   **Password:** (Masukkan password yang Anda buat saat instalasi PostgreSQL).
5.  Klik tombol **"Test Connection"**.
    *   *Catatan:* Jika muncul permintaan untuk mengunduh driver, klik **Download**. DBeaver akan otomatis menyiapkan file yang dibutuhkan.
6.  Jika muncul pesan "Connected", klik **Finish**.

## Langkah 3: Mengenal Antarmuka DBeaver
*   **Database Navigator (Kiri):** Tempat Anda melihat daftar database, tabel, dan kolom.
*   **SQL Editor (Tengah):** Tempat untuk menulis perintah SQL manual. (Bisa dibuka dengan menekan `Ctrl + Enter`).
*   **Data Grid (Bawah):** Tempat hasil query atau isi tabel ditampilkan layaknya spreadsheet (Excel).

---

```

---

### Tips untuk Anda:

Menggunakan DBeaver akan sangat memudahkan Anda saat kita masuk ke bagian **Debugging API** nanti. Jika data di aplikasi tidak muncul, Anda bisa langsung mengecek "kebenaran" datanya di DBeaver.

**Sudah berhasil menghubungkan DBeaver ke PostgreSQL?** Jika sudah muncul daftar folder seperti *Databases*, *Roles*, dan *Tables* di sisi kiri, berarti kita sudah siap lanjut ke **Sub-bab 2.3: Dasar-Dasar SQL** untuk mulai belajar cara membuat database dan tabel secara manual sebelum nantinya kita serahkan ke Prisma ORM.
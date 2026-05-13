Setelah materi **4.1** tersimpan dengan rapi, langkah selanjutnya adalah mendokumentasikan proses "sulap" dari kode skema menjadi tabel database nyata. Ini adalah tahap paling memuaskan karena Anda akan melihat struktur database Anda terbentuk secara otomatis.

Silakan buat file **`docs/04-03-migration.md`** dan masukkan materi berikut:

---

# 4.3 Database Migration (Deploy Schema)

Setelah kita menulis cetak biru database di file `schema.prisma`, langkah selanjutnya adalah melakukan **Migration**. Migrasi adalah proses mengirimkan perubahan skema yang kita buat di kode TypeScript ke dalam server PostgreSQL agar menjadi tabel yang nyata.

## 1. Apa itu Prisma Migrate?

Prisma Migrate adalah alat pelacak perubahan database. Setiap kali Anda menambah tabel atau mengubah nama kolom, Prisma akan mencatatnya dalam sebuah file sejarah (*history*). Ini sangat penting untuk laporan **Tugas Akhir** Anda karena menunjukkan progres pengembangan database yang terstruktur.

## 2. Mengeksekusi Migrasi Pertama

Pastikan database PostgreSQL Anda sudah menyala dan variabel `DATABASE_URL` di file `.env` sudah benar. Jalankan perintah berikut di terminal:

```bash
npx prisma migrate dev --name init_pos_schema

```

**Penjelasan Perintah:**

* `migrate dev`: Menjalankan migrasi di lingkungan pengembangan.
* `--name init_pos_schema`: Memberikan nama pada catatan migrasi ini agar mudah diingat.

## 3. Apa yang Terjadi di Balik Layar?

Saat perintah tersebut dijalankan, Prisma melakukan tiga hal sekaligus:

1. **Membuat File SQL:** Sebuah folder baru `prisma/migrations/` akan muncul berisi file `.sql` yang berisi perintah pembuatan tabel.
2. **Eksekusi ke Database:** Prisma menjalankan kode SQL tersebut ke PostgreSQL Anda.
3. **Regenerasi Client:** Prisma memperbarui tipe data di `node_modules` agar kode TypeScript Anda mengenali tabel-tabel baru tersebut.

## 4. Verifikasi dengan DBeaver

Untuk memastikan tabel sudah terbuat dengan benar sesuai rancangan **Kebab Yasmin** atau sistem POS Anda:

1. Buka aplikasi **DBeaver**.
2. Hubungkan ke database `pos_enterprise_db`.
3. Lihat di bagian **Tables**. Anda akan melihat tabel-tabel seperti `branches`, `users`, `products`, hingga `transactions` sudah siap digunakan.

## 5. Tips: Mengubah Struktur Tabel

Jika di masa depan Anda ingin menambah kolom baru (misalnya kolom `discount` di tabel produk), prosedurnya adalah:

1. Ubah file `schema.prisma`.
2. Jalankan kembali `npx prisma migrate dev --name nama_perubahan`.
3. Prisma akan mengupdate database tanpa menghapus data yang sudah ada (selama tidak ada konflik tipe data).

---

> **Penting:** Di lingkungan produksi (*Production*), kita tidak menggunakan `migrate dev`, melainkan `npx prisma migrate deploy` untuk menjaga keamanan data.

```

---

### Progres Anda:
Dengan selesainya Bab 4.3, proyek Anda sekarang sudah memiliki database fisik yang siap menampung data. 

**Apakah proses migrasi di terminal Anda berjalan sukses (muncul pesan "Your database is now in sync")?** Jika sukses, kita bisa lanjut ke materi penutup Bab 4, yaitu **4.4: Seeding Data Dummy**, agar database Anda tidak kosong melompong saat nanti kita mulai membuat API!

```
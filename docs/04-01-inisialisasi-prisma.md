Luar biasa semangatnya! Sekarang kita masuk ke bagian yang akan membuat Anda merasa seperti memiliki "kekuatan super" dalam mengelola database.

Selamat datang di **Bab 4: Integrasi Database dengan Prisma ORM**. Di sini, kita akan mengubah coretan ERD yang sudah kita diskusikan panjang lebar menjadi tabel-tabel nyata di PostgreSQL hanya dengan beberapa baris kode.

Silakan buat file **`docs/04-01-inisialisasi-prisma.md`** dan masukkan panduan berikut:

---

## 4.1 Inisialisasi Prisma & Koneksi Database

**Prisma** adalah ORM (_Object-Relational Mapping_) generasi terbaru yang akan memetakan tabel database menjadi objek TypeScript. Artinya, Anda tidak perlu lagi menulis query SQL manual yang panjang dan rawan salah ketik di dalam kode Express Anda.

### 1. Inisialisasi Prisma dalam Proyek

Jalankan perintah berikut di terminal Anda:

```bash
npx prisma init

```

Perintah ini akan secara otomatis melakukan dua hal:

1. Membuat folder baru bernama `prisma/` yang berisi file `schema.prisma`.
2. Memastikan file `.env` Anda sudah memiliki variabel `DATABASE_URL` (yang sudah kita buat di Bab 3.3).

### 2. Mengonfigurasi `schema.prisma`

Buka file **`prisma/schema.prisma`**. File ini adalah "kitab suci" bagi database kita. Pastikan bagian `datasource` mengarah ke PostgreSQL:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

```

### 3. Mengapa Menggunakan Prisma untuk POS Enterprise?

- **Type-Safety Otomatis**: Jika Anda mengganti nama kolom di database, Prisma akan langsung memberi tahu bagian kode mana yang _error_. Ini sangat membantu untuk proyek besar seperti Tugas Akhir Anda.
- **Prisma Studio**: GUI bawaan yang sangat ringan untuk mengintip data tanpa harus membuka DBeaver.
- **Migrasi Terkontrol**: Setiap perubahan struktur database dicatat dalam folder `migrations`, sehingga Anda punya riwayat perubahan database yang rapi.

---

### Menyiapkan Koneksi Global

Agar kita tidak berulang kali membuka-tutup koneksi ke database, kita akan membuat satu _instance_ Prisma yang bisa dipakai di mana saja.

Buat file baru **`src/config/database.config.ts`**:

```typescript
import { PrismaClient } from "@prisma/client";

// Membuat instance Prisma Client
const prisma = new PrismaClient();

export default prisma;
```

---

### Apa Selanjutnya?

Langkah inisialisasi ini seperti menyambungkan kabel antara aplikasi dan mesin database. Kabelnya sudah terpasang, tapi "wadah" datanya masih kosong.

Di **Sub-bab 4.2**, kita akan melakukan hal yang paling krusial: **Menulis Skema Database Lengkap**. Kita akan menyalin logika ERD (seperti Tabel Shift, Foto Selfie, dan Multi-Cabang) ke dalam bahasa Prisma.

**Sudahkah folder `prisma` muncul di proyek Anda setelah menjalankan `npx prisma init`?** Jika sudah, siapkan kopi, karena kita akan menulis skema database yang cukup panjang namun sangat memuaskan!

Tenang, jangan panik. Error pada file **`src/config/database.config.ts`** seperti yang terlihat pada gambar **image_7fcf7f.png** adalah hal yang sangat wajar saat pertama kali setup Prisma.

Garis merah di bawah `@prisma/client` itu terjadi karena TypeScript mencari "isi" dari library tersebut, tapi **Prisma Client belum di-generate** (dibuat) berdasarkan skema Anda. Ibaratnya, Anda sudah membeli bajunya, tapi bajunya belum dijahit sesuai ukuran badan.

---

### Solusi Cepat: Generate Prisma Client

Silakan buka terminal di VS Code Anda dan jalankan perintah berikut:

```bash
npx prisma generate

```

### Mengapa ini terjadi?

Berbeda dengan library biasa, `@prisma/client` bersifat dinamis. Dia akan membangun dirinya sendiri berdasarkan tabel-tabel yang kita tulis di file `schema.prisma`. Karena saat ini file skema kita masih "kosong" (baru inisialisasi), TypeScript belum bisa mengenali strukturnya.

---

### Jika Masih Error Setelah Generate:

Jika setelah menjalankan perintah di atas garis merah masih ada, coba lakukan langkah **"P3K"** (Pertolongan Pertama Pada Kode) berikut:

1. **Isi Skema Minimal:** Prisma kadang butuh minimal satu tabel agar bisa men-generate client dengan sempurna. Coba tambahkan "tabel percobaan" ini di dalam file **`prisma/schema.prisma`**:

```prisma
model User {
  id    String @id @default(uuid())
  name  String
}

```

Lalu jalankan lagi `npx prisma generate`. 2. **Restart TypeScript Server:** Kadang VS Code "telat mikir".

- Tekan `Ctrl + Shift + P` (Windows) atau `Cmd + Shift + P` (Mac).
- Ketik **"Restart TS Server"** dan tekan Enter.

3. **Cek Instalasi:** Pastikan library client sudah terpasang dengan perintah:

````bash
    npm install @prisma/client
    ```

---

### Tips Pro:
Nantinya, setiap kali Anda mengubah struktur tabel di `schema.prisma`, Anda **wajib** menjalankan `npx prisma generate` agar folder `node_modules` Anda diperbarui dan TypeScript bisa memberikan *auto-complete* yang akurat.

**Sudah hilang garis merahnya?** Jika sudah, kita bisa lanjut ke bagian yang paling seru: **Sub-bab 4.2**, di mana kita akan memasukkan seluruh rancangan ERD hebat kita ke dalam `schema.prisma`!

````

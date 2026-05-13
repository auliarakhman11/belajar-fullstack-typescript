Ah, baik! Anda benar, agar format dokumentasi di folder `docs/` Anda tetap rapi, konsisten, dan berurutan, lebih baik kita buatkan ulang materi Markdown khusus untuk **4.1** secara utuh. Terkadang me-*rename* file lama bisa membingungkan alur baca nantinya.

Silakan buat (atau *overwrite* jika sudah ada) file **`docs/04-01-inisialisasi-prisma.md`** dan masukkan materi berikut:

---

# 4.1 Instalasi & Inisialisasi Prisma ORM

Dalam proyek ini, kita tidak akan menulis query SQL secara manual (seperti `SELECT * FROM users`). Kita akan menggunakan **Prisma**, sebuah ORM (*Object-Relational Mapping*) modern generasi terbaru untuk Node.js dan TypeScript. 

Prisma bertindak sebagai "penerjemah" cerdas antara kode TypeScript kita dan database PostgreSQL, sekaligus memberikan *auto-complete* yang sangat kuat sehingga meminimalisir kesalahan pengetikan (*typo*).

## 1. Inisialisasi Prisma

Karena kita sudah menginstal library Prisma di Bab 3.1, langkah pertama adalah menginisialisasi konfigurasi dasarnya. Buka terminal dan jalankan:

```bash
npx prisma init

```

Perintah ini akan secara otomatis melakukan dua hal:

1. Membuat folder `prisma/` di root direktori proyek Anda.
2. Membuat file `schema.prisma` di dalam folder tersebut.

## 2. Membersihkan `schema.prisma` (Standar Prisma 7+)

Pada Prisma versi terbaru (v7 ke atas), ada aturan keamanan baru: **URL koneksi database tidak boleh lagi ditulis langsung di dalam file skema**.

Buka file **`prisma/schema.prisma`** dan ubah isinya menjadi persis seperti ini (pastikan baris `url` dihapus):

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  // Baris 'url' sengaja dihilangkan sesuai standar keamanan Prisma v7+
}

```

## 3. Membuat File Konfigurasi Khusus (`prisma.config.ts`)

Sebagai ganti baris `url` yang dihapus tadi, Prisma terbaru membutuhkan file konfigurasi di luar skema.

Buat file baru bernama **`prisma.config.ts`** di folder paling luar (sejajar dengan `package.json`), dan isi dengan:

```typescript
import { defineConfig } from "@prisma/config";
import "dotenv/config";

export default defineConfig({
  schema: "./prisma/schema.prisma",
  datasource: {
    // Memanggil URL database dari file .env dengan aman
    url: process.env.DATABASE_URL as string,
  },
});

```

## 4. Membuat *Instance* Global (`database.config.ts`)

Agar aplikasi tidak membuka koneksi database baru setiap kali ada *request* masuk (yang bisa membuat server *crash*), kita perlu membuat satu jembatan koneksi tunggal yang akan digunakan secara global.

Buat file baru di **`src/config/database.config.ts`**:

```typescript
import { PrismaClient } from "@prisma/client";
import { ENV } from "./env.config";

/**
 * Instance Prisma Client global.
 * Menggunakan URL database yang divalidasi secara ketat dari ENV.
 */
const prisma = new PrismaClient({
  datasourceUrl: ENV.DATABASE_URL,
});

export default prisma;

```

## 5. Menyiapkan Prisma Client

Langkah terakhir setelah konfigurasi selesai adalah "membangunkan" Prisma agar membuatkan tipe data untuk TypeScript. Jalankan perintah ini di terminal:

```bash
npx prisma generate

```

Jika berhasil, Anda akan melihat pesan berwarna hijau di terminal. Mulai saat ini, setiap kali kita memanggil `prisma.` di dalam kode, VS Code akan langsung tahu tabel apa saja yang kita miliki!

```

---

Sekarang materi **Bab 4.1** Anda sudah terdokumentasi dengan sangat rapi dan mengikuti standar teknologi paling baru. 

Jika Anda sudah menyimpan file ini, kita bisa lanjut memindahkan materi skema tabel (yang sudah saya kirim sebelumnya) ke dalam file **`docs/04-02-prisma-schema.md`** agar urutannya sempurna. Apakah ada yang ingin ditanyakan dari langkah 4.1 ini?

```
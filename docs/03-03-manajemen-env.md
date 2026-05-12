Mantap! Sekarang "rumah" proyek Anda sudah punya ruangan-ruangan yang jelas. Langkah selanjutnya adalah memastikan rahasia dapur kita (seperti _password_ database dan _token_ rahasia) tidak tersebar atau terlihat oleh orang lain.

Kita akan masuk ke **Sub-bab 3.3: Manajemen Environment Variable (.env)**. Silakan buat file **`docs/03-03-manajemen-env.md`** dan masukkan konten berikut:

---

## 3.3 Manajemen Environment Variable (.env)

Dalam pengembangan aplikasi profesional, kita tidak pernah menulis _password_ atau alamat database langsung di dalam kode. Jika kita melakukannya, siapa pun yang melihat kode kita (misal di GitHub) bisa meretas sistem kita.

Solusinya adalah menggunakan file **`.env`**.

### 1. Mengisi File `.env`

Buka file `.env` yang ada di root folder (sejajar dengan `package.json`) dan masukkan variabel-variabel berikut:

```text
# Server Configuration
PORT=5000
NODE_ENV=development

# Database Configuration (PostgreSQL)
# Format: postgresql://USER:PASSWORD@HOST:PORT/DATABASE
DATABASE_URL="postgresql://postgres:PASSWORD_POSTGRES_ANDA@localhost:5432/pos_enterprise_db"

# Security (Ganti dengan string acak yang panjang)
JWT_SECRET="rahasia_super_aman_123_pos_enterprise"
JWT_EXPIRES_IN="1d"

```

### 2. Mengakses Variabel di TypeScript

Berkat library `dotenv` yang sudah kita instal, kita bisa memanggil nilai-nilai di atas menggunakan `process.env`. Agar lebih rapi dan aman, kita akan memusatkan semua konfigurasi di file `src/config/env.config.ts`.

Buat file baru **`src/config/env.config.ts`**:

```typescript
import dotenv from "dotenv";
dotenv.config();

export const ENV = {
  PORT: process.env.PORT || 5000,
  DATABASE_URL: process.env.DATABASE_URL || "",
  JWT_SECRET: process.env.JWT_SECRET || "default_secret",
  NODE_ENV: process.env.NODE_ENV || "development",
};
```

### 3. Keamanan Wajib: `.gitignore`

Ini adalah langkah paling krusial. Kita harus memberitahu Git agar **JANGAN PERNAH** mengunggah file `.env` ke server atau GitHub.

Buat file baru bernama **`.gitignore`** di folder utama (sejajar dengan `.env`) dan masukkan:

```text
node_modules/
dist/
.env
*.log

```

---

### Kenapa Ini Penting untuk Proyek Anda?

Sebagai pengembang yang juga mengerjakan _freelance_ dan _Tugas Akhir_, keamanan data adalah nilai tambah yang besar. Dengan menggunakan `.env`:

- **Fleksibilitas:** Anda bisa punya database berbeda untuk laptop pribadi dan server kantor tanpa mengubah kode.
- **Keamanan:** Data rahasia tetap aman di komputer lokal Anda.

---

### Tips untuk Tugas Akhir:

Dalam laporan atau presentasi, Anda bisa menjelaskan bahwa sistem Anda sudah menerapkan **Security Best Practices** dengan memisahkan kredensial sensitif dari kode sumber menggunakan _environment variables_.

**Sudahkah Anda membuat file `.gitignore` dan mengisi file `.env`?**

Jika sudah, kita akan melompat ke bagian yang paling krusial: **Bab 4: Integrasi Database dengan Prisma ORM**. Ini adalah momen di mana rancangan ERD hebat kita sebelumnya akan diubah menjadi tabel database nyata secara otomatis! Siap lanjut?

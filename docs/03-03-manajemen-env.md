Ah, Anda sangat jeli! Anda benar sekali. Daftar Isi yang Anda lampirkan itu adalah **Daftar Isi yang sah dan yang seharusnya kita ikuti**.

Saya memohon maaf. Karena "drama" *error* konfigurasi Prisma versi 7 tadi, saya terbawa suasana untuk segera membereskan masalah tersebut sehingga tidak sengaja menggeser materi Prisma ke sub-bab `3.3`, padahal seharusnya itu adalah jatah untuk **Bab 4.1**.

Mari kita kembalikan semuanya ke jalur yang benar sesuai dengan cetak biru (blueprint) Anda.

### Solusi Cepat untuk Merapikan File Anda:

1. File **`docs/03-03-konfigurasi-prisma-v7.md`** yang baru saja kita buat di *chat* sebelumnya, silakan Anda ubah namanya (*rename*) menjadi **`docs/04-01-inisialisasi-prisma.md`**. Isinya sudah sangat sempurna untuk mengawali Bab 4.
2. Silakan buat file baru bernama **`docs/03-03-manajemen-env.md`** dan isi dengan materi di bawah ini agar Bab 3 kita tertutup dengan sempurna sesuai Daftar Isi awal.

---

Berikut adalah materi yang benar untuk **3.3**:


# 3.3 Manajemen Environment Variable (.env)

Dalam pengembangan aplikasi berstandar *enterprise*, kita tidak pernah menuliskan data sensitif—seperti password database, token rahasia, atau API Key—langsung di dalam kode sumber (*hardcode*). 

Jika hal itu dilakukan, siapa pun yang memiliki akses ke repositori (seperti GitHub) bisa dengan mudah meretas sistem POS Anda. Solusi standar industrinya adalah menggunakan **Environment Variables**.

## 1. Membuat File `.env`

File `.env` berfungsi sebagai brankas rahasia lokal di komputer Anda. Buka folder root proyek Anda (sejajar dengan `package.json`), buat file bernama **`.env`**, dan masukkan kredensial berikut:

```text
# Konfigurasi Server
PORT=5000
NODE_ENV=development

# Konfigurasi Database (Ganti PASSWORD_ANDA dengan password PostgreSQL Anda)
DATABASE_URL="postgresql://postgres:PASSWORD_ANDA@localhost:5432/pos_enterprise_db"

# Kunci Rahasia Keamanan (Gunakan kombinasi acak yang panjang untuk Production)
JWT_SECRET="rahasia_super_aman_123_pos_enterprise"

```

## 2. Mengamankan dengan `.gitignore`

File rahasia tidak boleh ikut ter- *upload*. Buat file bernama **`.gitignore`** di folder root, dan pastikan isinya mencakup:

```text
node_modules/
dist/
.env
*.log

```

## 3. Pemanggilan Variabel yang Type-Safe (`env.config.ts`)

Banyak aplikasi gagal di *production* karena *developer* lupa memasukkan variabel `.env` di server. Untuk mencegah hal ini, kita menggunakan TypeScript untuk memvalidasi keberadaan variabel tersebut sejak awal aplikasi dinyalakan.

Buat file **`src/config/env.config.ts`**:

```typescript
import "dotenv/config";

// Fungsi untuk memastikan variabel lingkungan tidak kosong
const requireEnv = (key: string): string => {
  const value = process.env[key];
  if (!value) {
    throw new Error(`CRITICAL ERROR: Environment variable ${key} belum disetting di file .env!`);
  }
  return value;
};

// Mengelompokkan semua variabel dalam satu objek ENV
export const ENV = {
  PORT: process.env.PORT || 5000,
  NODE_ENV: process.env.NODE_ENV || "development",
  DATABASE_URL: requireEnv("DATABASE_URL"),
  JWT_SECRET: requireEnv("JWT_SECRET"),
};

```

## 4. Keuntungan Pendekatan Ini

1. **Keamanan Sentral:** Seluruh file dalam proyek (seperti file koneksi database atau middleware keamanan) cukup memanggil `ENV.DATABASE_URL` atau `ENV.JWT_SECRET`.
2. **Auto-Complete:** Karena kita mengaturnya dalam objek TypeScript, VS Code akan memberikan fitur *auto-complete* saat Anda mengetik `ENV.`.
3. **Fail-Fast System:** Jika Anda *deploy* ke VPS namun lupa mengatur *environment variable*, server akan langsung menolak menyala dan memberikan pesan *error* yang jelas, bukan sekadar *crash* misterius di tengah operasi.

```

---

Sekali lagi, terima kasih sudah mengingatkan saya pada Daftar Isi utama kita. Ketelitian Anda mengawal struktur proyek ini sangat krusial, terutama untuk menyusun laporan Tugas Akhir nantinya.

**Apakah Anda sudah me-*rename* file sebelumnya menjadi `04-01` dan menyimpan `03-03` ini?** Jika sudah, kita benar-benar siap untuk membedah pembuatan tabel di **Sub-bab 4.2: Menulis Schema Prisma untuk POS**!

```
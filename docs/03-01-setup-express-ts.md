Tentu, mari kita pertajam kembali **Sub-bab 3.1**. Saya mohon maaf atas beberapa _library_ yang terlewat; dalam skala _enterprise_, kita memang membutuhkan "senjata" tambahan untuk validasi dan keamanan sejak awal.

Berikut adalah revisi lengkap dan mendalam untuk file **`docs/03-01-setup-express-ts.md`**.

---

# 3.1 Setup Project Express.js & TypeScript (Revised)

Langkah awal ini sangat krusial. Kita tidak hanya menginstal _library_, tetapi juga menyiapkan lingkungan kerja yang mendukung produktivitas tinggi dan meminimalisir kesalahan pengetikan (_human error_).

## Langkah 1: Inisialisasi Proyek

Buka terminal di folder proyek Anda, lalu jalankan perintah berikut:

```bash
npm init -y

```

Perintah ini akan menghasilkan file **`package.json`** yang berfungsi sebagai identitas dan daftar inventaris seluruh _library_ dalam proyek Anda.

## Langkah 2: Instalasi Dependensi (Production)

Ini adalah kumpulan _library_ yang akan tetap berjalan ketika aplikasi sudah di- _deploy_ ke server (VPS/Vercel).

```bash
npm install express dotenv cors prisma @prisma/client zod jsonwebtoken bcrypt morgan helmet

```

### Penjelasan Library Utama:

| Library     | Fungsi Utama dalam POS Enterprise                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------- |
| **Express** | Kerangka kerja (framework) utama untuk menangani rute API.                                        |
| **Prisma**  | ORM (Object-Relational Mapping) untuk berkomunikasi dengan PostgreSQL secara modern.              |
| **Zod**     | Validasi skema data. Memastikan data dari kasir (seperti harga atau stok) tidak salah format.     |
| **JWT**     | Menangani token keamanan agar pengguna tidak perlu login berulang kali.                           |
| **Bcrypt**  | Melakukan enkripsi (hashing) password agar tidak tersimpan dalam bentuk teks biasa.               |
| **Morgan**  | Logger otomatis. Mencatat setiap aktivitas permintaan yang masuk ke server untuk kebutuhan audit. |
| **Helmet**  | Mengamankan API dengan mengatur berbagai HTTP header demi mencegah serangan umum seperti XSS.     |

## Langkah 3: Instalasi Dependensi (Development)

Ini adalah alat bantu yang hanya kita gunakan saat proses _coding_. Alat ini tidak akan ikut dibawa saat aplikasi sudah jadi.

```bash
npm install -D typescript ts-node-dev @types/express @types/node @types/cors @types/jsonwebtoken @types/bcrypt @types/morgan

```

> **Catatan:** Library dengan awalan `@types/` sangat penting dalam TypeScript agar VS Code bisa memberikan fitur _auto-complete_ dan mendeteksi _error_ pada library JavaScript biasa.

## Langkah 4: Konfigurasi TypeScript (`tsconfig.json`)

Jalankan `npx tsc --init` untuk membuat file konfigurasi, lalu pastikan parameter berikut sudah disesuaikan agar struktur proyek rapi:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```

## Langkah 5: Otomasi Script di `package.json`

Buka file `package.json` dan tambahkan baris berikut di dalam bagian `"scripts"`:

```json
"scripts": {
  "dev": "ts-node-dev --respawn --transpile-only src/index.ts",
  "build": "tsc",
  "start": "node dist/index.js",
  "prisma:studio": "npx prisma studio",
  "prisma:migrate": "npx prisma migrate dev"
}

```

---

### Penjelasan Teknis Tambahan (Untuk Anda):

- **Mengapa Zod & Prisma itu Wajib?**
  Dalam sistem POS, integritas data adalah segalanya. **Zod** mencegah data "sampah" masuk ke server (misal: jumlah barang negatif), sementara **Prisma** memastikan data yang masuk ke PostgreSQL sesuai dengan tipe data yang kita rancang di ERD.
- **Keamanan Berlapis:**
  Dengan menyertakan **Helmet** dan **Bcrypt** sejak awal, Anda sudah menerapkan standar keamanan dasar yang profesional. Ini sangat penting jika nantinya aplikasi Anda digunakan untuk transaksi riil yang melibatkan uang dan data pribadi pelanggan.
- **Prisma Studio:**
  Saya menambahkan script `prisma:studio`. Ini adalah fitur luar biasa dari Prisma yang memungkinkan Anda melihat dan mengedit isi database lewat antarmuka web (mirip DBeaver tapi lebih praktis untuk developer).

---

**Apakah Anda sudah berhasil menginstal semua library di atas tanpa ada pesan error?**

Jika sudah, folder proyek Anda kini sudah memiliki "persenjataan" lengkap. Langkah selanjutnya kita akan masuk ke **Sub-bab 3.2: Struktur Folder Berbasis Domain**, di mana kita akan memisahkan kode antara bagian _Kasir_, _Admin_, dan _Produk_ agar tidak bercampur aduk.

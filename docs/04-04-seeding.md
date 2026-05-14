Setelah struktur database (Skema) Anda siap, langkah selanjutnya yang sangat penting adalah **Seeding**. Bayangkan Anda baru saja membangun sebuah mall (database) yang megah tapi masih kosong melompong. **Seeding** adalah proses mengisi mall tersebut dengan "penghuni" awal (seperti data Admin, daftar Cabang pertama, atau kategori barang) agar aplikasi bisa langsung diuji coba.

Silakan buat file **`docs/04-04-seeding.md`** dan ikuti panduan teknis berikut:

---

# 4.4 Seeding Data Dummy

**Seeding** adalah proses pengisian database dengan data awal secara otomatis menggunakan script. Ini sangat berguna agar kita tidak perlu mengisi data secara manual lewat DBeaver setiap kali kita melakukan reset database.

## 1. Menyiapkan Script Seed (`prisma/seed.ts`)

Buat file baru bernama **`seed.ts`** di dalam folder **`prisma/`**. Pada Prisma versi 7, kita wajib memasukkan `datasourceUrl` secara manual karena URL tidak lagi disimpan di `schema.prisma`.

```typescript
import { PrismaClient } from "@prisma/client";
import { PrismaPg } from "@prisma/adapter-pg"; // Import adapter baru
import bcrypt from "bcrypt";
import "dotenv/config";

// 1. Ambil URL dari .env
const connectionString = process.env.DATABASE_URL as string;

// 2. Buat Adapter
const adapter = new PrismaPg({ connectionString });

// 3. Masukkan adapter ke dalam Prisma Client
const prisma = new PrismaClient({ adapter });

async function main() {
  console.log("Memulai proses seeding...");

  // 1. Seed Roles
  const superAdminRole = await prisma.role.upsert({
    where: { id: "role-1" },
    update: {},
    create: { id: "role-1", name: "Super User" },
  });

  const adminRole = await prisma.role.upsert({
    where: { id: "role-2" },
    update: {},
    create: { id: "role-2", name: "Admin Cabang" },
  });

  const kasirRole = await prisma.role.upsert({
    where: { id: "role-3" },
    update: {},
    create: { id: "role-3", name: "Kasir" },
  });

  // 2. Seed Branch Utama
  const mainBranch = await prisma.branch.upsert({
    where: { id: "branch-main" },
    update: {},
    create: {
      id: "branch-main",
      name: "Pusat Jakarta",
      address: "Jl. Jenderal Sudirman No. 1",
    },
  });

  // 3. Seed Super Admin (Password: admin123)
  const hashedPassword = await bcrypt.hash("admin123", 10);
  await prisma.user.upsert({
    where: { email: "admin@pos.com" },
    update: {},
    create: {
      name: "Super Admin",
      email: "admin@pos.com",
      passwordHash: hashedPassword,
      roleId: superAdminRole.id,
    },
  });

  // 4. Seed Kategori Produk Dasar
  await prisma.category.createMany({
    data: [{ name: "Makanan" }, { name: "Minuman" }, { name: "Snack" }],
    skipDuplicates: true,
  });

  console.log("Seeding selesai dengan sukses!");
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

## 2. Daftarkan Script di `prisma.config.ts` (Standar Terbaru)

Pada Prisma v7, pendaftaran script seed dilakukan di file konfigurasi utama, bukan lagi di `package.json`. Buka `prisma.config.ts` dan tambahkan blok `migrations`:

````typescript
export default defineConfig({
  // ... konfigurasi lainnya
  migrations: {
    seed: "ts-node prisma/seed.ts",
  },
});

## 3. Eksekusi Seeding

Sekarang, jalankan perintah "sakti" ini di terminal Anda:

```bash
npx prisma db seed

````

Jika muncul pesan **"Seeding selesai dengan sukses!"**, selamat! Database Anda sekarang sudah memiliki isi.

---

### Catatan Penting untuk Bab 4.3 (Migration)

Sebelum Anda bisa menjalankan **Seeding (4.4)**, Anda **WAJIB** menjalankan **Migration (4.3)** terlebih dahulu. Migration adalah perintah untuk mengubah kode skema yang kita tulis di Bab 4.2 menjadi tabel asli di PostgreSQL.

Jika Anda belum melakukannya, jalankan ini sekarang:

```bash
npx prisma migrate dev --name init_database

```

### Kenapa Seeding ini Penting?

1. **Login Pertama:** Tanpa seeding, Anda tidak punya akun untuk login ke aplikasi karena tabel `User` masih kosong.
2. **Konsistensi Data:** Memastikan ID Role (seperti 'Super User') sama di semua komputer tim pengembang.
3. **Demo & Testing:** Anda bisa langsung menunjukkan aplikasi yang sudah ada isinya (produk, kategori) tanpa harus mengetik satu-satu saat presentasi.

**Apakah database Anda sudah berhasil terisi dengan data dummy ini?**

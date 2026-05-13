Tentu, mari kita susun kembali **Bab 4.2** dengan penjelasan yang sangat mendetail agar Anda bisa memverifikasi jumlahnya satu per satu. Fokus kita adalah memastikan struktur ini memenuhi standar **Tugas Akhir** Anda yang berbasis sistem informasi manajemen.

Berikut adalah materi lengkap untuk file **`docs/04-02-prisma-schema.md`**:

---

# 4.2 Menulis Schema Prisma untuk POS Enterprise (Full Version)

Dalam arsitektur database profesional, kita menerapkan prinsip **Normalisasi**. Tujuannya agar data tidak berulang (duplikat) dan hubungan antar data menjadi sangat kuat. Dalam sistem ini, kita akan membangun total **16 tabel (Model)**.

## A. Definisi Pilihan (Enum)

Enum digunakan untuk membatasi input agar tidak ada salah ketik pada kolom yang pilihannya sudah pasti.

```prisma
enum DiscountType { PERCENTAGE | NOMINAL }
enum MutationType { IN | OUT | SALE | RETURN }
enum ShiftStatus  { OPEN | CLOSED }

```

---

## B. Kelompok Master Data (Tabel 1 - 6)

Tabel ini adalah referensi utama. Jika Anda ingin mengganti nama cabang atau jenis pembayaran, Anda cukup mengubahnya di sini sekali saja.

1. **`Branch`**: Identitas fisik setiap cabang atau outlet.
2. **`Role`**: Level akses pengguna (Super User, Admin, Kasir).
3. **`Category`**: Pengelompokan produk (makanan, minuman, dll).
4. **`PaymentMethod`**: Daftar metode pembayaran (Cash, QRIS, Debit).
5. **`OrderType`**: Tipe pesanan (Dine-in, Takeaway, Delivery).
6. **`Discount`**: Pengaturan diskon yang sedang aktif.

```prisma
model Branch {
  id        String   @id @default(uuid())
  name      String
  address   String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  // Relasi ke tabel lain...
  @@map("branches")
}

model Role {
  id    String @id @default(uuid())
  name  String // Contoh: Super User, Admin, Kasir
  @@map("roles")
}

model Category {
  id    String @id @default(uuid())
  name  String
  @@map("categories")
}

model PaymentMethod {
  id    String @id @default(uuid())
  name  String // Contoh: Cash, QRIS
  @@map("payment_methods")
}

model OrderType {
  id    String @id @default(uuid())
  name  String // Contoh: Dine In, Takeaway
  @@map("order_types")
}

model Discount {
  id       String       @id @default(uuid())
  name     String
  type     DiscountType
  value    Decimal      @db.Decimal(12, 2)
  isActive Boolean      @default(true)
  @@map("discounts")
}

```

---

## C. Kelompok SDM & Pengguna (Tabel 7 - 9)

Bagian ini mengatur siapa yang mengoperasikan sistem dan siapa yang bekerja di lapangan.

7. **`User`**: Akun login (email dan password).
8. **`UserBranch`**: **(Tabel Penghubung)** Mencatat seorang User boleh mengelola cabang mana saja (Multi-Branch).
9. **`Employee`**: Identitas fisik staf yang bekerja di outlet.

```prisma
model User {
  id           String   @id @default(uuid())
  roleId       String
  name         String
  email        String   @unique
  passwordHash String
  @@map("users")
}

model UserBranch {
  userId   String
  branchId String
  @@id([userId, branchId])
  @@map("user_branches")
}

model Employee {
  id        String   @id @default(uuid())
  branchId  String
  name      String
  isActive  Boolean  @default(true)
  @@map("employees")
}

```

---

## D. Kelompok Produk & Stok (Tabel 10 - 11)

Mengelola barang dagangan dan melacak setiap butir pergerakan stok.

10. **`Product`**: Detail barang, harga, dan kode SKU.
11. **`StockMutation`**: Log otomatis setiap kali stok bertambah atau berkurang.

```prisma
model Product {
  id         String   @id @default(uuid())
  categoryId String
  sku        String   @unique
  name       String
  price      Decimal  @db.Decimal(12, 2)
  @@map("products")
}

model StockMutation {
  id        String       @id @default(uuid())
  productId String
  branchId  String
  type      MutationType
  quantity  Int
  createdAt DateTime     @default(now())
  @@map("stock_mutations")
}

```

---

## E. Kelompok Operasional Kasir (Tabel 12 - 13)

Fitur unggulan untuk audit, mencatat pembukaan laci kasir dan absensi foto.

12. **`Shift`**: Laporan saldo awal/akhir dan status operasional outlet.
13. **`ShiftAttendance`**: Mencatat kehadiran staf per shift lengkap dengan **foto selfie**.

```prisma
model Shift {
  id             String      @id @default(uuid())
  branchId       String
  openedByUserId String
  startTime      DateTime    @default(now())
  startCash      Decimal     @db.Decimal(12, 2)
  status         ShiftStatus @default(OPEN)
  @@map("shifts")
}

model ShiftAttendance {
  id          String   @id @default(uuid())
  shiftId     String
  employeeId  String
  photoSelfie String?
  @@map("shift_attendances")
}

```

---

## F. Kelompok Transaksi Penjualan (Tabel 14 - 16)

Mencatat detail uang masuk dan siapa saja yang terlibat dalam penjualan.

14. **`Transaction`**: Header struk (Total harga, diskon, metode bayar).
15. **`TransactionDetail`**: Daftar barang (Item A beli 2, Item B beli 1).
16. **`TransactionEmployee`**: **(Tabel Penghubung)** Mencatat siapa saja staf yang melayani transaksi tersebut untuk perhitungan performa.

```prisma
model Transaction {
  id              String   @id @default(uuid())
  branchId        String
  shiftId         String
  totalAmount     Decimal  @db.Decimal(12, 2)
  @@map("transactions")
}

model TransactionDetail {
  id                   String  @id @default(uuid())
  transactionId        String
  productId            String
  quantity             Int
  priceAtTransaction   Decimal @db.Decimal(12, 2)
  @@map("transaction_details")
}

model TransactionEmployee {
  transactionId String
  employeeId    String
  @@id([transactionId, employeeId])
  @@map("transaction_employees")
}

```

---

### Kesimpulan Penghitungan (Checklist)

Silakan pastikan 16 nama ini ada di dalam file `schema.prisma` Anda:

1. Branch, 2. Role, 3. Category, 4. PaymentMethod, 5. OrderType, 6. Discount, 7. User, 8. UserBranch, 9. Employee, 10. Product, 11. StockMutation, 12. Shift, 13. ShiftAttendance, 14. Transaction, 15. TransactionDetail, 16. TransactionEmployee.

Sudahkah Anda menghitung ke-16 tabel tersebut di dalam file Anda?
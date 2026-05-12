Sekarang kita sampai di bagian paling krusial dalam perancangan sistem: **Sub-bab 2.4**. ERD adalah "peta jalan" bagi data Anda. Jika rancangan ini salah, maka fitur _multi-cabang_ dan _manajemen stok_ Anda akan sulit diimplementasikan di kemudian hari.

Silakan buat file **`docs/02-04-perancangan-erd.md`** dan masukkan konten perancangan berikut:

---

# 2.4 Perancangan Database (ERD)

Entity Relationship Diagram (ERD) adalah representasi visual dari tabel-tabel di database. ERD ini dirancang dengan sangat komprehensif untuk mendukung skalabilitas **Multi-Cabang**, **Manajemen Hak Akses Kompleks**, **Pencatatan Shift/Operasional**, serta **Multi-Karyawan per Transaksi**.

## Diagram ERD (Mermaid)

```mermaid
erDiagram
    BRANCH ||--o{ USER_BRANCH : "akses"
    USER ||--o{ USER_BRANCH : "diberikan"
    BRANCH ||--o{ EMPLOYEE : "mempekerjakan"
    BRANCH ||--o{ SHIFT : "beroperasi"
    BRANCH ||--o{ STOCK_MUTATION : "log stok"
    BRANCH ||--o{ TRANSACTION : "mencatat"

    ROLE ||--o{ USER : "memiliki role"
    CATEGORY ||--o{ PRODUCT : "kategori"

    PRODUCT ||--o{ TRANSACTION_DETAIL : "berisi"
    PRODUCT ||--o{ STOCK_MUTATION : "log barang"

    TRANSACTION ||--o{ TRANSACTION_DETAIL : "memiliki detail"
    TRANSACTION ||--|{ TRANSACTION_EMPLOYEE : "dilayani oleh"
    EMPLOYEE ||--o{ TRANSACTION_EMPLOYEE : "melayani"

    SHIFT ||--o{ TRANSACTION : "terjadi pada shift"
    SHIFT ||--|{ SHIFT_ATTENDANCE : "mencatat kehadiran"
    EMPLOYEE ||--o{ SHIFT_ATTENDANCE : "melakukan absen"

    PAYMENT_METHOD ||--o{ TRANSACTION : "metode bayar"
    ORDER_TYPE ||--o{ TRANSACTION : "tipe pesanan"
    DISCOUNT ||--o{ TRANSACTION : "potongan"

    %% Master Data
    BRANCH {
        uuid id PK
        string name
        string address
        timestamp created_at
        timestamp updated_at
    }
    ROLE {
        uuid id PK
        string name "Super User, Admin, Kasir"
        timestamp created_at
        timestamp updated_at
    }
    CATEGORY {
        uuid id PK
        string name
        timestamp created_at
        timestamp updated_at
    }
    PAYMENT_METHOD {
        uuid id PK
        string name "Cash, QRIS, Transfer"
        timestamp created_at
        timestamp updated_at
    }
    ORDER_TYPE {
        uuid id PK
        string name "Takeaway, Dine In, Delivery"
        timestamp created_at
        timestamp updated_at
    }
    DISCOUNT {
        uuid id PK
        string name
        string type "PERCENTAGE, NOMINAL"
        decimal value
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }

    %% Users & Employees
    USER {
        uuid id PK
        uuid role_id FK
        string name
        string email
        string password_hash
        timestamp created_at
        timestamp updated_at
    }
    USER_BRANCH {
        uuid user_id FK
        uuid branch_id FK
    }
    EMPLOYEE {
        uuid id PK
        uuid branch_id FK
        string name
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }

    %% Products & Inventory
    PRODUCT {
        uuid id PK
        uuid category_id FK
        string sku
        string name
        decimal price
        string image_url
        timestamp created_at
        timestamp updated_at
    }
    STOCK_MUTATION {
        uuid id PK
        uuid product_id FK
        uuid branch_id FK
        string type "IN, OUT, SALE, RETURN"
        int quantity
        timestamp created_at
    }

    %% Shift & Operations
    SHIFT {
        uuid id PK
        uuid branch_id FK
        uuid opened_by_user_id FK
        timestamp start_time
        timestamp end_time
        decimal start_cash
        decimal end_cash
        string photo_front
        string photo_inside
        string photo_back
        string status "OPEN, CLOSED"
        timestamp created_at
        timestamp updated_at
    }
    SHIFT_ATTENDANCE {
        uuid id PK
        uuid shift_id FK
        uuid employee_id FK
        string photo_selfie
        timestamp check_in_time
    }

    %% Transactions
    TRANSACTION {
        uuid id PK
        uuid branch_id FK
        uuid shift_id FK
        uuid payment_method_id FK
        uuid order_type_id FK
        uuid discount_id FK
        string customer_name
        string customer_wa
        decimal sub_total
        decimal discount_amount
        decimal total_amount
        decimal paid_amount
        timestamp transaction_date "Waktu Riil"
        timestamp created_at "Waktu Sinkronisasi"
        timestamp updated_at
    }
    TRANSACTION_DETAIL {
        uuid id PK
        uuid transaction_id FK
        uuid product_id FK
        int quantity
        decimal price_at_transaction
        decimal subtotal
    }
    TRANSACTION_EMPLOYEE {
        uuid transaction_id FK
        uuid employee_id FK
    }

```

## Penjelasan Relasi Kunci

### 1. Multi-Cabang (Multi-Branch Isolation)

Hampir semua tabel utama (`User`, `Transaction`, `Stock_Mutation`) memiliki kolom `branch_id`. Ini memungkinkan kita untuk memfilter data dengan sangat cepat: _"Tampilkan hanya transaksi milik Cabang A"_.

### 2. Log Mutasi Stok (Inventory Ledger)

Kita tidak hanya menyimpan kolom "stok_saat_ini" di tabel produk. Mengapa? Karena itu rawan manipulasi dan sulit dilacak.

- Setiap ada barang masuk atau terjual, kita mencatatnya di `STOCK_MUTATION`.
- Stok akhir dihitung dari total mutasi masuk dikurangi mutasi keluar. Ini jauh lebih akurat untuk audit.

### 3. Keamanan UUID

Kita menggunakan `UUID` daripada `Integer (Serial)` untuk Primary Key.

- **Alasan:** Memudahkan fitur **Offline-First**. Kasir bisa membuat transaksi di lokal dengan ID unik tanpa takut bentrok dengan ID yang dibuat oleh cabang lain saat data disinkronkan ke server.

### 4. Harga Statis di Detail Transaksi

Perhatikan kolom `price_at_transaction` di `TRANSACTION_DETAIL`.

- **Alasan:** Harga produk bisa berubah di masa depan. Jika kita tidak menyimpan harga saat transaksi terjadi, maka laporan pendapatan lama kita akan ikut berubah mengikuti harga produk yang baru. Ini adalah prinsip dasar akuntansi digital.

## Penjelasan Struktur Lanjutan

### 1. Hak Akses Pivot (`USER_BRANCH`)

Tabel `USER_BRANCH` menyelesaikan masalah akses admin. Seorang _Super User_ dapat menetapkan Admin B untuk memiliki akses ke Cabang 1, Cabang 2, dan Cabang 3. Saat Admin B _login_, sistem hanya akan menarik data dari ketiga cabang tersebut.

### 2. Pemisahan Waktu (`transaction_date` vs `created_at`)

- `transaction_date`: Adalah waktu saat kasir menekan tombol "Bayar" di aplikasi (bahkan saat tidak ada internet).
- `created_at`: Adalah waktu saat _server backend_ menerima data tersebut ketika koneksi internet kembali pulih. Ini sangat penting untuk audit sinkronisasi _offline-first_.

### 3. Operasional Shift & Dokumentasi (`SHIFT`)

Fitur buka/tutup toko dikontrol melalui tabel ini. Kasir tidak bisa melakukan transaksi jika tidak ada `SHIFT` yang berstatus `OPEN` pada hari tersebut. Empat foto validasi kesiapan outlet disimpan alamat URL-nya di sini.

### 4. Transaksi Kolektif (`TRANSACTION_EMPLOYEE`)

Sistem tidak melacak siapa yang melakukan _login_ di perangkat kasir, melainkan karyawan mana yang dipilih di layar saat transaksi terjadi. Satu pesanan kompleks dapat ditandai dilayani oleh "Andi" dan "Budi" sekaligus melalui tabel relasi ini.

---

```

---

### Apa yang Harus Anda Lakukan Sekarang?
1.  Simpan file ini di folder `docs`.
2.  Coba baca kembali diagramnya di GitHub (GitHub secara otomatis merender kode `mermaid` menjadi gambar diagram yang cantik).

**Bab 2 selesai!** Kita sudah punya mesin database dan rancangan petanya.

**Siap lanjut ke Bagian 2, Bab 3 (Sub-bab 3.1)?** Kita akan mulai menyentuh kode program sesungguhnya: **Setup Project Express.js dengan TypeScript.** Inilah saatnya kita membangun "Pondasi Bangunan" backend Anda.

```

Sekarang kita masuk ke **Sub-bab 2.3**. Meskipun nantinya kita akan menggunakan Prisma ORM untuk mengelola database secara otomatis dari kode program, memahami **SQL (Structured Query Language)** adalah ilmu wajib bagi setiap *Backend Developer*.

Pengetahuan ini akan sangat membantu Anda saat melakukan *debugging* data atau ketika harus mengoptimalkan performa database yang tidak bisa ditangani oleh ORM biasa.

Silakan buat file **`docs/02-03-dasar-sql.md`** dan masukkan konten panduan berikut:

---

```markdown
# 2.3 Dasar-Dasar SQL (Create, Table, Querying)

SQL adalah bahasa standar untuk berinteraksi dengan PostgreSQL. Dalam membangun sistem POS, kita akan sering berurusan dengan dua jenis perintah utama: **DDL** (untuk membangun struktur) dan **DML** (untuk mengolah data).

## 1. DDL (Data Definition Language)
Digunakan untuk mendefinisikan atau mengubah struktur database.

### Membuat Database
Langkah pertama adalah membuat "wadah" untuk aplikasi kita.
```sql
CREATE DATABASE pos_enterprise_db;

```

### Membuat Tabel

Mari kita coba membuat tabel `branches` (cabang) sederhana sebagai latihan. Di PostgreSQL, kita sangat disarankan menggunakan tipe data `UUID` untuk keamanan.

```sql
CREATE TABLE branches (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    address TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

```

## 2. DML (Data Manipulation Language)

Digunakan untuk memanipulasi data di dalam tabel (CRUD).

### Create (Insert Data)

Menambahkan cabang baru ke dalam tabel.

```sql
INSERT INTO branches (name, address) 
VALUES ('Cabang Jakarta Pusat', 'Jl. Sudirman No. 10');

```

### Read (Select Data)

Mengambil data dari database.

```sql
-- Mengambil semua kolom
SELECT * FROM branches;

-- Mengambil kolom tertentu dengan filter
SELECT name FROM branches WHERE name LIKE '%Jakarta%';

```

### Update (Ubah Data)

Memperbarui informasi yang sudah ada.

```sql
UPDATE branches 
SET address = 'Jl. Sudirman No. 12 (Gedung Baru)' 
WHERE name = 'Cabang Jakarta Pusat';

```

### Delete (Hapus Data)

Menghapus baris dari tabel.

```sql
DELETE FROM branches WHERE name = 'Cabang Jakarta Pusat';

```

## Tips Praktis di DBeaver

1. Buka **SQL Editor** dengan menekan `F3` atau klik kanan pada database > **SQL Editor** > **New SQL Script**.
2. Tulis perintah di atas.
3. Tekan `Ctrl + Enter` untuk menjalankan satu baris perintah tempat kursor berada.
4. Gunakan `Shift + Ctrl + Enter` untuk menjalankan seluruh script.

---

```

---

### Mengapa Belajar Ini?
Di dalam sistem POS Anda, perintah-perintah ini akan dijalankan oleh **Express.js** melalui **Prisma**. Namun, dengan mencoba perintah ini secara manual di DBeaver, Anda akan memahami apa yang terjadi di balik layar ketika kasir menekan tombol "Simpan Transaksi".

**Apakah Anda sudah mencoba menjalankan perintah `CREATE DATABASE` di DBeaver?** Jika database `pos_enterprise_db` sudah berhasil dibuat, kita akan lanjut ke bagian paling seru di Bab 2: **Sub-bab 2.4: Perancangan ERD (Entity Relationship Diagram)** yang akan menjadi peta utama seluruh aplikasi kita.

```
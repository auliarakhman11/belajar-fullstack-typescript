Sesuai dengan rencana kita, setelah urusan konfigurasi selesai, kita harus merapikan "rumah" kode kita. Di proyek skala besar, menaruh semua kode di satu file adalah resep bencana. Kita akan menggunakan pendekatan **Domain-Based Folder Structure**.

Silakan buat file baru **`docs/03-02-struktur-folder.md`** dan masukkan konten berikut:

---

## 3.2 Struktur Folder Berbasis Domain

Dalam membangun sistem POS Enterprise, kita tidak menggunakan struktur MVC (Model-View-Controller) klasik yang mencampur semua logika. Kita akan membaginya berdasarkan **Domain** atau **Modul** agar ketika tim Anda bertambah atau fitur semakin kompleks, kode tetap mudah dinavigasi.

### Hierarki Folder Proyek

Berikut adalah struktur folder yang harus Anda buat di dalam folder `src/`:

```text
src/
├── config/             # Konfigurasi database, env, dan library pihak ketiga (Prisma, Cloudinary, dll)
├── controllers/        # Menangani HTTP Request & Response (Hanya validasi input awal)
├── services/           # Otak aplikasi. Seluruh logika bisnis (perhitungan stok, diskon) ada di sini
├── routes/             # Definisi endpoint API (URL)
├── middlewares/        # Fungsi perantara (Cek login/JWT, cek role admin/kasir)
├── interfaces/         # Definisi tipe data (TypeScript Interfaces & Types)
├── utils/              # Fungsi pembantu (Helper) seperti format mata uang atau generator ID
├── validatons/         # Skema validasi data menggunakan Zod
└── index.ts            # Entry point utama aplikasi

```

### Mengapa Memisahkan Service dan Controller?

Ini adalah poin paling penting dalam arsitektur profesional:

1. **Controller (Penerima Tamu):** Tugasnya hanya menerima data dari user, mengecek apakah datanya ada, lalu mengirimkannya ke _Service_. Setelah urusan di _Service_ selesai, _Controller_ memberikan jawaban (Response) ke user.
2. **Service (Dapur/Koki):** Di sinilah semua perhitungan terjadi. Misalnya, saat kasir menekan tombol bayar, _Service_ akan menghitung sisa stok, mengecek apakah diskon masih berlaku, dan menyimpan data ke database.

### Keuntungan Struktur Ini:

- **Mudah Diuji (Testable):** Anda bisa mengetes logika perhitungan di _Service_ tanpa harus menjalankan server API.
- **Modular:** Jika Anda ingin mengubah sistem pengiriman struk dari WhatsApp ke Email, Anda hanya perlu mengubah satu file di folder _Service_ atau _Utils_ tanpa mengganggu logika lainnya.
- **Skalabilitas:** Jika nanti fitur "Laporan Keuangan" menjadi sangat besar, Anda tinggal membuat folder baru tanpa membuat file `index.ts` menjadi ribuan baris.

---

### Tugas Anda Sekarang:

Silakan buat folder-folder kosong tersebut di dalam folder `src` melalui VS Code atau terminal:

```bash
mkdir src/config src/controllers src/services src/routes src/middlewares src/interfaces src/utils src/validations

```

Setelah folder ini siap, kita akan memiliki struktur yang sangat rapi dan siap untuk diisi dengan kode fungsional.

Apakah folder-folder tersebut sudah Anda buat di dalam direktori `src` Anda?

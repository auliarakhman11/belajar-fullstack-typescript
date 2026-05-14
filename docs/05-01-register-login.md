Selamat! Kita telah melewati bagian tersulit dalam konfigurasi _database_ dengan standar terbaru Prisma 7. Sekarang, saatnya membangun fitur keamanan yang akan menjadi gerbang utama aplikasi Anda.

Silakan buat file **`docs/05-01-register-login.md`** dan masukkan materi teknis berikut:

---

# 5.1 Implementasi Register & Login

Fitur Autentikasi adalah fondasi keamanan dalam sistem POS Enterprise. Kita tidak hanya menyimpan data, tetapi juga memastikan password pengguna terenkripsi dengan aman dan memberikan akses yang sesuai dengan _role_ mereka.

## 1. Instalasi Library Keamanan

Kita membutuhkan dua library utama:

- **`bcrypt`**: Untuk melakukan _hashing_ password (mengacak password agar tidak bisa dibaca manusia di database).
- **`jsonwebtoken`**: Untuk membuat token digital (JWT) sebagai bukti bahwa user sudah login.

Jalankan perintah ini di terminal:

```bash
npm install bcrypt jsonwebtoken
npm install -D @types/bcrypt @types/jsonwebtoken

```

---

## 2. Membuat Auth Service (`src/services/auth.service.ts`)

_Service_ bertugas menangani logika bisnis murni, seperti mengecek apakah email sudah terdaftar atau mencocokkan password.

```typescript
import prisma from "../config/database.config";
import bcrypt from "bcrypt";
import jwt from "jsonwebtoken";
import { ENV } from "../config/env.config";

export class AuthService {
  // Logika Pendaftaran User Baru
  static async register(data: any) {
    const { email, password, name, roleId } = data;

    // 1. Cek apakah email sudah ada
    const existingUser = await prisma.user.findUnique({ where: { email } });
    if (existingUser) throw new Error("Email sudah terdaftar!");

    // 2. Hash Password (Salt round: 10)
    const hashedPassword = await bcrypt.hash(password, 10);

    // 3. Simpan ke Database
    return await prisma.user.create({
      data: {
        email,
        name,
        roleId,
        passwordHash: hashedPassword,
      },
    });
  }

  // Logika Login
  static async login(email: string, pass: string) {
    const user = await prisma.user.findUnique({
      where: { email },
      include: { role: true }, // Sertakan data role untuk JWT
    });

    if (!user) throw new Error("User tidak ditemukan!");

    // Bandingkan password input dengan hash di database
    const isMatch = await bcrypt.compare(pass, user.passwordHash);
    if (!isMatch) throw new Error("Password salah!");

    // Buat Token JWT (Berlaku 1 hari)
    const token = jwt.sign(
      { id: user.id, role: user.role.name },
      ENV.JWT_SECRET as string,
      { expiresIn: "1d" },
    );

    return { user, token };
  }
}
```

---

## 3. Membuat Auth Controller (`src/controllers/auth.controller.ts`)

_Controller_ bertugas menerima _request_ dari pengguna dan mengirimkan _response_ balik.

```typescript
import { Request, Response } from "express";
import { AuthService } from "../services/auth.service";

export const register = async (req: Request, res: Response) => {
  try {
    const user = await AuthService.register(req.body);
    res.status(201).json({ message: "Registrasi berhasil", data: user });
  } catch (error: any) {
    res.status(400).json({ error: error.message });
  }
};

export const login = async (req: Request, res: Response) => {
  try {
    const { email, password } = req.body;
    const result = await AuthService.login(email, password);
    res.status(200).json({ message: "Login sukses", ...result });
  } catch (error: any) {
    res.status(401).json({ error: error.message });
  }
};
```

---

## 4. Menghubungkan Route (`src/routes/auth.route.ts`)

Sekarang kita buatkan "jalan" agar alamat `/api/auth/login` bisa diakses.

```typescript
import { Router } from "express";
import { login, register } from "../controllers/auth.controller";

const authRouter = Router();

authRouter.post("/register", register);
authRouter.post("/login", login);

export default authRouter;
```

---

### Alur Kerja Autentikasi JWT

## 5. Mengapa Menggunakan Password Hash?

Sebagai pengembang sistem informasi manajemen, keamanan data adalah prioritas utama. Kita tidak boleh menyimpan password dalam bentuk teks biasa (_plain text_). Dengan `bcrypt`, jika database Anda bocor, peretas hanya akan melihat kode acak yang sangat sulit dipecahkan, sehingga data pengguna tetap terlindungi.

---

**Tugas Anda Selanjutnya:**

1. Pastikan Anda sudah membuat folder `services`, `controllers`, dan `routes` di dalam folder `src`.
2. Simpan kode-kode di atas ke dalam file masing-masing.
3. Jangan lupa daftarkan `authRouter` di file utama aplikasi Anda (biasanya `src/app.ts` atau `src/index.ts`) menggunakan `app.use("/api/auth", authRouter)`.

Apakah file-file autentikasi ini sudah siap di VS Code Anda? Jika sudah, kita bisa lanjut ke **5.2 Mekanisme Access & Refresh Token** untuk membuat sistem keamanan Anda setingkat lebih canggih!

Untuk menguji API yang baru saja kita bangun, Anda memerlukan alat pengirim _request_ HTTP. Alat ini bertindak sebagai "pelanggan" yang mengirim data ke server Anda dan menangkap balasannya.

Ada dua alat yang paling direkomendasikan untuk pengembang:

1. **Thunder Client:** Ekstensi VS Code (sangat praktis karena tidak perlu pindah aplikasi).
2. **Postman:** Aplikasi desktop (standar industri, sangat lengkap fiturnya).

Berikut adalah langkah-langkah pengujiannya.

### Langkah 1: Jalankan Server

Pastikan server backend Anda sedang berjalan. Jalankan di terminal:

```bash
npm run dev

```

_(Pastikan tidak ada pesan error di terminal)._

---

### Langkah 2: Uji Endpoint `Register`

Buka Thunder Client atau Postman, lalu buat _request_ baru dengan parameter berikut:

- **Method:** `POST`
- **URL:** `http://localhost:3000/api/auth/register` (Sesuaikan port jika aplikasi Anda menggunakan port lain)
- **Header:** `Content-Type: application/json`
- **Body (JSON):**

```json
{
  "name": "Budi Santoso",
  "email": "budi@pos.com",
  "password": "password123",
  "roleId": "role-3"
}
```

_(Catatan: Pastikan `roleId` sesuai dengan ID yang ada di tabel `roles` database Anda. Anda bisa mengeceknya dengan menjalankan `npx prisma studio` di terminal)._

**Hasil yang diharapkan:**

- **Status Code:** `201 Created`
- **Respon:** JSON berisi data user yang baru dibuat.

---

### Langkah 3: Uji Endpoint `Login`

Setelah berhasil register, mari uji apakah sistem bisa mengenali user tersebut.

- **Method:** `POST`
- **URL:** `http://localhost:3000/api/auth/login`
- **Body (JSON):**

```json
{
  "email": "budi@pos.com",
  "password": "password123"
}
```

**Hasil yang diharapkan:**

- **Status Code:** `200 OK`
- **Respon:** JSON yang berisi data user dan **`token`** (JWT).

---

### Tips Jika Terjadi Error

Jika Anda mendapatkan respons error, berikut cara membacanya:

- **Status 400 (Bad Request):** Biasanya ada validasi yang gagal (misal: email sudah dipakai). Cek pesan error di dalam body respons.
- **Status 401 (Unauthorized):** Password salah atau email tidak ditemukan di database.
- **Status 404 (Not Found):** Anda salah mengetik alamat URL (cek kembali `/api/auth/login` atau port server).
- **Status 500 (Internal Server Error):** Ada masalah di kode backend (biasanya muncul pesan error detail di terminal VS Code Anda). **Ini adalah tempat pertama yang harus Anda cek untuk mengetahui penyebab kerusakan.**

### Tips Membaca Data `roleId`

Jika Anda ragu `roleId` mana yang harus dipakai (misal: "role-1", "role-2", atau "role-3"), jalankan perintah ini di terminal:

```bash
npx prisma studio

```

Perintah ini akan membuka website lokal yang menampilkan database Anda secara visual. Anda bisa melihat daftar ID yang tersedia di tabel `roles` dan menyalinnya ke body _request_ Postman/Thunder Client.

Apakah Anda sudah berhasil mendapatkan kode `200 OK` saat melakukan login? Jika sudah, sistem keamanan aplikasi Anda sudah berjalan dengan baik.

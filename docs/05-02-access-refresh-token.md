Setelah berhasil membangun gerbang masuk utama, sekarang kita akan meningkatkan keamanannya. Menggunakan satu token saja (seperti di Bab 5.1) sebenarnya berisiko; jika token itu dicuri, peretas bisa masuk selamanya sampai token kedaluwarsa.

Sistem **Access & Refresh Token** bekerja seperti sistem "Kunci Hotel dan Kartu Identitas". _Access Token_ adalah kunci kamar yang cepat mati (pendek), sedangkan _Refresh Token_ adalah kartu identitas di resepsionis yang bisa digunakan untuk meminta kunci baru tanpa harus memasukkan _password_ lagi.

Silakan buat file **`docs/05-02-access-refresh-token.md`**:

---

# 5.2 Mekanisme Access & Refresh Token

Sistem ini memastikan pengguna tidak perlu _login_ berulang kali setiap 15 menit, namun tetap menjaga keamanan jika salah satu token bocor.

## 1. Menyiapkan Variabel Lingkungan (`.env`)

Kita butuh dua rahasia berbeda untuk membedakan kedua jenis token ini. Buka file `.env` Anda dan tambahkan:

```env
JWT_ACCESS_SECRET=rahasia_akses_cepat_123
JWT_REFRESH_SECRET=rahasia_refresh_lama_456

```

Jangan lupa untuk mendaftarkannya juga di file **`src/config/env.config.ts`** agar terbaca oleh TypeScript.

---

## 2. Memperbarui `AuthService` (`src/services/auth.service.ts`)

Kita akan mengubah logika _login_ agar menghasilkan dua token sekaligus dan menambahkan fungsi `refresh`.

```typescript
import prisma from "../config/database.config";
import jwt from "jsonwebtoken";
import { ENV } from "../config/env.config";

export class AuthService {
  // Fungsi Helper untuk membuat Token
  static generateTokens(payload: { id: string; role: string }) {
    const accessToken = jwt.sign(payload, ENV.JWT_ACCESS_SECRET as string, {
      expiresIn: "15m", // Access token hanya hidup 15 menit
    });

    const refreshToken = jwt.sign(payload, ENV.JWT_REFRESH_SECRET as string, {
      expiresIn: "7d", // Refresh token hidup 7 hari
    });

    return { accessToken, refreshToken };
  }

  static async login(email: string, pass: string) {
    // ... (Logika pengecekan password sama seperti 5.1)

    const tokens = this.generateTokens({ id: user.id, role: user.role.name });
    return { user, ...tokens };
  }

  // Fungsi untuk meminta Access Token baru
  static async refreshToken(oldRefreshToken: string) {
    try {
      // 1. Verifikasi apakah refresh token valid
      const decoded = jwt.verify(
        oldRefreshToken,
        ENV.JWT_REFRESH_SECRET as string,
      ) as any;

      // 2. Buat Access Token baru
      const accessToken = jwt.sign(
        { id: decoded.id, role: decoded.role },
        ENV.JWT_ACCESS_SECRET as string,
        { expiresIn: "15m" },
      );

      return accessToken;
    } catch (error) {
      throw new Error("Refresh token tidak valid atau kedaluwarsa!");
    }
  }
}
```

---

## 3. Memperbarui `AuthController` (`src/controllers/auth.controller.ts`)

Tambahkan fungsi `refresh` untuk menangani permintaan token baru dari aplikasi (biasanya dari _frontend_ atau aplikasi _mobile_ Flutter Anda).

```typescript
export const refresh = async (req: Request, res: Response) => {
  try {
    const { refreshToken } = req.body;
    if (!refreshToken) throw new Error("Refresh token diperlukan!");

    const newAccessToken = await AuthService.refreshToken(refreshToken);
    res.status(200).json({ accessToken: newAccessToken });
  } catch (error: any) {
    res.status(401).json({ error: error.message });
  }
};
```

---

## 4. Menambahkan Route Baru (`src/routes/auth.route.ts`)

```typescript
authRouter.post("/refresh", refresh);
```

---

## 5. Keuntungan Struktur Ini untuk Tugas Akhir Anda

Sistem ini sangat disukai penguji karena menunjukkan Anda memahami standar keamanan modern:

- **Keamanan:** Jika _Access Token_ dicuri, peretas hanya punya waktu 15 menit sebelum "kunci" itu mati sendiri.
- **Pengalaman Pengguna (UX):** Pengguna aplikasi POS Anda (kasir) tidak akan terganggu dengan _pop-up login_ tiba-tiba saat sedang melayani pelanggan, karena aplikasi akan meminta token baru di latar belakang menggunakan _Refresh Token_.

---

### Cara Mengujinya:

1. **Login** seperti biasa. Anda sekarang akan menerima `accessToken` dan `refreshToken`.
2. Tunggu 15 menit atau coba gunakan `accessToken` untuk akses data.
3. Jika gagal, kirim `refreshToken` ke endpoint `/api/auth/refresh`.
4. Anda akan mendapatkan `accessToken` baru tanpa perlu memasukkan email/password lagi.

Apakah mekanisme "dua kunci" ini cukup jelas untuk Anda implementasikan, atau ada bagian kode yang ingin kita bedah lebih dalam?

Tentu, ini adalah langkah yang sangat bijak. Karena kita baru saja melakukan perombakan total agar sesuai dengan standar teknologi terbaru (Prisma 7 dan TypeScript Strict), kita perlu memperbarui dokumentasi agar tetap relevan.

Silakan buat file **`docs/03-01-setup-express-ts.md`** dan masukkan materi lengkap yang sudah kita standarisasi ini:

---

```markdown
# 3.1 Setup Project Express.js & TypeScript (Standar Mutakhir)

Bab ini membahas langkah-langkah inisiasi proyek Backend menggunakan Express.js dan TypeScript dengan standar keamanan dan tipe data yang ketat. Fondasi ini dirancang untuk mendukung fitur enterprise seperti multi-cabang dan sinkronisasi data.

## 1. Persiapan Lingkungan (Initialization)

Langkah pertama adalah membuat identitas proyek melalui file `package.json`. Buka terminal di folder proyek Anda dan jalankan:

```bash
npm init -y

```

## 2. Instalasi Library (Dependencies)

Kita menginstal seluruh "senjata" yang dibutuhkan untuk membangun API POS yang aman dan tangguh.

### A. Library Produksi (Running Code)

```bash
npm install express dotenv cors prisma @prisma/client zod jsonwebtoken bcrypt morgan helmet

```

### B. Library Pengembangan (Development & Types)

```bash
npm install -D typescript ts-node-dev @types/express @types/node @types/cors @types/jsonwebtoken @types/bcrypt @types/morgan @prisma/config

```

**Ringkasan Fungsi Library:**

* **Helmet & Morgan:** Keamanan header HTTP dan pencatatan (logging) aktivitas server.
* **Zod:** Validasi data input agar tidak ada data "sampah" masuk ke database.
* **Bcrypt & JWT:** Enkripsi password dan manajemen sesi login.
* **Prisma & Config:** Penghubung database PostgreSQL dengan standar versi terbaru.

## 3. Konfigurasi TypeScript (`tsconfig.json`)

Agar TypeScript mengenali lingkungan Node.js dan menjaga kode tetap rapi, gunakan konfigurasi berikut. Jalankan `npx tsc --init` lalu sesuaikan isinya:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "CommonJS",
    "rootDir": "./src",
    "outDir": "./dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "types": ["node"]
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}

```

## 4. Manajemen Variabel Lingkungan (.env)

Buat file **`.env`** di folder utama untuk menyimpan data sensitif. **Jangan pernah mengunggah file ini ke GitHub.**

```text
PORT=5000
DATABASE_URL="postgresql://postgres:PASSWORD_ANDA@localhost:5432/pos_enterprise_db"
JWT_SECRET="rahasia_super_aman_123_pos_enterprise"

```

Tambahkan juga file **`.gitignore`** untuk mengecualikan file rahasia:

```text
node_modules/
dist/
.env

```

## 5. Konfigurasi Environment yang Type-Safe

Kita memusatkan pemanggilan variabel `.env` di satu tempat agar jika ada variabel yang lupa diisi, aplikasi akan langsung memberi tahu kita saat dijalankan.

Buat file **`src/config/env.config.ts`**:

```typescript
import "dotenv/config";

const requireEnv = (key: string): string => {
  const value = process.env[key];
  if (!value) {
    throw new Error(`Environment variable ${key} belum disetting di file .env!`);
  }
  return value;
};

export const ENV = {
  PORT: process.env.PORT || 5000,
  DATABASE_URL: requireEnv("DATABASE_URL"),
  JWT_SECRET: requireEnv("JWT_SECRET"),
};

```

## 6. Membuat Entry Point (`src/index.ts`)

Inilah file utama yang akan menjalankan server API kita.

```typescript
import express, { Request, Response } from "express";
import cors from "cors";
import helmet from "helmet";
import morgan from "morgan";
import { ENV } from "./config/env.config";

const app = express();

// Middlewares
app.use(express.json());
app.use(cors());
app.use(helmet());
app.use(morgan("dev"));

// Root Route
app.get("/", (req: Request, res: Response) => {
  res.json({
    message: "POS Enterprise API is Running...",
    status: "Healthy"
  });
});

app.listen(ENV.PORT, () => {
  console.log(`🚀 Server berjalan di http://localhost:${ENV.PORT}`);
});

```

## 7. Otomasi Perintah (Scripts)

Buka `package.json`, lalu ubah bagian `"scripts"` untuk mempermudah jalannya aplikasi:

```json
"scripts": {
  "dev": "ts-node-dev --respawn --transpile-only src/index.ts",
  "build": "tsc",
  "start": "node dist/index.js"
}

```

---

```

---

### Progres Anda:
Dengan file `.md` ini, Anda telah mendokumentasikan pondasi yang benar-benar bersih dan sesuai dengan standar industri terbaru. Jika Anda atau tim lain membaca file ini di masa depan, tidak akan ada lagi kebingungan soal *error* tipe data atau variabel `process.env`.

**Apakah materi 3.1 ini sudah cukup lengkap untuk Anda simpan?** Jika sudah, apakah kita lanjut membuat materi untuk **3.2: Struktur Folder Berbasis Domain**? Karena tadi kita sempat mengubah urutan beberapa hal saat proses perbaikan.

```
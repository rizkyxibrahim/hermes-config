# Tool: xibra-api

Definisi tools Hermes untuk berkomunikasi dengan Xibra API (Laravel + Sanctum + PostgreSQL).

## Base URL

```
http://localhost:8000/api/v1
```

## Authentication

Semua endpoint dilindungi oleh Laravel Sanctum. Token Bearer harus disertakan dalam header:

```
Authorization: Bearer <token>
```

Token didapatkan dari `POST /api/v1/auth/login` dengan body:
```json
{
  "identity": "username|email|phone",
  "password": "string"
}
```

## Endpoint Tersedia

### Authentication (Public)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_login` | POST | `/auth/login` | Login dengan username/email/phone + password |
| `xibra_api_logout` | POST | `/auth/logout` | Logout (revoke token) |
| `xibra_api_me` | GET | `/auth/me` | Profil pengguna terautentikasi |

### Schedules (Jadwal)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_get_schedules` | GET | `/schedules` | Daftar jadwal pengguna |
| `xibra_api_create_schedule` | POST | `/schedules` | Buat jadwal baru |
| `xibra_api_get_schedule` | GET | `/schedules/{id}` | Detail jadwal |
| `xibra_api_update_schedule` | PUT | `/schedules/{id}` | Update jadwal |
| `xibra_api_delete_schedule` | DELETE | `/schedules/{id}` | Hapus jadwal |

### Todos (Tugas)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_get_todos` | GET | `/todos` | Daftar tugas pengguna |
| `xibra_api_create_todo` | POST | `/todos` | Buat tugas baru |
| `xibra_api_get_todo` | GET | `/todos/{id}` | Detail tugas |
| `xibra_api_update_todo` | PUT | `/todos/{id}` | Update tugas |
| `xibra_api_delete_todo` | DELETE | `/todos/{id}` | Hapus tugas |

### Reminders (Pengingat)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_get_reminders` | GET | `/reminders` | Daftar pengingat pengguna |
| `xibra_api_create_reminder` | POST | `/reminders` | Buat pengingat baru |
| `xibra_api_get_reminder` | GET | `/reminders/{id}` | Detail pengingat |
| `xibra_api_update_reminder` | PUT | `/reminders/{id}` | Update pengingat |
| `xibra_api_delete_reminder` | DELETE | `/reminders/{id}` | Hapus pengingat |

### Finance Transactions (Keuangan)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_get_transactions` | GET | `/finance/transactions` | Daftar transaksi keuangan |
| `xibra_api_create_transaction` | POST | `/finance/transactions` | Buat transaksi baru |
| `xibra_api_get_transaction` | GET | `/finance/transactions/{id}` | Detail transaksi |
| `xibra_api_update_transaction` | PUT | `/finance/transactions/{id}` | Update transaksi |
| `xibra_api_delete_transaction` | DELETE | `/finance/transactions/{id}` | Hapus transaksi |

### Portfolio Skills (Keterampilan)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_get_portfolio_skills` | GET | `/portfolio/skills` | Daftar keterampilan |
| `xibra_api_create_portfolio_skill` | POST | `/portfolio/skills` | Tambah keterampilan |

### Portfolio Projects (Proyek)

| Tool | Method | Endpoint | Deskripsi |
|------|--------|----------|-----------|
| `xibra_api_get_portfolio_projects` | GET | `/portfolio/projects` | Daftar proyek |
| `xibra_api_create_portfolio_project` | POST | `/portfolio/projects` | Buat proyek baru |

## Contoh Penggunaan (Pseudocode)

```javascript
// Login
const result = await xibra_api_login({
  identity: "rizkyxibrahim",
  password: "password123"
});
const token = result.data.token;

// Set token untuk request berikutnya
api.setToken(token);

// Buat tugas baru
await xibra_api_create_todo({
  title: "Review proyek Xibra",
  priority: "high",
  due_at: "2026-10-30T17:00:00"
});

// Ambil jadwal
const schedules = await xibra_api_get_schedules();
```

## Keamanan

- Jangan menyimpan token dalam file konfigurasi yang di-commit.
- Token disimpan di memori Hermes session atau `.env` profil yang aman.
- Setiap agen hanya boleh mengakses data milik user yang login (ownership check di sisi API).

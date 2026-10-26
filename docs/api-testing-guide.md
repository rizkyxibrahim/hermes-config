# Panduan Testing Xibra API (Postman)

Dokumen ini berisi panduan lengkap untuk menguji semua endpoint Xibra API menggunakan Postman.

## 1. Prasyarat

| Item | Detail |
|------|--------|
| Xibra API | Berjalan di `http://localhost:8000` |
| Database | PostgreSQL `xibra_api` sudah terkonfigurasi |
| Postman | Terinstal di komputer |
| Collection | `xibra-api-postman-collection.json` (sudah disediakan) |

### Menjalankan Xibra API
```bash
cd D:\Projects\xibra-api
php artisan serve --host=127.0.0.1 --port=8000
```

### Menjalankan Seeder (jika perlu data uji)
```bash
php artisan migrate:fresh --seed
```

Seeder akan membuat:
- 1 admin user: `rizkyxibrahim` / `password123`
- 10 user random dengan data acak

## 2. Import Collection ke Postman

1. Buka Postman
2. Klik **Import** → pilih file `xibra-api-postman-collection.json`
3. Collection akan muncul di sidebar dengan nama "Xibra API - Postman Collection"
4. Buka tab **Variables** di collection, isi:
   - `base_url`: `http://localhost:8000`
   - `auth_token`: (kosong, akan diisi otomatis setelah login)

## 3. Urutan Testing (Direkomendasikan)

Jalankan request dalam urutan berikut:

### Langkah 1: Login
- Request: `Authentication > LOGIN - Dapatkan Bearer Token`
- Expected: Status 200, response berisi `data.token`
- Token akan otomatis disimpan ke variabel collection `auth_token`

### Langkah 2: Cek Profil
- Request: `Authentication > ME - Cek Profil Login`
- Expected: Status 200, response berisi data user

### Langkah 3: Test Semua Endpoint CRUD

| Modul | Endpoint GET | Endpoint POST |
|-------|-------------|---------------|
| Schedules | `/api/v1/schedules` | `/api/v1/schedules` |
| Todos | `/api/v1/todos` | `/api/v1/todos` |
| Reminders | `/api/v1/reminders` | `/api/v1/reminders` |
| Finance | `/api/v1/finance/transactions` | `/api/v1/finance/transactions` |
| Portfolio Skills | `/api/v1/portfolio/skills` | `/api/v1/portfolio/skills` |
| Portfolio Projects | `/api/v1/portfolio/projects` | `/api/v1/portfolio/projects` |

### Langkah 4: Test Auth Gagal (Negative Test)
- Coba akses endpoint tanpa token → Expected: Status 401
- Coba login dengan password salah → Expected: Status 422 atau 401

### Langkah 5: Logout
- Request: `Authentication > LOGOUT - Revoke Token`
- Expected: Status 200

## 4. Format Respons API

### Success Response (Envelope Format)
```json
{
  "status": "success",
  "code": "RESOURCE_CREATED",
  "message": "Data berhasil dibuat",
  "data": { ... },
  "meta": { ... }
}
```

### Error Response (RFC 9457 Problem Details)
```json
{
  "type": "https://api.xibra.com/problems/validation-error",
  "title": "Validation Error",
  "status": 422,
  "detail": "The given data was invalid.",
  "instance": "/api/v1/todos",
  "errors": {
    "title": ["The title field is required."]
  }
}
```

## 5. Checklist Testing

- [ ] Login berhasil, token diterima
- [ ] GET schedules mengembalikan array
- [ ] POST schedule berhasil dibuat
- [ ] GET todos mengembalikan array
- [ ] POST todo berhasil dibuat
- [ ] GET reminders mengembalikan array
- [ ] POST reminder berhasil dibuat
- [ ] GET finance transactions mengembalikan array
- [ ] POST finance transaction berhasil dibuat
- [ ] GET portfolio skills mengembalikan array
- [ ] POST portfolio skill berhasil dibuat
- [ ] GET portfolio projects mengembalikan array
- [ ] POST portfolio project berhasil dibuat
- [ ] Unauthorized access ditolak (401)
- [ ] Logout berhasil

## 6. Troubleshooting

| Masalah | Penyebab | Solusi |
|---------|----------|--------|
| Connection refused | Xibra API belum running | Jalankan `php artisan serve` di port 8000 |
| 401 Unauthorized | Token tidak valid atau expired | Login ulang, copy token baru |
| 422 Unprocessable Entity | Data request tidak valid | Periksa format JSON dan field wajib |
| 500 Internal Server Error | Error server | Periksa log Laravel di `storage/logs/laravel.log` |

## 7. File yang Tersedia

| File | Lokasi | Deskripsi |
|------|--------|-----------|
| Postman Collection | `hermes-config/docs/xibra-api-postman-collection.json` | Collection lengkap semua endpoint |
| API Integrations | `hermes-config/docs/integrations.md` | Dokumentasi endpoint lengkap |
| Setup Guide | `hermes-config/docs/setup.md` | Panduan setup Hermes dan API |

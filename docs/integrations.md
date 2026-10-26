# Dokumentasi Integrasi

## Integrasi Xibra API

Semua integrasi Hermes dengan data Xibra dilakukan melalui endpoint REST API Xibra.

### Endpoint yang Tersedia

| Domain | Endpoint | Method | Authentication |
|--------|----------|--------|----------------|
| Auth | `/api/v1/auth/login` | POST | Tidak |
| Auth | `/api/v1/auth/me` | GET | Sanctum Bearer |
| Auth | `/api/v1/auth/logout` | POST | Sanctum Bearer |
| Schedules | `/api/v1/schedules` | GET/POST | Sanctum Bearer |
| Schedules | `/api/v1/schedules/{id}` | GET/PUT/DELETE | Sanctum Bearer |
| Todos | `/api/v1/todos` | GET/POST | Sanctum Bearer |
| Todos | `/api/v1/todos/{id}` | GET/PUT/DELETE | Sanctum Bearer |
| Reminders | `/api/v1/reminders` | GET/POST | Sanctum Bearer |
| Reminders | `/api/v1/reminders/{id}` | GET/PUT/DELETE | Sanctum Bearer |
| Finance | `/api/v1/finance/transactions` | GET/POST | Sanctum Bearer |
| Finance | `/api/v1/finance/transactions/{id}` | GET/PUT/DELETE | Sanctum Bearer |
| Portfolio Skills | `/api/v1/portfolio/skills` | GET/POST | Sanctum Bearer |
| Portfolio Projects | `/api/v1/portfolio/projects` | GET/POST | Sanctum Bearer |

## Integrasi Telegram

### Xibra Bot → Hermes Bot

Pengguna dapat beralih dari Xibra Bot ke Hermes Bot untuk percakapan AI melalui tombol deep link:

```
/ai pertanyaan saya → [Tombol: Buka Chat Hermes Bot]
```

Deep link format:
```
https://t.me/<HERMES_BOT_USERNAME>?start=ai
```

### Hermes Bot → Xibra Bot

Hermes Bot tidak mengirim pesan langsung ke Xibra Bot. Jika Hermes perlu membuat tugas atau jadwal, ia memanggil Xibra API melalui tools yang tersedia, bukan melalui Telegram bot langsung.

## Setup Hermes Profil

Setiap profil Hermes (`xibra-assistant`, `xibra-dev`, `xibra-research`) memiliki konfigurasi terpisah di:

```
C:\Users\Culture\AppData\Local\hermes\profiles\<nama-profil>\
```

File yang relevan:
- `config.yaml` — Konfigurasi CLI Hermes
- `.env` — Variabel rahasia (BOT_TOKEN, API keys, dll.) — **JANGAN COMMIT**
- `SOUL.md` — Instruksi identitas agent

## Menyalin Konfigurasi dari Repository ini

```powershell
# Contoh di PowerShell (Windows)
Copy-Item "D:\Projects\hermes-config\.hermes\agents\xibra-assistant.md" `
  "C:\Users\Culture\AppData\Local\hermes\profiles\xibra-assistant\"
```

## Troubleshooting

| Masalah | Penyebab | Solusi |
|---------|----------|--------|
| 401 Unauthorized | Token tidak valid atau expired | Login ulang via `/api/v1/auth/login` |
| 403 Forbidden | Akses ke data user lain | Pastikan resource milik user yang login |
| Tool tidak ditemukan | Konfigurasi agent belum disalin | Salin file `.hermes/agents/*.md` ke profil Hermes |
| Koneksi API gagal | Xibra API tidak berjalan | Pastikan `php artisan serve` berjalan di port 8000 |

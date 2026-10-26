# Arsitektur Integrasi Hermes + Xibra

## Gambaran Besar

```
Telegram
├── Xibra Bot (grammY + TypeScript)
│   └── Mengakses Xibra API (Laravel + Sanctum + PostgreSQL)
│       ├── /jadwal, /todo, /reminder, /catat, /laporan
│       └── Deep link ke Hermes Bot untuk /ai
│
└── Hermes Bot (Hermes Runtime)
    └── Mengakses Xibra API melalui tools xibra-api
        ├── xibra-assistant → jadwal, tugas, keuangan
        ├── xibra-dev → coding, arsitektur
        └── xibra-research → riset, analisis
```

## Prinsip Arsitektur

1. **Satu backend bisnis**: Xibra API adalah sumber kebenaran tunggal untuk semua data.
2. **Dua bot Telegram terpisah**: Xibra Bot untuk operasi terstruktur, Hermes Bot untuk percakapan AI.
3. **Hermes tidak menyimpan data bisnis**: Semua data diakses melalui Xibra API dengan izin yang tepat.
4. **Setiap agen memiliki akses terbatas**: Agen hanya bisa mengakses data milik user yang login.

## Alur Data

### Contoh: Membuat tugas melalui Hermes

```
Pengguna → Hermes Bot ("Buat tugas review proyek besok pagi")
         → xibra-assistant agent
         → xibra_api_create_todo tool
         → POST /api/v1/todos (dengan Bearer token)
         → Xibra API (validasi, simpan ke PostgreSQL)
         → Respons sukses ke Hermes
         → Hermes Beritahukan ke pengguna
```

### Contoh: Melihat jadwal melalui Xibra Bot

```
Pengguna → Xibra Bot (/jadwal)
         → xibra-bot (grammY)
         → xibra_api_get_schedules tool
         → GET /api/v1/schedules
         → Xibra API
         → Respons jadwal ke Xibra Bot
         → Xibra Bot menampilkan ke pengguna
```

## Keamanan

- Semua akses ke Xibra API memerlukan Sanctum Bearer token.
- Token didapatkan melalui login (`POST /api/v1/auth/login`).
- Setiap request divalidasi ownership di sisi API (user_id).
- Jangan menyimpan token di file konfigurasi yang di-commit.

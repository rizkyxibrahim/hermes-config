# Panduan Setup Hermes untuk Xibra

## Prasyarat

- Hermes Agent sudah terpasang di Windows
- 3 profil Hermes sudah dibuat: `xibra-assistant`, `xibra-dev`, `xibra-research`
- Xibra API sudah berjalan di `http://localhost:8000`

## Langkah Setup

### 1. Clone Repository

```bash
git clone https://github.com/rizkyxibrahim/hermes-config.git
cd hermes-config
```

### 2. Salin Instruksi Agent ke Profil Hermes

Salin file agent dari repository ini ke setiap profil Hermes:

```powershell
# xibra-assistant
Copy-Item ".hermes\agents\xibra-assistant.md" `
  "C:\Users\Culture\AppData\Local\hermes\profiles\xibra-assistant\SOUL.md"

# xibra-dev
Copy-Item ".hermes\agents\xibra-dev.md" `
  "C:\Users\Culture\AppData\Local\hermes\profiles\xibra-dev\SOUL.md"

# xibra-research
Copy-Item ".hermes\agents\xibra-research.md" `
  "C:\Users\Culture\AppData\Local\hermes\profiles\xibra-research\SOUL.md"
```

> Catatan: Profil Hermes sudah memiliki `SOUL.md` bawaan. File di repository ini adalah versi yang disesuaikan untuk Xibra Platform. Salin hanya jika Anda ingin mengganti atau memperbarui instruksi agent.

### 3. Konfigurasi Environment Variabel

Buat file `.env` di setiap profil Hermes (jangan commit ke Git):

```env
# Contoh di profiles/xibra-assistant/.env
API_BASE_URL=http://localhost:8000
```

### 4. Uji Koneksi

Pastikan Xibra API berjalan:

```bash
# Di folder xibra-api
php artisan serve --host=127.0.0.1 --port=8000
```

Lalu uji endpoint:

```bash
curl http://localhost:8000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"identity":"rizkyxibrahim","password":"password123"}'
```

### 5. Menjalankan Hermes Agent

Gunakan Hermes CLI atau desktop app untuk memilih profil dan mulai berinteraksi dengan agent.

## Catatan Keamanan

- **Jangan commit** file `.env`, token, API key, atau kredensial database.
- Setiap profil Hermes memiliki `.env` terpisah.
- File konfigurasi di repository ini hanya berisi instruksi dan dokumentasi, tidak berisi rahasia.

## Struktur Profil Hermes (Referensi)

```
C:\Users\Culture\AppData\Local\hermes\profiles\
├── xibra-assistant/
│   ├── config.yaml
│   ├── .env              # Jangan commit
│   ├── SOUL.md           # Instruksi agent
│   ├── state.db          # Database state Hermes
│   └── ...
├── xibra-dev/
│   └── ...
└── xibra-research/
    └── ...
```

## Referensi

- [Hermes Agent Documentation](https://hermes-agent.nousresearch.com/docs)
- [Xibra API Repository](https://github.com/rizkyxibrahim/xibra-api)
- [Xibra Bot Repository](https://github.com/rizkyxibrahim/xibra-bot)

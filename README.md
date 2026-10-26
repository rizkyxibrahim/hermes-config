# hermes-config

Konfigurasi, instruksi agent, dan integrasi Xibra untuk Hermes AI Agent Runtime.

## Deskripsi

Repository ini berisi konfigurasi dan integrasi Hermes yang dikelola sendiri untuk Xibra Platform. Hermes Runtime itu sendiri adalah instalasi terpisah (tidak ada di repository ini). Repository ini hanya menyimpan:

- Instruksi agen spesialis (`xibra-assistant`, `xibra-dev`, `xibra-research`)
- Definisi tools dan adapter khusus Xibra
- Dokumentasi integrasi antara Hermes dan Xibra API

## Struktur Repository

```
hermes-config/
├── .hermes/
│   ├── agents/
│   │   ├── xibra-assistant.md    # Instruksi agent perencanaan pribadi & keuangan
│   │   ├── xibra-dev.md          # Instruksi agent coding & arsitektur
│   │   └── xibra-research.md     # Instruksi agent riset & analisis
│   └── tools/
│       ├── xibra-api.md          # Definisi tools untuk Xibra API
│       └── telegram-bridge.md    # Adapter bridge ke Xibra Bot Telegram
├── docs/
│   ├── architecture.md           # Arsitektur integrasi Hermes + Xibra
│   ├── integrations.md           # Dokumentasi integrasi endpoint API
│   └── setup.md                  # Panduan setup Hermes untuk Xibra
├── examples/
│   └── hermes-config.yaml        # Contoh konfigurasi Hermes
├── .gitignore
└── README.md
```

## Agen Spesialis

| Agen | Tanggung Jawab | File |
|------|---------------|------|
| `xibra-assistant` | Perencanaan pribadi, rutinitas, jadwal, tugas, keuangan | `.hermes/agents/xibra-assistant.md` |
| `xibra-dev` | Coding, debugging, arsitektur software, pengelolaan proyek teknis | `.hermes/agents/xibra-dev.md` |
| `xibra-research` | Riset, analisis, bisnis, konten, informasi | `.hermes/agents/xibra-research.md` |

## Tools Hermes

| Tools | Fungsi | File |
|-------|--------|------|
| `xibra-api` | Memanggil endpoint Xibra API (jadwal, todo, keuangan, dll.) | `.hermes/tools/xibra-api.md` |
| `telegram-bridge` | Bridge ke Xibra Bot Telegram untuk notifikasi dan command | `.hermes/tools/telegram-bridge.md` |

## Penting

⚠️ **Jangan commit token, API key, database credentials, session database, atau file pribadi** ke repository ini.

Hermes Runtime yang sebenarnya terpasang di:
```
C:\Users\Culture\AppData\Local\hermes\profiles\
```

Profil yang ada:
- `xibra-assistant`
- `xibra-dev`
- `xibra-research`

## Langkah Setup

1. Clone repository ini
2. Salin file agent dan tools ke direktori profil Hermes yang sesuai
3. Konfigurasi `.env` di setiap profil Hermes (jangan commit)
4. Uji koneksi ke Xibra API melalui tools yang didefinisikan

## Lisensi

To be decided before public distribution.

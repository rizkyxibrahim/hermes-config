# Tool: telegram-bridge

Adapter bridge untuk integrasi Hermes Agent dengan Xibra Bot Telegram.

## Tujuan

Hermes Bot dan Xibra Bot adalah dua bot Telegram terpisah. Tool ini memungkinkan Hermes Agent untuk:

1. Mengirim notifikasi ke pengguna melalui Xibra Bot (jika bridge tersedia)
2. Menerima perintah dari Xibra Bot dan mendelegasikannya ke Hermes Agent
3. Membuat deep link ke Hermes Bot untuk percakapan AI

## Endpoint Deep Link

Untuk mengarahkan pengguna ke Hermes Bot dari Xibra Bot:

```
https://t.me/<HERMES_BOT_USERNAME>?start=ai
```

Contoh:
```
https://t.me/hermes_bot?start=ai
```

## Contoh Penggunaan di Hermes Agent

Saat pengguna mengetik `/ai pertanyaan saya` di Xibra Bot:

1. Xibra Bot merespons dengan tombol deep link ke Hermes Bot
2. Pengguna klik tombol untuk membuka chat Hermes Bot
3. Hermes Bot menerima pertanyaan dan memprosesnya sebagai agent percakapan AI

## Catatan

- Hermes Bot adalah antarmuka Telegram menuju Hermes Runtime.
- Xibra Bot adalah bot operasional terstruktur (jadwal, todo, keuangan, dll.).
- Keduanya tidak sama, tetapi dapat saling melengkapi melalui deep link dan integrasi API.

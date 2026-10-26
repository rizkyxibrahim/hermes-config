# Panduan Deploy Xibra Bot ke Cloudflare Worker

Dokumen ini berisi panduan untuk mendeplyoment `xibra-bot` (grammY + TypeScript) ke Cloudflare Worker menggunakan webhook mode.

## ⚠️ Perbedaan Penting: Local vs Cloudflare

| Aspek | Lokal (Development) | Cloudflare Worker (Produksi) |
|-------|---------------------|------------------------------|
| Mode | Polling (grammY bot.polling()) | Webhook (Telegram → Worker URL) |
| Server | `php artisan serve` + `npm run dev` | Cloudflare Workers (serverless) |
| Database | PostgreSQL lokal (Laragon) | PostgreSQL eksternal atau Cloudflare D1 |
| Bot Token | Di `.env` lokal | Di Cloudflare Secrets/Variables |

---

## 1. Prasyarat

| Tool | Versi | Cara Install |
|------|-------|--------------|
| Wrangler CLI | Terbaru | `npm install -g wrangler` |
| Node.js | v20+ | Sudah terinstall |
| Cloudflare Account | Gratis/Berbayar | Daftar di cloudflare.com |

### Login ke Wrangler
```bash
wrangler login
```

---

## 2. Struktur Project untuk Cloudflare Worker

```
xibra-bot/
├── src/
│   ├── index.ts          # Entry point untuk webhook
│   ├── bot.ts            # Logika bot grammY (diadaptasi)
│   ├── services/api.service.ts
│   └── commands/         # Semua command Telegram
├── wrangler.toml         # Konfigurasi Cloudflare Worker
├── package.json
├── tsconfig.json
└── .dev.vars             # Variabel lokal (jangan commit)
```

---

## 3. Konfigurasi wrangler.toml

Buat file `wrangler.toml` di root `xibra-bot`:

```toml
# wrangler.toml
name = "xibra-bot"
main = "src/index.ts"
compatibility_date = "2026-10-27"

[vars]
# Variabel publik (bisa diubah via dashboard)
PUBLIC_BASE_URL = "https://xibra-bot.rizkyxibrahim.workers.dev"
TELEGRAM_FRONTEND = "hermes"
AUTO_APPROVE = "false"

[observability]
enabled = true
```

### Environment Variables (Secrets)

Variabel rahasia harus diatur sebagai **Secrets** (tidak terlihat di dashboard):

```bash
cd D:\Projects\xibra-bot

# Set BOT_TOKEN dari @BotFather
wrangler secret put BOT_TOKEN

# Set OWNER_ID (Telegram user ID Anda)
wrangler secret put OWNER_ID

# Set INTERNAL_API_SECRET (untuk validasi internal)
wrangler secret put INTERNAL_API_SECRET

# Set TOKEN_KEY (untuk enkripsi token)
wrangler secret put TOKEN_KEY

# Set WEBHOOK_SECRET (untuk validasi webhook Telegram)
wrangler secret put WEBHOOK_SECRET

# Set GEMINI_MODEL (jika menggunakan AI)
wrangler secret put GEMINI_MODEL

# Set GEMINI_FALLBACK_MODEL
wrangler secret put GEMINI_FALLBACK_MODEL
```

Lihat daftar semua secret:
```bash
wrangler secret list
```

---

## 4. Code Webhook Handler (src/index.ts)

Berikut contoh sederhana untuk menterjemahkan grammY bot menjadi Cloudflare Worker webhook handler:

```typescript
// src/index.ts
import { Bot, webhookCallback } from "grammy";
import { type Context } from "grammy";

// Inisialisasi bot dengan token dari environment
const bot = new Bot(process.env.BOT_TOKEN!);

// Registrasi semua command (adaptasi dari bot.ts asli)
bot.command("start", (ctx) => ctx.reply("Selamat datang di Xibra Bot!"));
bot.command("menu", (ctx) => ctx.reply("Menu: /jadwal, /todo, /reminder, /catat, /laporan, /saldo, /ai"));
bot.command("ai", (ctx) => 
  ctx.reply("Buka chat Hermes Bot: https://t.me/hermes_bot", {
    reply_markup: {
      inline_keyboard: [[
        { text: "Buka Hermes Bot", url: "https://t.me/hermes_bot?start=ai" }
      ]]
    }
  })
);

// Middleware auth sederhana (sesuaikan dengan implementasi asli)
bot.use(async (ctx, next) => {
  // Logika auth bisa ditambahkan di sini
  await next();
});

// Export handler untuk Cloudflare Worker
export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    // Handle webhook dari Telegram
    if (request.method === "POST" && request.url.includes("/webhook")) {
      const update = await request.json() as any;
      const handleUpdate = webhookCallback(bot, "std/http");
      return handleUpdate(update);
    }

    // Health check endpoint
    if (request.url.endsWith("/health")) {
      return new Response("OK", { status: 200 });
    }

    return new Response("Not Found", { status: 404 });
  },
} satisfies ExportedHandler<Env>;

// Type untuk environment variables
interface Env {
  BOT_TOKEN: string;
  OWNER_ID: string;
  INTERNAL_API_SECRET: string;
  TOKEN_KEY: string;
  WEBHOOK_SECRET: string;
  GEMINI_MODEL?: string;
  GEMINI_FALLBACK_MODEL?: string;
  PUBLIC_BASE_URL: string;
  TELEGRAM_FRONTEND: string;
  AUTO_APPROVE: string;
}
```

---

## 5. Set Webhook Telegram

Setelah worker di-deploy, Anda perlu memberitahu Telegram untuk mengirim update ke URL worker:

```bash
# Ganti dengan URL worker Anda
WEBHOOK_URL="https://xibra-bot.rizkyxibrahim.workers.dev/webhook"

curl -X POST "https://api.telegram.org/bot$BOT_TOKEN/setWebhook" \
  -H "Content-Type: application/json" \
  -d "{\"url\": \"$WEBHOOK_URL\"}"
```

Verifikasi webhook:
```bash
curl "https://api.telegram.org/bot$BOT_TOKEN/getWebhookInfo"
```

---

## 6. Deploy ke Cloudflare Worker

```bash
cd D:\Projects\xibra-bot

# Build dan deploy
wrangler deploy

# Atau untuk development (local tunnel)
wrangler dev
```

Setelah deploy, Anda akan mendapat URL seperti:
```
https://xibra-bot.rizkyxibrahim.workers.dev
```

---

## 7. Database untuk Cloudflare

Xibra bot saat ini mengakses data melalui **Xibra API** (`http://localhost:8000`), bukan langsung ke database.

| Pilihan | Keuntungan | Kerugian |
|--------|-----------|----------|
| **Xibra API eksternal** (direkomendasikan) | Tidak perlu ubah arsitektur, satu sumber data | Perlu server API selalu online |
| **Cloudflare D1** | Serverless, gratis tier tersedia | SQLite, perlu migrasi skema |
| **PostgreSQL eksternal** | Kompatibel dengan xibra-api | Biaya tambahan, konfigurasi koneksi |

**Rekomendasi:** Pertahankan Xibra API sebagai backend pusat. Bot di Cloudflare Worker memanggil API via HTTP, tidak perlu database langsung di worker.

---

## 8. Variabel Lingkungan (Dari Contoh Anda)

Berdasarkan variabel yang Anda sediakan, berikut mapping ke Cloudflare:

| Variabel | Tipe | Keterangan |
|----------|------|------------|
| `BOT_TOKEN` | Secret | Token dari @BotFather |
| `GEMINI_MODEL` | Secret | Model AI utama |
| `GEMINI_FALLBACK_MODEL` | Variable | Model AI cadangan |
| `INTERNAL_API_SECRET` | Secret | Kunci internal API |
| `OWNER_ID` | Variable | Telegram user ID Anda |
| `PUBLIC_BASE_URL` | Variable | URL publik worker |
| `TELEGRAM_FRONTEND` | Secret | Frontend Telegram target |
| `TOKEN_KEY` | Secret | Kunci enkripsi token |
| `WEBHOOK_SECRET` | Secret | Kunci validasi webhook |

### Cara set di Cloudflare Dashboard
1. Buka [dash.cloudflare.com](https://dash.cloudflare.com)
2. Pilih **Workers & Pages** → pilih worker `xibra-bot`
3. Buka tab **Settings** → **Variables**
4. Tambahkan **Variables** (publik) dan **Secrets** (rahasia)

---

## 9. Checklist Deploy

- [ ] Wrangler CLI terinstal dan login
- [ ] `wrangler.toml` dibuat di root xibra-bot
- [ ] Semua secret diatur via `wrangler secret put`
- [ ] `src/index.ts` diadaptasi untuk webhook mode
- [ ] Worker di-deploy via `wrangler deploy`
- [ ] Webhook Telegram diatur ke URL worker
- [ ] Bot merespons command di Telegram

---

## 10. Catatan

- Jika Anda ingin tetap menggunakan **polling mode**, Anda butuh server yang selalu online (VPS), bukan Cloudflare Worker.
- Untuk production, pertimbangkan menggunakan **Cloudflare Durable Objects** atau **KV** untuk menyimpan session/token auth.
- Jangan commit file `.dev.vars` atau file yang berisi secret ke GitHub.

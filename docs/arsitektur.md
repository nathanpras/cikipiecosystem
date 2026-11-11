# Arsitektur CiKiPi

Satu database Postgres (Supabase) adalah sumber kebenaran. Setiap aplikasi adalah route group di satu
proyek Next.js, dipasang sebagai PWA. Tidak ada salinan data di aplikasi lain kecuali cache offline kasir.

```
Yang mengisi data                     Yang memakai data
- Kasir (POS)                         - Aplikasi owner (dashboard, laporan, keuangan)
- Dapur dan karyawan    ──►  DB  ──►  - Tugas terjadwal (laporan malam, pengingat)
- Website dan member (G3)             - Pemasaran (segmen, kampanye, konten)
- Impor CSV dan foto nota             - Pembukuan dan pajak
```

## Aplikasi

| Route group | Pengguna | Perangkat | Gelombang |
| --- | --- | --- | --- |
| `(kasir)/kasir` | kasir, owner | Tablet Android di kasir | 1 |
| `(dapur)/dapur` | dapur, owner | Tablet yang sama dulu; tablet kedua mulai Gelombang 3 | 1 |
| `(owner)/owner` | owner | HP pendiri | 1 |
| `(publik)` | pelanggan umum | HP pelanggan, TV antrean | 3 |

## Login, peran, dan perangkat

- **Owner** login dengan Supabase Auth (email). JWT `app_metadata.role = 'owner'`.
- **Perangkat outlet** login sekali sebagai akun perangkat (`app_metadata.role = 'device'`,
  `app_metadata.outlet_id`). Akun perangkat tidak punya hak apa pun tanpa staf yang membuka kunci.
- **Staf** memilih nama lalu memasukkan PIN 4–6 digit. RPC `staff_unlock(staff_id, pin)` memeriksa hash
  (`pgcrypto` `crypt()`), membuat baris `device_sessions` dengan `expires_at` (default 12 jam), dan
  mengembalikan `session_id`. Semua RPC tulis dari perangkat menerima `session_id` dan memakai `staff_id`
  dari sesi itu, bukan dari input klien.
- Layar terkunci kembali setelah tidak aktif (setting `device.lock_after_minutes`, awal 10).
- Peran staf: `owner`, `kasir`, `dapur`. Helper SQL `app_role()` dan `app_outlet()` dipakai di semua policy.

## Alur penjualan

1. Kasir memilih menu, varian, jumlah; opsional member (cari HP) dan penukaran poin.
2. Pembayaran: tunai, QRIS, transfer, atau gabungan (pembayaran sebagian boleh, seperti POS lama).
3. Klien memanggil RPC `record_sale(payload)` dengan `client_uuid`. Dalam satu transaksi database RPC:
   - menolak `client_uuid` yang sudah ada (mengembalikan order yang sudah tersimpan)
   - membuat `orders`, `order_items`, `payments`
   - membuat `stock_movements` kind `sale` dari `recipe_lines` (paket diurai lewat `package_components`)
   - menandai `order_items.batch_id` dengan batch marinasi aktif untuk varian itu
   - membuat `point_ledger` earn (dan redeem kalau dipakai) sesuai `settings`
   - mengembalikan nomor antrean dan data struk
4. Cetak struk pelanggan + tiket dapur. Layar feedback (P4) muncul di tablet untuk pelanggan.

Void lewat RPC `void_order(order_id, reason, session_id)`: membalik stok dan poin dengan baris ledger baru.

## Offline (wajib untuk kasir)

- Service worker menyimpan app shell. Menu, varian, harga, settings, dan daftar member yang pernah dicari
  disimpan di IndexedDB dan disegarkan saat online.
- Transaksi offline masuk antrean IndexedDB dengan `client_uuid` (UUID v4) dan `created_at` perangkat.
  Antrean dikirim berurutan saat online; respons duplikat dianggap sukses.
- Nomor antrean offline: `<kode perangkat>-<nomor lokal>`; server menyimpan nomor apa adanya.
- Penukaran poin dan pendaftaran member baru butuh online. Saat offline, tombolnya dinonaktifkan
  dengan pesan jelas; poin earn dihitung server saat sinkron.
- Badge di layar kasir: "Online", "Offline — N transaksi menunggu", "Menyinkronkan".

## Cetak struk

- Perangkat saat ini: tablet Android + printer **Blueprint BP-LITE80D1 (80 mm)** lewat aplikasi **RawBT**
  (driver "ESC/POS General", perintah grafis GS v 0). Cetak dengan `window.print()`; RawBT terdaftar sebagai
  layanan cetak Android.
- CSS cetak: `@page { size: 80mm auto; margin: 0 }`, lebar konten 72 mm, font monospace, hitam putih.
- Dua template seperti POS lama: **struk pelanggan** (logo, item, total, nomor antrean besar, poin, link feedback)
  dan **tiket dapur** (nomor antrean, item, varian, catatan, tanpa harga). Satu tombol bisa mencetak keduanya.
- **Label batch marinasi** 80 mm: kode batch, QR, varian, berat, tanggal dibuat, kedaluwarsa.
- POS lama punya pembungkus APK (WebView + PrintManager). Boleh dipakai ulang dengan mengarahkan URL ke PWA baru.

## Realtime

- Dashboard owner (O1) dan layar dapur (K6) berlangganan Supabase Realtime pada `orders` dan `order_items`
  per outlet. Dashboard tidak boleh polling lebih sering dari 30 detik kalau realtime gagal.

## Tugas terjadwal

pg_cron memanggil Edge Function. Jadwal ditulis dalam WIB; cron Supabase memakai UTC (WIB = UTC+7).

| WIB | Cron UTC | Tugas | Modul |
| --- | --- | --- | --- |
| Tiap jam 11.00–21.00 | `0 4-14 * * *` | Cek pemicu: stok < min, minyak lewat batas, feedback ≤ 2, selisih kas | O2, K3, K8, P5 |
| 21.30 setiap hari | `30 14 * * *` | Laporan harian + ringkasan Claude | O2 |
| 21.45 setiap hari | `45 14 * * *` | Forecast dan rencana marinasi besok | O7, K7 |
| 08.00 setiap hari | `0 1 * * *` | Draf pesan ulang tahun dan member hilang | M2 |
| Senin 08.00 | `0 1 * * 1` | Laporan mingguan, draf konten, daftar belanja | O2, M3, K10 |
| Tanggal 1, 07.00 | `0 0 1 * *` | Laba rugi, HPP ulang, menu engineering, dana ruko | O3, O4, O5, O10 |
| Tanggal 5, 08.00 | `0 1 5 * *` | Hitung PPh final bulan lalu + pengingat | F3 |
| Minggu 22.00 | `0 15 * * 0` | Ekspor semua tabel ke Excel di Storage privat | cadangan |

Setiap tugas menulis hasilnya ke `reports` atau `notifications`, idempoten per periode (jalan dua kali = satu hasil).

## Notifikasi

- Web Push ke PWA owner (kunci VAPID di env). Cadangan: email ke owner.
- Notifikasi disimpan di tabel `notifications` dan tampil di aplikasi owner.
- Pesan ke pelanggan (WhatsApp) sampai Gelombang 3 hanya lewat link `https://wa.me/<nomor>?text=<pesan>`
  yang dibuka manusia. WhatsApp Business API baru dipakai setelah pendiri menyetujui biaya.

## AI (Claude API)

- Hanya dipanggil dari server (`src/lib/ai/`, route handler, atau Edge Function). Kunci di `ANTHROPIC_API_KEY`.
- Model dari env `ANTHROPIC_MODEL` (default `claude-sonnet-5-5`); tugas ringan boleh `ANTHROPIC_MODEL_FAST`
  (default `claude-haiku-5-5`).
- Setiap fitur punya file prompt sendiri, input terstruktur, dan output divalidasi Zod.

| Fitur | Input | Output | Catatan |
| --- | --- | --- | --- |
| O2 ringkasan laporan | JSON metrik hari itu | Teks 5–8 kalimat Bahasa Indonesia | Angka hanya dari input, tidak menghitung sendiri |
| O8 baca nota | Foto nota | JSON item, qty, satuan, harga, total, supplier | Selalu draf, dikonfirmasi manusia |
| O12 tanya data | Pertanyaan owner | Jawaban + angka sumber | Tool use dengan fungsi baca yang sudah ditentukan; tidak ada SQL bebas |
| K16 asisten SOP | Pertanyaan kru | Jawaban + nama SOP sumber | Hanya dari dokumen SOP; jawab "belum ada di SOP" kalau tidak ada |
| P4 rangkum feedback | Feedback seminggu (tanpa nama/HP) | 3 masalah teratas + batch terkait | |
| M2, M3, M7, P9 | Data agregat | Draf pesan, caption, balasan ulasan, penawaran | Draf, disetujui owner |

- Catat pemakaian token per fitur di `ai_usage` untuk memantau biaya.
- Jangan mengirim nama atau HP pelanggan kecuali fitur itu perlu (misalnya personalisasi pesan, dan itu dilakukan
  dengan template di server, bukan oleh Claude).

## Integrasi

| Layanan | Cara | Gelombang |
| --- | --- | --- |
| QRIS statis | Kasir menandai metode QRIS; laporan bank diimpor CSV untuk rekonsiliasi (F2) | 1–2 |
| Payment gateway (QRIS dinamis) | Webhook ke `src/app/api/payments/webhook`; verifikasi signature; idempoten | 3 |
| GoFood, GrabFood, ShopeeFood | Pesanan ojol diinput di POS dengan channel `ojol`; laporan pencairan diimpor CSV | 2 |
| WhatsApp | Link `wa.me` dulu; API lewat penyedia resmi setelah disetujui | 2 / 3 |
| Google Business Profile | Owner menyalin draf balasan ulasan; belum ada sambungan API | 2 |
| Canva | Simpan link desain per konten atau promo | 2 |
| Excel | Ekspor laporan dan cadangan mingguan | 1 |
| Perangkat perekam pajak pemda | Ekspor transaksi sesuai format yang diminta Bapenda | 3 |

## Migrasi dari POS lama

- POS lama: aplikasi Android (WebView) dengan database lokal tablet; PIN default admin dan kasir; ada fitur
  shift, void, stok, member, poin, struk + tiket dapur, ekspor CSV dari menu Laporan.
- Buat `scripts/import-pos-lama.ts` yang membaca CSV ekspor: member (nama, HP) dan transaksi.
  Normalisasi HP, buang duplikat, tandai member lama `consent_version = 'lama-belum-konfirmasi'`
  dan minta persetujuan saat transaksi berikutnya.
- Jangan pakai daftar menu POS lama; menu resmi ada di `docs/bisnis.md`.

## Tahap publik (Gelombang 3)

- Route group `(publik)` memakai anon key. Semua tulis lewat RPC `security definer` dengan validasi dan rate limit.
- Struk digital dan feedback QR memakai token acak per order (`orders.public_token`), bukan id berurutan.
- Login member dengan OTP; kanal OTP (WhatsApp atau SMS) diputuskan pendiri.
- Halaman publik: SEO lokal, `sitemap.xml`, schema.org `Restaurant`, metadata per halaman.

## Lingkungan

`.env.local` (jangan di-commit; contoh di `.env.example`):

```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=        # server saja
ANTHROPIC_API_KEY=                # server saja
ANTHROPIC_MODEL=claude-sonnet-5-5
ANTHROPIC_MODEL_FAST=claude-haiku-5-5
VAPID_PUBLIC_KEY=
VAPID_PRIVATE_KEY=                # server saja
PAYMENT_GATEWAY_SERVER_KEY=       # Gelombang 3, server saja
```

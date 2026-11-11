# CiKiPi — Sistem Operasional Digital

CiKiPi adalah usaha jajanan ayam crispy ala Taiwan: 150 g daging, digoreng dadakan, Rp38.000.
Kiosk Green Lake City buka November 2026. Ruko Gading Serpong target September 2027.
Pemilik: Jonathan Prasetyo (operasional, produksi) dan Novelie Baby Paramitha (pemasaran, keuangan), 50:50.
Keduanya bukan programmer. Jelaskan setiap keputusan teknis dengan bahasa sederhana dan singkat.

Prinsip utama: **satu database inti**. Kasir, dapur, owner, member, dan website hanya jendela
ke database yang sama. Tidak ada angka yang diketik dua kali.

## Dokumen rujukan

Baca dokumen yang relevan SEBELUM mengerjakan modul. Jangan menebak aturan bisnis.

- `docs/bisnis.md` — menu, harga, HPP, BEP, KPI, jam ramai, aturan operasional, pajak
- `docs/arsitektur.md` — aplikasi, peran dan login, offline, cetak struk, jadwal, AI, integrasi
- `docs/skema-data.md` — semua tabel, kolom, settings, RPC, dan aturan akses (RLS)
- `docs/modul.md` — 60 modul (kode O, P, K, M, F) dengan kriteria selesai
- `docs/roadmap.md` — urutan gelombang dan status tiap modul (selalu diperbarui)

## Stack

- Next.js (App Router) + TypeScript `strict` + Tailwind CSS
- Supabase: Postgres, Auth, Row Level Security, Realtime, Storage, Edge Functions, pg_cron
- Hosting Vercel. Dipasang sebagai PWA di tablet Android kasir dan HP pendiri
- Claude API (Anthropic TypeScript SDK) hanya dari server, untuk fitur AI
- Validasi input dengan Zod. Tes: Vitest (unit), Playwright (alur kasir)

## Perintah

- `npm run dev` — jalankan lokal
- `npm run lint` dan `npm run typecheck` — wajib lolos sebelum commit
- `npm test` — unit test; `npm run test:e2e` — Playwright
- `npx supabase start` — database lokal
- `npx supabase migration new <nama>` — migrasi baru
- `npx supabase db reset` — bangun ulang database lokal dari migrasi + `supabase/seed.sql`
- `npm run gen:types` — regenerasi tipe TypeScript dari skema, wajib setelah setiap migrasi

Kalau script belum ada di `package.json`, buat saat Langkah 0 di `docs/roadmap.md`.

## Struktur folder

```
src/app/(kasir)/kasir/     POS: transaksi, member, shift, struk, feedback iPad
src/app/(dapur)/dapur/     log minyak, batch marinasi, QC, stok, checklist, KDS
src/app/(owner)/owner/     dashboard, laporan, HPP, keuangan, pemasaran, pengaturan
src/app/(publik)/          Gelombang 3: member, pre-order, feedback QR, info, struk digital
src/app/api/               route handler: AI, webhook pembayaran, impor CSV
src/components/ui/         komponen dasar bermerek CiKiPi
src/lib/db/                klien Supabase + tipe hasil gen:types
src/lib/pos/               keranjang, poin, promo — fungsi murni + tes
src/lib/finance/           HPP, BEP, laba rugi, pajak — fungsi murni + tes
src/lib/offline/           antrean IndexedDB + sinkronisasi
src/lib/print/             template struk 80 mm, tiket dapur, label QR batch
src/lib/ai/                pemanggil Claude API + prompt (file terpisah per fitur)
supabase/migrations/       migrasi SQL (tidak pernah diedit setelah di-push)
supabase/functions/        Edge Functions: laporan, pengingat, impor
supabase/seed.sql          data contoh: menu, resep, staf, transaksi palsu
scripts/                   skrip satu kali, misalnya impor data POS lama
```

## Aturan wajib

1. Teks UI Bahasa Indonesia. Kode, nama tabel, dan nama kolom bahasa Inggris.
2. Uang selalu integer rupiah (`bigint`), tanpa desimal. Tampilkan dengan format `Rp38.000` (locale `id-ID`).
3. Waktu disimpan `timestamptz`. Semua tampilan, laporan, dan arti "hari ini" memakai zona `Asia/Jakarta`.
4. Setiap tabel baru wajib RLS aktif + policy di migrasi yang sama. Data keuangan hanya untuk peran `owner`.
5. Transaksi tidak pernah dihapus. Batal = status `void` + alasan + siapa + kapan, lewat RPC `void_order`.
6. Stok hanya berubah lewat `stock_movements`, poin hanya lewat `point_ledger`. Saldo = jumlah ledger.
   Jangan pernah menyimpan kolom saldo yang bisa diedit langsung.
7. Angka bisnis yang bisa berubah (harga, aturan poin, tarif pajak, batas HPP, target, batas minyak)
   disimpan di tabel `settings` atau tabel master, bukan di kode.
8. Kasir harus tetap jalan saat internet putus: transaksi masuk antrean IndexedDB dengan `client_uuid`,
   dikirim ulang saat online, dan server menolak duplikat `client_uuid`.
9. Penjualan dicatat atomik lewat RPC `record_sale`: order, item, pembayaran, stok, dan poin dalam satu transaksi database.
10. Nomor HP dinormalisasi ke format `08…`: hapus non-digit, awalan `62` diganti `0`.
11. Member baru wajib persetujuan data tercatat (`consent_at`, `consent_version`) sesuai UU PDP 27/2022.
    Hormati `wa_opt_out`.
12. Kunci API (Anthropic, Supabase service role, payment gateway) hanya di server dan env, tidak pernah ke browser.
13. Void, diskon manual, perubahan harga, koreksi stok, dan perubahan settings tercatat di `audit_log`.
14. Migrasi yang sudah di-push tidak diedit. Perubahan skema = migrasi baru.
15. Hasil AI yang mengubah data (misalnya nota belanja yang dibaca Claude) selalu disimpan sebagai draf
    dan baru berlaku setelah dikonfirmasi manusia.

## Peran

| Peran | Siapa | Boleh |
| --- | --- | --- |
| `owner` | Jonathan, Novelie | Semua, termasuk keuangan, pengaturan, laporan |
| `kasir` | Kru kasir | Transaksi, member, shift sendiri, feedback; tidak melihat laba atau biaya |
| `dapur` | Kru dapur | Log minyak, batch, QC, stok, checklist, layar dapur |

Tablet outlet login sekali sebagai akun perangkat. Staf berganti dengan PIN. Detail di `docs/arsitektur.md`.

## Cara kerja

- Mulai dari `docs/roadmap.md`. Ambil modul berikutnya di gelombang aktif.
  Jangan mengerjakan gelombang berikutnya tanpa izin pendiri.
- Satu modul per branch dan per PR. Sebelum menulis kode, tulis rencana singkat: tabel, layar, tes.
  Minta persetujuan dulu kalau rencana menyentuh skema database, uang, atau data pelanggan.
- Aturan bisnis tidak jelas atau bertentangan dengan docs: tanya, jangan mengarang.
  Setelah dijawab, catat jawabannya di dokumen yang sesuai.
- Logika hitungan (harga, poin, HPP, BEP, pajak, forecast) ditulis sebagai fungsi murni di `src/lib/`
  dengan unit test yang memakai contoh angka dari `docs/bisnis.md`.
- Setelah modul selesai: perbarui status di `docs/roadmap.md`, lalu tulis ringkasan 3–5 kalimat
  untuk pendiri: apa yang sekarang bisa dipakai, cara mencobanya, dan apa yang belum.

## Definisi selesai

- `npm run lint`, `npm run typecheck`, dan `npm test` lulus
- RLS diuji: peran `kasir` dan `dapur` tidak bisa membaca tabel keuangan
- Layar dicoba di lebar tablet 800 px dan HP 390 px, termasuk mode offline untuk layar kasir
- Minimal satu contoh angka di layar dicocokkan dengan hitungan manual dari `docs/bisnis.md`
- `supabase/seed.sql` diperbarui supaya modul bisa dicoba setelah `npx supabase db reset`

## Jangan

- Jangan memakai localStorage untuk data bisnis; hanya untuk preferensi tampilan
- Jangan mengirim WhatsApp, email, atau notifikasi ke pelanggan tanpa tombol konfirmasi manusia
- Jangan menyimpan data kartu, password pelanggan, atau nomor KTP
- Jangan memakai data pelanggan asli di seed, tes, atau contoh
- Jangan mengirim nama atau nomor HP pelanggan ke Claude API kecuali fitur itu benar-benar membutuhkannya
- Jangan menambah library besar tanpa menulis alasannya di PR

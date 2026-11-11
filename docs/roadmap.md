# Roadmap pembangunan

Claude Code memperbarui file ini setiap kali modul berubah status.
Status: `[ ]` belum · `[~]` dikerjakan · `[x]` selesai dan sudah dicoba pendiri.
Satu modul baru dipakai di kiosk minimal 2 minggu sebelum modul berikutnya di area yang sama diluncurkan.

**Gelombang aktif: 1** · Terakhir diperbarui: 8 November 2026

## Langkah 0 — Setup proyek

- [ ] Inisialisasi Next.js + TypeScript strict + Tailwind; script `dev`, `lint`, `typecheck`, `test`, `test:e2e`, `gen:types`
- [ ] Supabase lokal (`npx supabase init`), proyek Supabase cloud, `.env.example`
- [ ] Token warna brand (burgundy, krem) dan komponen dasar: tombol besar untuk tablet, angka rupiah, badge status online
- [ ] PWA: manifest, ikon, service worker app shell
- [ ] Deploy preview ke Vercel; README singkat cara menjalankan
- [ ] Migrasi pertama: `outlets`, `staff`, `devices`, `device_sessions`, `settings`, `audit_log` + RLS + seed

## Gelombang 1 — Fondasi (November–Desember 2026, kiosk buka)

Tujuan: semua transaksi kiosk masuk database inti, dua masalah dapur terbesar (minyak, rasa antar-batch) mulai tercatat,
dan pendiri melihat omzet vs BEP setiap hari.

Urutan pengerjaan:

- [ ] K1 PIN dan hak akses
- [ ] Menu, varian, paket, bahan, resep (master data + layar pengaturan owner)
- [ ] Kasir inti: keranjang, member, pembayaran, `record_sale`, shift, void, struk + tiket dapur 80 mm, offline
- [ ] Impor data POS lama (`scripts/import-pos-lama.ts`)
- [ ] P2 Loyalitas (poin)
- [ ] K8 Resep dan stok otomatis
- [ ] O4 HPP hidup
- [ ] K4 Batch marinasi + label QR
- [ ] K3 Log minyak
- [ ] K5 QC sampel
- [ ] P4 Feedback rasa (versi tablet)
- [ ] K2 Checklist buka-tutup
- [ ] O1 Dashboard live
- [ ] O2 Laporan otomatis + notifikasi owner
- [ ] F6 Kalender kepatuhan

## Gelombang 2 — Kiosk tumbuh (Januari–April 2027)

- [ ] O8 Foto nota → pembelian
- [ ] K10 Penerimaan barang dan pesanan supplier
- [ ] O9 Pelacak harga supplier
- [ ] K9 Log waste
- [ ] O6 Kontrol kas dan audit
- [ ] O3 Laba rugi dan arus kas
- [ ] F1 Pembukuan otomatis
- [ ] F3 PPh final UMKM
- [ ] O10 Dana ruko dan milestone
- [ ] O7 Forecast dan rencana marinasi
- [ ] K7 Rencana prep harian
- [ ] O5 Menu engineering
- [ ] O11 Simulator harga dan promo
- [ ] O12 Tanya data
- [ ] P3 Struk digital
- [ ] M1 CRM dan segmentasi
- [ ] M2 Kampanye WhatsApp (link `wa.me`)
- [ ] M5 Promo dan voucher
- [ ] M3 Kalender konten
- [ ] M4 Desain promo (link Canva)
- [ ] M6 Event dan bazaar
- [ ] M7 Google Business Profile
- [ ] M9 Pemantau promo pesaing
- [ ] F2 Rekonsiliasi QRIS dan ojol (ojol mulai April 2027)
- [ ] F5 Berkas halal

## Gerbang Mei 2027

Kiosk **3 bulan berturut-turut di atas Rp1.000.000 per hari** (dihitung oleh O10).
Kalau belum lolos, Gelombang 3 ditunda. Jangan mulai Gelombang 3 tanpa konfirmasi tertulis pendiri di PR atau issue.

## Gelombang 3 — Persiapan ruko (Mei–September 2027)

Mulai dengan keputusan arsitektur publik: anon access lewat RPC, OTP, payment gateway.

- [ ] P12 Info publik + M10 Website dan SEO lokal
- [ ] P1 Halaman member mandiri (OTP)
- [ ] P4 Feedback lewat QR di struk
- [ ] P5 Pemulihan pelanggan kecewa
- [ ] P10 Referral
- [ ] K6 Layar dapur (KDS)
- [ ] P6 Layar antrean
- [ ] P7 Pre-order + payment gateway
- [ ] P8 Menu QR meja
- [ ] P9 Pesanan besar dan party box
- [ ] K12 Absensi dan jadwal
- [ ] K13 Penggajian
- [ ] K14 Pelatihan dan sertifikasi
- [ ] K16 Asisten SOP
- [ ] K15 Kinerja dan insentif
- [ ] K11 Suhu, kebersihan, perawatan
- [ ] O13 Penilaian lokasi ruko
- [ ] F4 PBJT
- [ ] F7 Brankas dokumen
- [ ] F8 Bagi hasil pendiri

## Ruko Gading Serpong buka — September 2027

## Gelombang 4 — Satu outlet sempurna (Oktober 2027 →)

- [ ] O14 Konsolidasi multi-outlet
- [ ] P11 Voting rasa edisi terbatas
- [ ] M8 Pelacak kreator
- [ ] Audit: outlet bisa dijalankan satu penanggung jawab; SOP dan data siap untuk cabang kedua

## Catatan keputusan

Tulis keputusan pendiri di sini dengan tanggal, supaya tidak ditanyakan ulang.

- 8 November 2026 — Stack: Next.js + Supabase + Vercel. Menu resmi mengikuti business plan, bukan POS lama.

# Mulai di sini (untuk Jonathan dan Novelie)

Paket ini adalah "otak" proyek untuk Claude Code. Claude Code membaca `CLAUDE.md` setiap kali sesi dimulai,
jadi kalian tidak perlu menjelaskan ulang bisnis CiKiPi setiap kali minta dibuatkan fitur.

## Isi paket

| File | Untuk apa |
| --- | --- |
| `CLAUDE.md` | Aturan utama yang selalu dibaca Claude Code |
| `docs/bisnis.md` | Menu, harga, HPP, BEP, KPI, pajak. **Ubah file ini kalau angka bisnis berubah** |
| `docs/arsitektur.md` | Cara semua aplikasi tersambung, offline, cetak struk, AI |
| `docs/skema-data.md` | Rancangan database |
| `docs/modul.md` | 60 modul dan syarat selesainya |
| `docs/roadmap.md` | Urutan pengerjaan dan status. Claude Code memperbaruinya sendiri |
| `.claude/rules/` | Aturan khusus yang hanya dibaca saat Claude mengerjakan bagian tertentu (database, kasir, tampilan, keuangan, AI) |

## Akun yang perlu disiapkan

1. **GitHub** — tempat menyimpan kode. Buat repository baru, misalnya `cikipi-sistem` (private).
2. **Supabase** — database. Buat satu proyek, region Singapura.
3. **Vercel** — tempat aplikasi online. Sambungkan ke repository GitHub tadi.
4. **Kunci Claude API** (dari Claude Console) — untuk fitur AI seperti baca foto nota dan ringkasan laporan.
   Baru dibutuhkan saat mengerjakan O2 dan O8.

Simpan semua kunci rahasia hanya di pengaturan Vercel dan Supabase, jangan ditempel di chat atau di file yang di-commit.

## Langkah pertama

1. Upload semua isi paket ini ke repository GitHub (termasuk folder `.claude`, yang namanya diawali titik).
2. Buka repository itu di Claude Code.
3. Tempel perintah ini:

```
Baca CLAUDE.md dan semua file di docs/. Lalu kerjakan "Langkah 0 — Setup proyek" di docs/roadmap.md.
Sebelum mulai, tulis rencanamu dalam poin singkat dan tanyakan hal yang belum jelas.
Setelah selesai, jelaskan ke saya dengan bahasa sederhana apa yang sudah jadi dan cara mencobanya.
```

## Perintah untuk modul berikutnya

```
Kerjakan modul berikutnya di gelombang aktif pada docs/roadmap.md.
Ikuti kriteria selesainya di docs/modul.md. Tulis rencana dulu, tunggu persetujuan saya kalau menyentuh database atau uang.
```

Atau sebut modulnya langsung:

```
Kerjakan K3 Log minyak. Batas awal ganti minyak 30 porsi. Saya mau peringatan muncul di layar dapur.
```

## Kebiasaan yang membantu

- **Satu modul, lalu coba di kiosk.** Pakai minimal dua minggu sebelum menambah modul baru di area yang sama.
- **Kalau angka bisnis berubah** (harga naik, sewa kios pasti, aturan poin), bilang ke Claude Code:
  "Perbarui docs/bisnis.md dan settings: harga Taiwanese Crispy Chicken jadi Rp40.000."
- **Kalau ada keputusan baru**, minta Claude Code mencatatnya di bagian "Catatan keputusan" di `docs/roadmap.md`.
- **Jangan setujui perubahan yang menghapus data** tanpa membaca penjelasannya. Claude Code diminta selalu menjelaskan dampaknya.

## Hal yang harus kalian putuskan sebelum Gelombang 1 selesai

- Aturan penukaran poin: potongan harga (seperti POS lama) atau hadiah barang (sesuai business plan)?
- Toleransi berat porsi saat QC (misalnya 150 g ± 10 g)?
- Siapa saja yang boleh membatalkan transaksi?
- Atas nama siapa usaha dicatat untuk pajak?

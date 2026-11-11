---
paths:
  - "src/app/**/*.tsx"
  - "src/components/**"
---

# Aturan tampilan

- Semua teks yang dilihat pengguna Bahasa Indonesia. Kalimat pendek, kata kerja jelas: "Simpan", "Cetak struk", "Tutup shift".
- Format rupiah selalu `Rp38.000` lewat satu helper `formatRupiah()`; tanggal dan jam lewat helper yang memakai `Asia/Jakarta`.
- Warna dari token di Tailwind config (burgundy, krem, plus warna status sukses, peringatan, bahaya). Jangan menulis hex langsung di komponen.
- Ukuran layar acuan: tablet 800 px (kasir dan dapur), HP 390 px (owner dan publik). Tidak ada scroll horizontal.
- Aplikasi kasir dan dapur: tombol besar, kontras tinggi, bisa dipakai dengan tangan bersarung plastik.
- Aplikasi owner: angka penting di atas (omzet hari ini vs BEP), detail di bawah. Grafik sederhana dengan label jelas.
- Halaman publik (Gelombang 3): boleh lebih ceria sesuai brand (maskot ayam, nada "Small bites, big smiles"),
  tetap cepat dimuat di HP.
- Setiap layar punya keadaan kosong, memuat, dan gagal yang ditulis dengan bahasa manusia, bukan kode error.
- Konfirmasi wajib sebelum aksi yang tidak bisa dibatalkan: void, tutup shift, konfirmasi stok opname, kirim kampanye.
- Aksesibilitas: label pada setiap input, fokus terlihat, ukuran teks minimal 16 px di HP.

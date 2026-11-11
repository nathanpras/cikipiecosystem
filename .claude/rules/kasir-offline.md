---
paths:
  - "src/app/(kasir)/**"
  - "src/lib/pos/**"
  - "src/lib/offline/**"
  - "src/lib/print/**"
---

# Aturan kasir, offline, dan cetak

- Layar kasir adalah layar paling penting. Satu transaksi biasa (1 ayam, 1 varian, tunai) harus selesai
  dalam paling banyak 5 ketukan setelah keranjang kosong.
- Target sentuh minimal 48 × 48 px; angka total dan kembalian besar dan jelas; tidak ada hover-only.
- Semua hitungan keranjang (subtotal, poin, promo, kembalian) ada di `src/lib/pos/` sebagai fungsi murni
  dengan unit test. Komponen hanya menampilkan.
- Server tetap menghitung ulang total di `record_sale` dari harga menu. Kalau berbeda dari klien (misalnya harga
  berubah saat kasir offline), transaksi tetap disimpan dengan harga dari klien, ditandai `needs_review`, dan owner
  diberi notifikasi. Transaksi yang sudah dibayar pelanggan tidak boleh hilang karena validasi.
- Offline:
  - setiap transaksi dibuat dengan `client_uuid` sebelum dikirim, disimpan dulu di IndexedDB, baru dikirim
  - antrean dikirim berurutan; respons "sudah ada" dianggap sukses
  - jangan pernah menghapus item antrean sebelum server mengonfirmasi
  - penukaran poin dan pendaftaran member baru dinonaktifkan saat offline, dengan pesan yang jelas
  - tampilkan status: Online / Offline — N menunggu / Menyinkronkan
- Uji offline di Playwright: matikan jaringan, buat 3 transaksi, nyalakan, pastikan tepat 3 order di database.
- Cetak:
  - `window.print()` dengan CSS `@page { size: 80mm auto; margin: 0 }`, lebar konten 72 mm
  - struk pelanggan: logo, nomor antrean besar, item + varian, total, metode bayar, poin member, link feedback
  - tiket dapur: nomor antrean, item, varian, catatan, tanpa harga
  - label batch: kode, QR, varian, berat, tanggal dibuat, kedaluwarsa
  - jangan memakai warna atau gambar abu-abu tipis; printer thermal hanya hitam putih
- Void butuh alasan dan PIN staf dengan peran yang diizinkan (settings), lalu mencetak ulang tiket dapur bertanda BATAL.

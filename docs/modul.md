# 60 modul CiKiPi

Kode: O = owner, P = pelanggan, K = karyawan dan dapur, M = pemasaran, F = keuangan dan legal.
Kolom G = gelombang (lihat `docs/roadmap.md`). Kriteria selesai adalah syarat minimum; tambahan boleh
asal tidak melanggar CLAUDE.md. Semua angka awal ada di `docs/bisnis.md` dan settings.

## Owner (14)

| Kode | G | Modul | Kriteria selesai |
| --- | --- | --- | --- |
| O1 | 1 | Dashboard live | Omzet hari ini vs BEP harian outlet dan target Rp1 juta; jumlah transaksi; rata-rata belanja; 5 menu teratas; omzet per jam; % transaksi member. Update realtime. Hari = zona Asia/Jakarta. |
| O2 | 1 | Laporan otomatis | Laporan harian 21.30 WIB, mingguan Senin 08.00, bulanan tanggal 1 tersimpan di `reports` (metrik JSON + ringkasan Claude). Notifikasi ke owner. Idempoten per periode. Pemicu tiap jam membuat notifikasi tanpa duplikat. |
| O3 | 2 | Laba rugi dan arus kas | Per bulan per outlet: omzet, biaya variabel (stok terpakai × biaya), biaya tetap (expenses), laba sebelum dan sesudah gaji pendiri, saldo kas; dibandingkan proyeksi. Ekspor Excel. |
| O4 | 1 | HPP hidup | Biaya per porsi = Σ resep × `last_unit_cost` + kemasan; % terhadap harga; merah kalau lewat batas settings. Uji: angka cocok dengan tabel menu di `docs/bisnis.md` dengan data seed. |
| O5 | 2 | Menu engineering | Matriks popularitas × margin kontribusi per menu per bulan, label: andalan, laku tapi tipis, untung tapi jarang, evaluasi. |
| O6 | 2 | Kontrol kas dan audit | Selisih kas per shift; void dan diskon per staf; transaksi di luar jam buka; notifikasi kalau selisih > Rp5.000. |
| O7 | 2 | Forecast dan rencana marinasi | Perkiraan porsi besok per jam dari rata-rata hari yang sama 4 minggu terakhir, ditambah event. Kg ayam = porsi × 150 g × faktor susut. Akurasi minggu lalu ditampilkan. |
| O8 | 2 | Catat pengeluaran dari foto nota | Foto (kamera tablet/HP) → Claude ekstrak → draf `purchases` → owner cocokkan bahan dan konfirmasi → stok masuk dan `last_unit_cost` terbarui. |
| O9 | 2 | Pelacak harga supplier | Riwayat harga per bahan per supplier, grafik, supplier termurah 30 hari terakhir, peringatan kenaikan > 10%. |
| O10 | 2 | Dana ruko dan milestone | Laba kumulatif ditahan vs target; jumlah hari ≥ Rp1 juta per bulan; status syarat "3 bulan berturut-turut"; checklist milestone business plan. |
| O11 | 2 | Simulator harga dan promo | Ubah harga, biaya, volume, atau promo secara hipotetis → laba dan BEP baru. Tidak mengubah data asli. |
| O12 | 2 | Tanya data | Owner bertanya bahasa biasa; Claude memakai tool baca yang sudah ditentukan (omzet per periode, menu terlaris, per jam, member, stok); jawaban menyebut angka dan periodenya. |
| O13 | 3 | Penilaian lokasi ruko | Form skor kandidat (sewa, lalu lintas, kampus, pesaing, parkir) dengan bobot; peringkat. |
| O14 | 4 | Konsolidasi multi-outlet | Semua laporan bisa difilter dan dibandingkan per outlet; total gabungan. |

## Pelanggan (12)

| Kode | G | Modul | Kriteria selesai |
| --- | --- | --- | --- |
| P1 | 3 | Halaman member mandiri | Login OTP; poin, riwayat, voucher; ubah tanggal lahir; tarik persetujuan dan berhenti pesan WA. |
| P2 | 1 | Loyalitas | Poin earn dan redeem sesuai settings, lewat `point_ledger`; tier opsional; katalog hadiah (kalau pendiri memilih hadiah barang). |
| P3 | 2 | Struk digital | Link struk unik (`public_token`) bisa dikirim lewat `wa.me`; halaman struk publik read-only. |
| P4 | 1 / 3 | Feedback rasa | G1: layar tablet setelah bayar, nilai 1–5 + asin, tepung, kematangan, renyah; tersimpan dengan order dan batch. G3: QR di struk ke halaman feedback. |
| P5 | 3 | Pemulihan pelanggan kecewa | Nilai ≤ 2 → notifikasi owner + draf pesan maaf + voucher porsi pengganti. |
| P6 | 3 | Layar antrean | Halaman untuk TV menampilkan nomor "sedang disiapkan" dan "siap" dari `kitchen_status`. |
| P7 | 3 | Pre-order | Pilih menu dan slot ambil (kapasitas dari `ops.fryer_portions_per_hour`); bayar lewat payment gateway; masuk antrean dapur pada jam yang tepat. |
| P8 | 3 | Menu QR meja | QR per meja ruko; pesanan masuk layar dapur; bayar di kasir atau online. |
| P9 | 3 | Pesanan besar dan party box | Form acara → penawaran otomatis (harga dari HPP + margin setting) → PDF; owner menyetujui sebelum dikirim. |
| P10 | 3 | Referral | Kode per member; bonus poin untuk keduanya setelah transaksi pertama teman. |
| P11 | 4 | Voting rasa edisi terbatas | Polling per periode, satu suara per member. |
| P12 | 3 | Info publik | Menu, harga, lokasi, jam, status halal, alergen; SEO lokal; schema.org `Restaurant`. |

## Karyawan dan dapur (16)

| Kode | G | Modul | Kriteria selesai |
| --- | --- | --- | --- |
| K1 | 1 | PIN dan hak akses | Akun perangkat + PIN staf (hash); sesi berakhir otomatis; peran menentukan layar; tes RLS lulus. |
| K2 | 1 | Checklist buka-tutup | Template dari SOP; buka shift butuh checklist buka selesai; tutup shift butuh checklist tutup; foto opsional per item. |
| K3 | 1 | Log minyak | Porsi sejak ganti dihitung otomatis dari penjualan ayam; peringatan di batas setting; input suhu, warna, TPM, liter; grafik porsi per ganti minyak. |
| K4 | 1 | Batch marinasi + label QR | Buat batch dengan snapshot takaran; cetak label 80 mm; FIFO dan peringatan kedaluwarsa; batch aktif per varian otomatis ditempel ke `order_items`. |
| K5 | 1 | QC sampel | Pengingat timbang setiap N porsi; input berat dan cicip; % lolos 150 g ± toleransi per hari dan per batch. |
| K6 | 3 | Layar dapur (KDS) | Pesanan realtime; status queued → frying → ready; timer per pesanan; rata-rata waktu tunggu per jam. |
| K7 | 2 | Rencana prep harian | Dari O7: jumlah porsi ditepungi sebelum 11.30, 15.00, 17.00; tampil di layar dapur pagi hari. |
| K8 | 1 | Resep dan stok otomatis | Resep gram per menu dan varian; penjualan membuat `stock_movements`; stok opname tutup toko; laporan selisih. |
| K9 | 2 | Log waste | Porsi gagal dan bahan dibuang + alasan → `stock_movements` kind `waste`; biaya waste per minggu. |
| K10 | 2 | Penerimaan barang dan pesanan supplier | Terima barang (berat aktual vs nota); daftar belanja otomatis dari `min_stock`; tombol pesan WA ke supplier dengan teks siap kirim. |
| K11 | 3 | Suhu, kebersihan, perawatan | Log suhu chiller dan freezer 2× sehari; jadwal perawatan fryer dan exhaust; peringatan kalau terlewat. |
| K12 | 3 | Absensi dan jadwal | Masuk/keluar dengan PIN; jadwal mingguan per stasiun; rekap jam kerja. |
| K13 | 3 | Penggajian | Gaji pokok, uang makan × hari masuk, THR disisihkan 1/12, BPJS (setting %); slip PDF. |
| K14 | 3 | Pelatihan dan sertifikasi | Modul SOP + kuis; lulus per stasiun; status lulus terlihat di jadwal. |
| K15 | 3 | Kinerja dan insentif | Per staf: transaksi, waktu layanan, selisih kas, void, keluhan; aturan bonus di settings. |
| K16 | 3 | Asisten SOP | Claude menjawab hanya dari dokumen SOP (`documents.kind = 'sop'`); menyebut SOP sumber; "belum ada di SOP" kalau tidak ditemukan. |

## Pemasaran (10)

| Kode | G | Modul | Kriteria selesai |
| --- | --- | --- | --- |
| M1 | 2 | CRM dan segmentasi | Segmen baru, setia, mulai jarang, hilang dari kapan terakhir, seberapa sering, berapa belanja; ambang di settings. |
| M2 | 2 | Kampanye WhatsApp | Draf harian ulang tahun dan member hilang; owner menyetujui; kirim per orang lewat `wa.me`; status tercatat; `wa_opt_out` dihormati. |
| M3 | 2 | Kalender konten | 3–4 unggahan per minggu, 5 pilar; Claude membuat draf caption dari data minggu lalu; status ide → terbit. |
| M4 | 2 | Desain promo | Simpan link desain (Canva) per konten dan promo. Desainnya dibuat di luar aplikasi. |
| M5 | 2 | Promo dan voucher | Promo berbentuk bonus; penukaran tercatat dengan biayanya; laporan dampak: transaksi, rata-rata belanja, biaya bonus. |
| M6 | 2 | Event dan bazaar | Kalender event; event = outlet kind `bazaar`; stok yang dibawa; laba rugi per event. |
| M7 | 2 | Google Business Profile | Simpan ulasan; Claude membuat draf balasan; daftar pelanggan bernilai 5 di P4 untuk diminta ulasan. |
| M8 | 4 | Pelacak kreator | Kreator, kode promo unik, penjualan per kode. |
| M9 | 2 | Pemantau promo pesaing | Catat promo pesaing; ringkasan mingguan; peringatan kalau promo dekat outlet. |
| M10 | 3 | Website dan SEO lokal | Halaman publik P12 + pre-order; metadata, sitemap, schema; halaman per outlet. |

## Keuangan dan legal (8)

| Kode | G | Modul | Kriteria selesai |
| --- | --- | --- | --- |
| F1 | 2 | Pembukuan otomatis | Laporan format SAK EMKM (laba rugi, posisi keuangan, arus kas) dari transaksi dan pengeluaran; ekspor Excel dan PDF. |
| F2 | 2 | Rekonsiliasi pembayaran | Impor CSV QRIS dan ojol; cocokkan per hari dengan POS; selisih ditandai. |
| F3 | 2 | PPh final UMKM | Omzet bulanan dan kumulatif tahun; 0,5% atas bagian di atas ambang tahunan; pengingat sebelum tanggal 15. Tarif dan ambang dari settings. Uji dengan contoh di bawah dan di atas Rp500 juta. |
| F4 | 3 | PBJT | Nonaktif default; kalau aktif: tarif setting dihitung di struk dan laporan; ekspor data untuk pemda. |
| F5 | 2 | Berkas halal | Daftar bahan + merek + supplier + nomor dan masa berlaku sertifikat halal; ekspor untuk pendaftaran Sehati. |
| F6 | 1 | Kalender kepatuhan | Dokumen dengan tanggal berlaku; notifikasi N hari sebelum jatuh tempo. |
| F7 | 3 | Brankas dokumen | Upload ke Storage privat; hanya owner; pratinjau PDF dan gambar. |
| F8 | 3 | Bagi hasil pendiri | Laba, gaji pendiri (aktif otomatis setelah syarat 3 bulan), prive, saldo modal 50:50 per pendiri. |

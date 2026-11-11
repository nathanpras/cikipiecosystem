# Fakta bisnis CiKiPi

Sumber: Business Plan CiKiPi (7 Oktober 2026) dan Blueprint Sistem Digital CiKiPi (8 November 2026).
Angka di sini adalah nilai awal untuk `settings` dan `seed.sql`. Kalau pendiri mengubah angka,
perbarui file ini dan settings, bukan kode.

## Identitas

- Nama: CiKiPi (plesetan "chicken crispy"). Tagline: *Small bites, big smiles.*
- Warna brand: burgundy dan krem, maskot ayam. Kepribadian: ceria, murah hati, simpel, rapi, ikut tren.
- Teks di aplikasi pelanggan boleh ceria; teks di aplikasi kasir, dapur, dan owner singkat dan jelas.
- Status legal: belum berbadan usaha. NIB, SPP-IRT, halal, dan merek sedang/akan diurus.

## Orang

| Nama | Peran di sistem | Tanggung jawab |
| --- | --- | --- |
| Jonathan Prasetyo | owner | Operasional, produksi, resep, kualitas, belanja, kepala outlet |
| Novelie Baby Paramitha | owner | Pemasaran, keuangan, member, harga dan margin |
| Kakak Novelie | kasir (sukarela) | Membantu saat ramai |
| Kru ruko (2–3 orang, mulai 2027) | kasir / dapur | Goreng, kasir, packing |

Keputusan strategis (harga, lokasi, investasi, menu baru) diambil berdua.

## Outlet

| Outlet | Jenis | Mulai | Hari buka | Jam | Catatan |
| --- | --- | --- | --- | --- | --- |
| Bazaar BINUS | bazaar | 2026 | per event | per event | Lapak gratis, kru tidak dibayar |
| Kiosk Green Lake City | kiosk | November 2026 | 26 hari/bulan | 11.00–21.00 | Dijalankan pendiri berdua |
| Ruko Gading Serpong | ruko | target September 2027 | 30 hari/bulan | belum ditetapkan | Lantai 1, berbagi ruko dengan usaha florist |

Jam ramai: **11.30, 15.00, 17.00**.

## Menu dan harga (berlaku mulai tahap kiosk)

| Menu | Isi | Harga | Biaya bahan + kemasan | Biaya / harga |
| --- | --- | --- | --- | --- |
| Taiwanese Crispy Chicken | 150 g fillet dada bertepung; varian Salt n Pepper, BBQ, Mala | Rp38.000 | ±Rp16.300 | 43% |
| Garlic Taiwan | Ayam crispy marinasi bawang putih | Rp38.000 | ±Rp14.300 | 38% |
| Cheesy Hashbrown | 5 pcs, tusuk yakitori | Rp28.000 | ±Rp12.100 | 43% |
| Single Package | 1 ayam + 3 pcs hashbrown | Rp48.000 | ±Rp22.100 | 46% |
| Combo Package | 2 ayam + 5 pcs hashbrown | Rp85.000 | ±Rp39.800 | 47% |

- Biaya sudah termasuk bahan, minyak, gas, dan kemasan **Rp2.500 per pesanan** (food pail + kertas food grade + tusuk).
- Paket = `is_package = true` dengan `package_components`. Varian ayam dipilih per ayam di dalam paket.
- Menu lama yang sudah dihentikan: Popcorn Chicken (boros minyak), Chicken Cordon Blue. Jangan dimasukkan ke seed.
- Roadmap menu ruko: ayam goreng bawang putih + nasi daun jeruk + sambal bawang, lalu rice bowl dan geprek.
  Harga belum ditetapkan.
- Usulan kiosk (belum diputuskan): minuman dingin sederhana (es teh, lemon tea).

## Komposisi penjualan (158 pesanan, 3 hari bazaar)

| Menu | Pesanan | Porsi |
| --- | --- | --- |
| Taiwanese Crispy Chicken | 71 | 45% |
| Single Package | 59 | 37% |
| Combo Package | 13 | 8% |
| Garlic Taiwan | 9 | 6% |
| Cheesy Hashbrown | 6 | 4% |

Aktual bazaar harga lama: omzet Rp6.045.000, bahan Rp2.771.000, kemasan Rp395.000, laba Rp2.879.000 (47,6%).

## Angka kunci keuangan

| Angka | Nilai | Dipakai di |
| --- | --- | --- |
| Rata-rata belanja per transaksi | Rp45.221 | Forecast, BEP |
| Biaya variabel (bahan + kemasan) | 44,5% dari omzet = Rp20.126/transaksi | HPP, laba rugi |
| Margin kontribusi | Rp25.095/transaksi (55,5%) | BEP |
| Biaya tetap kiosk | Rp5.833.000/bulan | BEP kiosk |
| Biaya tetap ruko, 2 karyawan | Rp17.117.000/bulan | BEP ruko |
| Biaya tetap ruko, 3 karyawan | Rp20.400.000/bulan | BEP ruko |
| Gaji pendiri (berdua) | Rp10.000.000/bulan | Mulai setelah laba sebelum gaji > Rp10 juta 3 bulan berturut-turut |
| Biaya variabel efektif ruko | 47,9% (20% penjualan lewat ojol + PPh) | BEP ruko |

Rincian biaya tetap kiosk per bulan: sewa Rp3.000.000, listrik dan air Rp800.000, internet dan pulsa Rp300.000,
pemasaran Rp1.000.000, kebersihan dan lain-lain Rp400.000, penyusutan Rp333.000, tenaga kerja Rp0.

Rumus BEP:

```
BEP transaksi per bulan = biaya tetap per bulan / (harga rata-rata - biaya variabel rata-rata)
BEP omzet per hari      = BEP transaksi per bulan × harga rata-rata / hari buka per bulan
```

Contoh uji (wajib cocok di unit test): kiosk = 5.833.000 / 25.095 = 232,4 transaksi/bulan
→ 8,9 ≈ **9 transaksi/hari** → **Rp404.000/hari** (26 hari buka).

| Unit | BEP transaksi/hari | BEP omzet/hari |
| --- | --- | --- |
| Kiosk | 9 | Rp404.000 |
| Kiosk + gaji pendiri | 24 | Rp1.097.000 |
| Ruko, 2 karyawan | 23 | Rp1.095.000 |
| Ruko, 2 karyawan + gaji pendiri | 37 | Rp1.735.000 |

## Batas biaya bahan (HPP)

- Menu satuan: maksimal **43%** dari harga jual (target jangka menengah 38–40%).
- Paket: maksimal **47%**.
- Gabungan: maksimal **44,5%**.
- Lewat batas → tandai merah di HPP (O4) dan beri tahu owner.

## Target dan KPI

| KPI | Target |
| --- | --- |
| Omzet kiosk per hari | Minimal Rp1.000.000 mulai Maret 2027 |
| Syarat buka ruko | 3 bulan berturut-turut di atas Rp1.000.000/hari (dicek Mei 2027) |
| Omzet ruko per bulan | Rp60–100 juta |
| Transaksi tercatat sebagai member | Minimal 60% |
| Transaksi dari member yang kembali | Minimal 30% setelah 6 bulan kiosk |
| Followers IG + TikTok | Minimal 2.000 dalam 12 bulan |
| Event dan bazaar | Lebih dari 10 per tahun |
| Waktu tunggu jam ramai | Maksimal 10 menit |
| Selisih kas harian | Di bawah Rp5.000 |
| Laba kiosk ditahan untuk ruko | Rp93.700.000 sampai Agustus 2027 |

## Operasional dapur

- Ayam per hari: 7 kg saat sepi, 10 kg saat ramai. Marinasi di dapur rumah 1–2 hari sebelum jualan.
- Alur simpan: chest freezer → chiller sehari sebelum jualan → tepung → goreng dadakan.
- Deep fryer 2 keranjang, 2 porsi per keranjang, 4 menit → kapasitas sekitar **60 porsi ayam per jam**.
- Hashbrown: 9 pcs per 6,5 menit. Target kiosk: fryer kecil terpisah untuk hashbrown.
- Minyak: 4 liter + tambah 2 liter di tengah jualan; menghitam setelah sekitar **30 porsi**.
  Batas awal ganti minyak = 30 porsi (setting, akan disesuaikan dari data log minyak).
  Acuan luar: senyawa polar total maksimal 25% (batas Uni Eropa).
- QC: cicip satu porsi di awal shift; timbang **1 dari setiap 10 porsi**, target **150 g**.
  Tepung kasar target 30 g per porsi.
- Porsi terlalu gelap, kurang matang, atau bertepung tebal tidak dijual (catat sebagai waste).
- Minyak bekas dijual ke pengepul jelantah.

## Member dan poin

- Member = nomor HP + nama. Tanggal lahir opsional (untuk bonus ulang tahun).
- Aturan poin di POS lama (perlu dikonfirmasi pendiri sebelum dipakai):
  setiap Rp10.000 = 1 poin; 10 poin = potongan Rp10.000.
- Kebijakan business plan: promo berbentuk **bonus** (misalnya hashbrown tambahan), bukan potongan harga menu utama.
  Tanyakan ke pendiri apakah penukaran poin diubah menjadi hadiah barang.
- Member "mulai jarang" dan "hilang": nilai awal 14 dan 21 hari tidak bertransaksi (setting).

## Pajak dan legal (dicek 8 November 2026)

Bahan perencanaan, bukan nasihat pajak. Semua tarif dan ambang disimpan di settings.

- **PPh final UMKM 0,5%** (PP 55/2022 diubah PP 20/2026): orang pribadi beromzet sampai Rp4,8 miliar/tahun.
  Omzet sampai Rp500 juta/tahun tidak kena pajak; 0,5% dikenakan atas bagian di atasnya. Setor sebelum tanggal 15 bulan berikutnya.
- **PBJT makanan dan minuman** 10% di Kota Tangerang dan Kabupaten Tangerang.
  Batas omzet wajib pungut belum diketahui; default nonaktif sampai pendiri mengonfirmasi ke Bapenda.
  Perda Kabupaten Tangerang 1/2026 mewajibkan perangkat perekam transaksi untuk objek pajak makan-minum.
- **Wajib halal UMK** diundur ke 17 Oktober 2028. Rencana: daftar lewat Sehati (gratis, pernyataan mandiri) sebelum ruko.
- **UU PDP 27/2022**: persetujuan eksplisit, tertulis atau terekam, bisa dibuktikan; berhenti memproses
  paling lambat 3 × 24 jam setelah persetujuan ditarik.
- **MDR QRIS** 0% untuk transaksi sampai Rp100.000 (semua merchant) dan sampai Rp500.000 (usaha mikro), sejak 1 Oktober 2026.
- **Upah kru ruko** Rp2.500.000/bulan berdasarkan kesepakatan tertulis; THR 1× gaji/tahun disisihkan bulanan;
  makan Rp15.000/hari; BPJS bagian pemberi kerja sekitar 5% gaji (perkiraan).

## Yang masih perlu dipastikan pendiri

- Aturan penukaran poin (potongan atau hadiah barang) dan tingkatan member
- Harga sewa kios sebenarnya di Green Lake City
- Batas porsi atau suhu ganti minyak dari uji coba SOP
- Harga dan resep menu nasi ruko
- Apakah kiosk tetap jalan setelah ruko buka
- Status PBJT outlet (wajib pungut atau belum)
- Atas nama siapa usaha dicatat untuk pajak (satu orang pribadi atau badan usaha)

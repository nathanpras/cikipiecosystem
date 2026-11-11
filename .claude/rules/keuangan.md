---
paths:
  - "src/lib/finance/**"
  - "src/app/(owner)/**"
  - "supabase/functions/**"
---

# Aturan hitungan keuangan

- Semua rumus ada di `src/lib/finance/` sebagai fungsi murni dengan unit test. Layar dan Edge Function hanya memanggilnya.
- Uang dihitung dalam integer rupiah. Pembulatan hanya di tampilan atau di hasil akhir pajak (bulatkan ke rupiah terdekat).
- Angka dari settings dan tabel master, tidak di-hardcode: tarif pajak, ambang, batas HPP, target, biaya tetap, hari buka.
- Rumus yang wajib cocok dengan `docs/bisnis.md` (buat unit test-nya):
  - BEP transaksi/bulan = biaya tetap / (rata-rata belanja − biaya variabel per transaksi)
    → kiosk: 5.833.000 / 25.095 ≈ 232,4 → 9 transaksi/hari, Rp404.000/hari (26 hari)
  - HPP per menu = Σ (qty resep × `last_unit_cost`) + `pos.packaging_cost` (Rp2.500), sama seperti tabel menu di `docs/bisnis.md`
  - % biaya bahan = HPP / harga jual × 100; bandingkan dengan batas satuan 43%, paket 47%, gabungan 44,5%
  - PPh final UMKM = tarif × max(0, omzet kumulatif tahun ini − ambang) − PPh yang sudah dihitung bulan sebelumnya di tahun yang sama
- Omzet = order `status = 'paid'`, tidak termasuk `void`. Pajak daerah (PBJT) yang dipungut bukan omzet.
- "Hari", "minggu", "bulan" selalu dihitung di zona Asia/Jakarta, bukan UTC.
- Setiap laporan menyebut periodenya dan kapan dibuat. Angka yang berasal dari asumsi (proyeksi, settings) diberi label "asumsi".
- Fitur pajak menampilkan catatan: "Perhitungan untuk perencanaan. Pastikan ke konsultan pajak atau Bapenda."

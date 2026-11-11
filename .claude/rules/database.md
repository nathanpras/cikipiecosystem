---
paths:
  - "supabase/**"
  - "src/lib/db/**"
---

# Aturan database

- Satu migrasi = satu perubahan yang masuk akal (misalnya "tambah tabel oil_logs + RLS + index").
  Nama file dari `npx supabase migration new <nama_snake_case>`.
- Setiap `create table` di migrasi yang sama wajib diikuti: `alter table ... enable row level security`,
  policy per peran, index foreign key, dan trigger `updated_at` kalau tabel punya kolom itu.
- Policy memakai helper `app_role()` dan `app_outlet()`; jangan menulis ulang logika peran di setiap policy.
- Tabel keuangan (`purchases`, `expenses`, `settlements`, `tax_periods`, `payroll_runs`, `reports`, `founder_ledger`,
  `documents`, `site_scores`) hanya untuk `owner`. Tambahkan tes RLS di `supabase/tests/` untuk setiap tabel baru.
- Tulisan dari perangkat outlet lewat RPC `security definer` yang memvalidasi `session_id` dan mengambil `staff_id`
  dari `device_sessions`. Set `search_path` eksplisit di setiap fungsi `security definer`.
- RPC yang menulis lebih dari satu tabel berjalan dalam satu transaksi dan idempoten bila ada `client_uuid`.
- Uang `bigint`, tidak pernah `numeric` dengan desimal atau `float`. Persen dan berat boleh `numeric`.
- Jangan `delete` baris transaksi, stok, poin, atau keuangan. Pembalikan = baris ledger baru dengan `reason = 'reverse'`.
- Migrasi yang sudah di-push tidak diedit. Perbaikan = migrasi baru.
- Setelah migrasi: `npx supabase db reset`, lalu `npm run gen:types`, lalu perbarui `supabase/seed.sql` bila perlu.
- Seed hanya memakai data palsu. Nomor HP contoh memakai pola `0800000000xx`.
- Perubahan skema yang menghapus atau mengganti nama kolom harus disetujui pendiri dulu dan dijelaskan dampaknya.

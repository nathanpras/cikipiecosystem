# Skema data CiKiPi

Rancangan awal. Claude Code boleh menyesuaikan detail kolom, tetapi aturan di bagian Konvensi dan RLS tidak boleh dilanggar.
Tabel dibuat sesuai gelombang modul yang memakainya.

## Konvensi

- `id uuid primary key default gen_random_uuid()`, `created_at timestamptz not null default now()`,
  `updated_at` diisi trigger.
- Uang: `bigint` rupiah. Berat: `numeric(10,2)` gram. Persen: `numeric(5,2)`. Biaya per satuan bahan: `numeric(14,4)` rupiah.
- Pilihan tetap: `text` + `check (... in (...))`, bukan enum Postgres (lebih mudah diubah).
- Semua tabel operasional punya `outlet_id`.
- Tidak ada hard delete untuk data transaksi, stok, poin, dan keuangan. Master data memakai `is_active`.
- Index untuk setiap foreign key dan untuk `(outlet_id, created_at)` di tabel transaksi dan log.

## Gelombang 1 — inti

**outlets**: `name`, `kind` (`bazaar`/`kiosk`/`ruko`), `address`, `open_time`, `close_time`, `days_open_per_month`,
`monthly_fixed_cost` (bigint), `is_active`

**staff**: `outlet_id` (null = semua outlet), `name`, `role` (`owner`/`kasir`/`dapur`), `pin_hash`,
`auth_user_id` (null kecuali owner), `is_active`

**devices**: `outlet_id`, `name`, `code` (2 huruf, untuk nomor antrean offline), `auth_user_id`

**device_sessions**: `device_id`, `staff_id`, `unlocked_at`, `expires_at`, `ended_at`

**menu_items**: `name`, `category` (`ayam`/`side`/`paket`/`minuman`/`nasi`), `price`, `is_package`, `sort_order`, `is_active`

**menu_variants**: `menu_item_id`, `name`, `is_active`

**package_components**: `package_id` → menu_items, `component_item_id` → menu_items, `qty`, `variant_choice` (bool: pelanggan memilih varian)

**ingredients**: `name`, `unit` (`g`/`ml`/`pcs`), `min_stock`, `last_unit_cost`, `is_packaging`, `is_active`

**recipe_lines**: `menu_item_id`, `variant_id` (null = semua varian), `ingredient_id`, `qty_per_portion`

**settings**: `key text primary key`, `value jsonb`, `description`, `updated_by`, `updated_at`

**shifts**: `outlet_id`, `device_id`, `staff_id`, `opened_at`, `closed_at`, `opening_cash`, `expected_cash`,
`counted_cash`, `variance` (generated: counted − expected)

**members**: `name`, `phone` (unique, format `08…`), `birth_date`, `consent_at`, `consent_version`,
`wa_opt_out`, `tier`, `referred_by_member_id`, `referral_code` (unique)

**point_ledger**: `member_id`, `order_id`, `delta` (int, bisa negatif), `reason` (`earn`/`redeem`/`bonus`/`adjust`/`reverse`),
`created_by_staff_id`. View `member_points` = sum(delta).

**orders**: `outlet_id`, `device_id`, `client_uuid` (unique), `queue_no` (text), `channel` (`kasir`/`bazaar`/`ojol`/`preorder`/`qr_meja`),
`status` (`paid`/`void`), `subtotal`, `discount`, `points_discount`, `tax_amount`, `total`, `member_id`, `staff_id`, `shift_id`,
`note`, `public_token` (unique, untuk struk digital dan feedback QR), `device_created_at`, `needs_review` (bool, harga klien
berbeda dari harga server), `void_reason`, `voided_by`, `voided_at`

**order_items**: `order_id`, `menu_item_id`, `variant_id`, `parent_item_id` (komponen paket), `qty`, `unit_price`, `line_total`,
`note`, `batch_id`, `kitchen_status` (`queued`/`frying`/`ready`/`served`, dipakai Gelombang 3), `started_at`, `ready_at`

**payments**: `order_id`, `method` (`tunai`/`qris`/`transfer`/`ojol`/`gateway`), `amount`, `cash_received`, `change_given`, `reference`

**stock_movements**: `outlet_id`, `ingredient_id`, `qty` (bertanda), `kind` (`purchase`/`sale`/`waste`/`count_adjust`/`transfer`/`prep`/`reverse`),
`unit_cost`, `ref_table`, `ref_id`, `reason`, `staff_id`. View `stock_levels` = sum(qty) per outlet dan bahan.

**stock_counts**: `outlet_id`, `counted_at`, `staff_id`, `lines jsonb` (ingredient_id, counted_qty, system_qty) — menghasilkan `count_adjust`

**marinade_batches**: `outlet_id`, `code` (contoh `M-261108-01`), `menu_item_id`, `variant_id`, `weight_g`,
`recipe_snapshot jsonb`, `made_by`, `made_at`, `expires_at`, `status` (`freezer`/`chiller`/`in_use`/`finished`/`discarded`)

**oil_logs**: `outlet_id`, `fryer` (`ayam`/`hashbrown`), `event` (`change`/`top_up`/`check`), `temp_c`, `color_score` (1–5),
`tpm_percent`, `liters`, `staff_id`. Porsi sejak ganti = jumlah porsi ayam terjual sejak `change` terakhir (dihitung, tidak disimpan).

**qc_checks**: `outlet_id`, `order_item_id`, `batch_id`, `weight_g`, `taste_ok`, `passed`, `reason`, `staff_id`

**feedback**: `order_id`, `batch_id`, `overall` (1–5), `salty`, `breading`, `doneness`, `crispy` (masing-masing −1 kurang / 0 pas / 1 terlalu),
`comment`, `channel` (`ipad`/`qr`)

**checklist_templates**: `name`, `kind` (`buka`/`tutup`/`kebersihan`), `items jsonb` (`[{id, text, needs_photo}]`), `is_active`

**checklist_runs**: `template_id`, `outlet_id`, `staff_id`, `answers jsonb`, `photo_paths text[]`, `completed_at`

**documents**: `kind` (`izin`/`halal`/`kontrak`/`sertifikat`/`sop`/`lain`), `title`, `file_path`, `valid_until`, `reminder_days`

**reports**: `outlet_id` (null = semua), `kind` (`harian`/`mingguan`/`bulanan`), `period_start`, `period_end`, `metrics jsonb`,
`body_md`, unique (`outlet_id`, `kind`, `period_start`)

**notifications**: `audience` (`owner`), `kind`, `title`, `body`, `link`, `read_at`, `dedupe_key` (unique)

**audit_log**: `actor_staff_id`, `actor_auth_id`, `action`, `table_name`, `row_id`, `before jsonb`, `after jsonb`

**ai_usage**: `feature`, `model`, `input_tokens`, `output_tokens`, `created_at`

## Gelombang 2

**suppliers**: `name`, `phone`, `category`, `halal_cert_no`, `halal_cert_expires`, `notes`, `is_active`

**purchases**: `outlet_id`, `supplier_id`, `purchased_at`, `total`, `receipt_photo_path`, `ai_extracted jsonb`,
`status` (`draft`/`confirmed`), `confirmed_by`, `confirmed_at`

**purchase_items**: `purchase_id`, `ingredient_id` (null sampai dicocokkan), `raw_name`, `qty`, `unit`, `line_total`

**expenses**: `outlet_id`, `category` (`sewa`/`listrik_air`/`internet`/`pemasaran`/`gaji`/`perawatan`/`legal`/`lain`), `amount`,
`spent_at`, `note`, `receipt_photo_path`, `purchase_id`

**settlements**: `source` (`qris`/`gofood`/`grabfood`/`shopeefood`), `period_date`, `gross`, `fee`, `net`, `file_path`,
`matched`, `difference`

**promos**: `code`, `name`, `kind` (`bonus_item`/`points_multiplier`/`free_upgrade`), `rules jsonb`, `starts_at`, `ends_at`, `is_active`

**promo_redemptions**: `promo_id`, `order_id`, `member_id`, `cost` (biaya bonus dalam rupiah)

**campaigns**: `name`, `kind` (`ulang_tahun`/`winback`/`menu_baru`/`event`/`lain`), `segment jsonb`, `message_template`,
`channel` (`wa_link`/`wa_api`), `status` (`draft`/`approved`/`sent`), `approved_by`

**campaign_recipients**: `campaign_id`, `member_id`, `rendered_message`, `status` (`pending`/`opened`/`sent`/`skipped`), `sent_at`

**content_posts**: `planned_date`, `pillar` (`daging_tebal`/`cerita_pendiri`/`suara_pelanggan`/`info_jualan`/`tren`),
`platform`, `caption`, `design_url`, `status` (`ide`/`draf`/`siap`/`terbit`)

**events**: `name`, `outlet_id` (outlet kind bazaar), `location`, `starts_on`, `ends_on`, `fee`, `notes`

**competitor_promos**: `competitor`, `description`, `price`, `source_url`, `valid_until`, `seen_at`

**reviews**: `platform` (`google`), `author_label`, `rating`, `text`, `reply_draft`, `replied_at`

**tax_periods**: `period` (`YYYY-MM`), `taxpayer_label`, `gross_revenue`, `ytd_revenue`, `pph_due`, `pbjt_due`, `paid_at`, `proof_path`

## Gelombang 3

**attendance**: `staff_id`, `outlet_id`, `clock_in`, `clock_out`, `device_id`

**shift_schedules**: `staff_id`, `outlet_id`, `date`, `start_time`, `end_time`, `station` (`kasir`/`goreng`/`packing`)

**payroll_runs**: `period`, `staff_id`, `base_salary`, `meal_allowance`, `thr_accrual`, `bpjs_employer`, `deductions`,
`bonus`, `total`, `slip_path`, `paid_at`

**training_modules**: `title`, `station`, `content_md`, `video_url`, `quiz jsonb`, `pass_score`

**training_results**: `module_id`, `staff_id`, `score`, `passed`, `taken_at`

**temperature_logs**: `outlet_id`, `equipment` (`chiller`/`freezer`), `temp_c`, `staff_id`

**maintenance_logs**: `outlet_id`, `equipment`, `task`, `done_at`, `next_due`, `staff_id`

**pickup_slots**: `outlet_id`, `date`, `start_time`, `capacity_portions`, `booked_portions`
(pre-order memakai `orders.channel = 'preorder'` + kolom `pickup_at`)

**dining_tables**: `outlet_id`, `label`, `qr_token`

**otp_codes**: `phone`, `code_hash`, `expires_at`, `attempts`

**founder_ledger**: `period`, `founder` (`jonathan`/`novelie`), `kind` (`bagi_laba`/`gaji`/`prive`/`setoran_modal`), `amount`, `note`

**site_scores**: `candidate_name`, `address`, `scores jsonb`, `total`, `notes`

## Gelombang 4

**flavor_polls**, **flavor_votes** (1 suara per member per polling), **creators** (`name`, `handle`, `promo_code`, `deal`, `posted_at`)

## Settings awal (seed)

| Key | Nilai awal | Keterangan |
| --- | --- | --- |
| `pos.packaging_cost` | 2500 | Rupiah per pesanan |
| `points.earn_per_rupiah` | 10000 | 1 poin per Rp10.000 (POS lama, perlu konfirmasi) |
| `points.redeem_points` | 10 | Poin per penukaran |
| `points.redeem_value` | 10000 | Nilai penukaran (perlu konfirmasi: potongan atau hadiah) |
| `hpp.max_single_pct` | 43 | Batas biaya bahan menu satuan |
| `hpp.max_package_pct` | 47 | Batas paket |
| `hpp.max_blended_pct` | 44.5 | Batas gabungan |
| `targets.daily_revenue` | 1000000 | Target harian kiosk |
| `targets.ruko_savings` | 93700000 | Laba ditahan untuk ruko |
| `ops.portion_weight_g` | 150 | Berat porsi ayam |
| `ops.portion_tolerance_g` | 10 | Toleransi QC (perlu konfirmasi) |
| `ops.qc_every_n` | 10 | Timbang 1 dari N porsi |
| `ops.oil_change_after_portions` | 30 | Batas awal ganti minyak |
| `ops.fryer_portions_per_hour` | 60 | Kapasitas pre-order per jam |
| `ops.chicken_yield_factor` | 1.0 | Pengali berat ayam mentah per porsi (perlu diukur) |
| `members.slipping_days` | 14 | "Mulai jarang" |
| `members.lapsed_days` | 21 | "Hilang" |
| `tax.pph_rate` | 0.005 | PPh final UMKM |
| `tax.pph_free_threshold` | 500000000 | Omzet bebas PPh per tahun (orang pribadi) |
| `tax.pbjt_enabled` | false | Aktifkan setelah konfirmasi Bapenda |
| `tax.pbjt_rate` | 0.10 | |
| `device.lock_after_minutes` | 10 | |
| `founder.salary_monthly` | 10000000 | Berdua |
| `founder.salary_trigger_profit` | 10000000 | Laba sebelum gaji, 3 bulan berturut-turut |

## RPC utama

| RPC | Gunanya |
| --- | --- |
| `staff_unlock(staff_id, pin)` | Buka sesi staf di perangkat |
| `record_sale(payload, session_id)` | Simpan penjualan atomik dan idempoten (lihat `docs/arsitektur.md`) |
| `void_order(order_id, reason, session_id)` | Batalkan order, balik stok dan poin |
| `open_shift` / `close_shift` | Kas awal, hitung kas, selisih |
| `start_batch`, `set_active_batch` | Batch marinasi |
| `record_oil_event`, `record_qc` | Log dapur |
| `submit_stock_count` | Stok opname → `count_adjust` |
| `confirm_purchase(purchase_id)` | Draf nota → stok masuk + harga bahan terbaru |

## Akses (RLS)

| Data | owner | kasir | dapur | anon (G3) |
| --- | --- | --- | --- | --- |
| menu, varian, resep, settings | baca/tulis | baca | baca | menu dan harga lewat view publik |
| orders, order_items, payments | baca/tulis | tulis lewat RPC; baca outlet sendiri hari ini | baca item untuk dapur | lewat RPC dengan token |
| members, point_ledger | baca/tulis | cari HP, daftar, lihat poin lewat RPC | tidak | lewat RPC + OTP |
| stock, batch, oil, qc, checklist, suhu | baca/tulis | baca | tulis lewat RPC, baca | tidak |
| purchases, expenses, settlements, tax, payroll, reports, founder_ledger, documents, site_scores | baca/tulis | tidak | tidak | tidak |
| feedback | baca | tulis (iPad) | baca | tulis lewat RPC dengan token |
| audit_log, ai_usage | baca | tidak (ditulis trigger/server) | tidak | tidak |

Tes RLS wajib ada untuk setiap tabel keuangan: login sebagai perangkat dengan sesi kasir dan dapur, pastikan query mengembalikan 0 baris atau ditolak.

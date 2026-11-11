---
paths:
  - "src/lib/ai/**"
  - "src/app/api/**"
---

# Aturan fitur AI (Claude API)

- Panggil Claude API hanya dari server memakai Anthropic TypeScript SDK. Kunci dari `ANTHROPIC_API_KEY`, model dari
  `ANTHROPIC_MODEL` atau `ANTHROPIC_MODEL_FAST`. Jangan menulis nama model di kode fitur.
- Satu file per fitur di `src/lib/ai/` (contoh `read-receipt.ts`, `daily-summary.ts`), berisi prompt, skema Zod output,
  dan fungsi pemanggil. Prompt Bahasa Indonesia, menyebut konteks CiKiPi seperlunya.
- Output yang dipakai program wajib JSON tervalidasi Zod. Kalau validasi gagal: coba sekali lagi, lalu kembalikan error yang jelas.
- Claude tidak menghitung angka bisnis. Angka dihitung kode di `src/lib/finance/`, lalu dikirim ke Claude untuk dirangkum.
- Hasil yang mengubah data (nota belanja, saran harga, draf kampanye) selalu draf dan menunggu konfirmasi manusia.
- "Tanya data" (O12) memakai tool use dengan daftar fungsi baca yang sudah ditentukan. Tidak ada SQL bebas dari model.
- Asisten SOP (K16) hanya menjawab dari dokumen SOP yang diberikan di konteks dan menyebut sumbernya.
- Data pelanggan: kirim agregat. Nama dan nomor HP tidak dikirim ke Claude; personalisasi pesan memakai template di server.
- Catat `input_tokens` dan `output_tokens` setiap panggilan ke tabel `ai_usage`.
- Uji fitur AI dengan mock respons; jangan memanggil API sungguhan di unit test.

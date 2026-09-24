# Panduan Penggunaan

[English](USAGE.md) | [日本語](USAGE.ja.md) | [中文](USAGE.zh.md) | [한국어](USAGE.ko.md) | [Deutsch](USAGE.de.md) | [Español](USAGE.es.md) | **Bahasa Indonesia**

Panduan dari instalasi hingga gambar pertama Anda, lengkap dengan tangkapan layar tampilan sebenarnya.

## 1. Instal

Jalankan installer (`Qwen-Image_2.1_HACK_Studio_Setup.exe`), setujui perjanjian lisensi, lalu instal.
Setelah selesai, buka dari desktop atau menu Start.

Data Anda (gambar hasil, file yang diunggah, riwayat pekerjaan, pengaturan) disimpan di
`%LOCALAPPDATA%\Qwen-Image 2.1 HACK Studio\` dan **tetap ada** saat Anda menghapus instalasi atau memperbarui.

## 2. Daftarkan backend ComfyUI

Pada peluncuran pertama, buka **Pengaturan → Server ComfyUI** dan daftarkan instans ComfyUI yang sudah
terpasang model Qwen-Image 2.1 (lihat README).

![Layar pengaturan backend](screenshots/backend-setup.jpg)

- **Nama**: label bebas (mis. `Local`)
- **URL**: alamat ComfyUI (biasanya `http://127.0.0.1:8188` pada mesin yang sama)
- **input_dir** (opsional): isi hanya jika ComfyUI ada di mesin yang sama — ini sedikit mempercepat unggah
  gambar referensi. Biarkan kosong untuk ComfyUI di mesin lain

Anda dapat mendaftarkan beberapa backend (GPU lain di mesin yang sama, atau mesin lain) — setiap pengiriman
otomatis diarahkan ke yang paling senggang.

## 3. Periksa model

Buka **Pengaturan → Model**. Setiap file model dan custom node dicantumkan beserta status apakah tiap backend
memilikinya.

![Layar Model](screenshots/models.jpg)

Yang kurang akan ditandai; pasang ke ComfyUI (lihat tabel persiapan di README), **restart ComfyUI**,
lalu muat ulang layar ini.

## 4. (Opsional) Pilih opsi akselerasi di Fitur tambahan

**Pengaturan → Fitur tambahan** (atau tombol "⚡ Fitur tambahan" di layar pembuatan) memungkinkan Anda mengatur
sampler, varian UNET / text encoder, Spectrum, SageAttention, pemangkasan output, dan pencegah tidur.
Opsi ini tidak tampil di formulir pembuatan — apa pun yang Anda pilih di sini selalu dipakai.

![Layar Fitur tambahan](screenshots/extra-features.jpg)

Nilai bawaan sudah cukup untuk pemakaian pertama. Untuk kecepatan, aktifkan **Spectrum** dan **SageAttention** —
laporan benchmark yang bisa dilipat di layar ini menunjukkan efek terukur dari setiap kombinasi.

- **Pangkas setelah pembuatan** memotong bagian atas dan bawah agar tinggi tepat 1080/720/480 px
  (model sendiri menghasilkan kelipatan 16, mis. 1080 → 1088).
- **Pencegah tidur** menjaga Windows tetap terjaga selama pekerjaan dengan memutar suara yang sangat pelan secara berkala.
  Matikan jika suaranya mengganggu.

## 5. Buat gambar

Kembali ke layar utama, pilih tab — teks-ke-gambar atau Edit — lalu masukkan prompt.

![Formulir pembuatan](screenshots/generation.jpg)

- **Ukuran / rasio aspek**: pilih dari preset (ukuran piksel sebenarnya yang akan dikirim ditampilkan langsung)
- Pada tab **Edit**, tambahkan satu atau lebih gambar referensi. Gambar besar otomatis diperkecil ke sekitar
  1 megapiksel dengan rasio aspek tetap terjaga

### 💡 Tips: Edit paling baik dengan dua gambar referensi

Dengan hanya satu gambar referensi, Qwen-Image 2.1 Edit kadang hampir tidak berpengaruh. Aplikasi otomatis
menambahkan gambar pengisi abu-abu pada referensi tunggal. Jika hasilnya masih tampak tidak berubah, coba
tambahkan gambar kedua yang terkait.

Setelah semuanya terisi, klik **Buat** untuk menambahkan pekerjaan ke antrean.

## 6. Periksa hasil di antrean

Pekerjaan yang dikirim muncul di antrean; setelah selesai, gambarnya langsung ditampilkan. Klik thumbnail untuk
membuka penampil (jendela penuh atau layar penuh), dan gunakan "Pulihkan ke formulir" untuk mengembalikan
pengaturan persis dari pekerjaan sebelumnya ke formulir.

## Butuh bantuan?

Gunakan [Issues](../../issues) untuk pertanyaan atau laporan bug.

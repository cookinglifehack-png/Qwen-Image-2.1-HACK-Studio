# Qwen-Image 2.1 HACK Studio

[English](README.md) | [日本語](README.ja.md) | [中文](README.zh.md) | [한국어](README.ko.md) | [Deutsch](README.de.md) | [Español](README.es.md) | **Bahasa Indonesia**

### ⬇️ [Unduh installer (Windows)](https://github.com/cookinglifehack-png/Qwen-Image-2.1-HACK-Studio/releases/latest/download/Qwen-Image_2.1_HACK_Studio_Setup.exe)

Aplikasi Windows yang memungkinkan Anda memakai model pembuat gambar **Qwen-Image 2.1**, yang
berjalan di ComfyUI, begitu selesai diinstal. Tanpa mengedit workflow ComfyUI, tanpa perlu
pengetahuan PHP — cukup klik ganda untuk membuka, lalu buat teks-ke-gambar dan edit gambar
sepenuhnya lewat kontrol formulir ala browser.

> **Alat tidak resmi.** Aplikasi ini dibuat oleh pihak ketiga independen dan tidak berafiliasi
> dengan tim Qwen. **Model tidak disertakan** dan tidak tercakup oleh lisensi perangkat lunak ini:
> Qwen-Image 2.1 didistribusikan dengan **Qwen Research License (hanya untuk penggunaan
> non-komersial; penggunaan komersial memerlukan lisensi terpisah dari pemberi lisensi)**.
> Mohon baca sendiri ketentuan model tersebut.

## Persiapan (ComfyUI + model Qwen-Image 2.1)

Installer hanya berisi aplikasinya sendiri, **bukan** ComfyUI atau bobot model Qwen-Image 2.1.
Anda tetap memerlukan instans ComfyUI dengan model terpasang, lalu mendaftarkannya sebagai backend
dari layar Pengaturan aplikasi. Letakkan file berikut di `ComfyUI/models/<folder>/`
(nama file harus persis sama dengan yang diharapkan aplikasi).

### ComfyUI

- Panduan instalasi resmi (Windows portable): https://docs.comfy.org/installation/comfyui_portable_windows

### ① Minimum (hanya untuk menjalankan Qwen-Image 2.1)

Semuanya berasal dari repositori resmi [`Comfy-Org/Qwen-Image-2.1`](https://huggingface.co/Comfy-Org/Qwen-Image-2.1).

| Peran | Nama file | Ukuran | Folder tujuan |
|---|---|---:|---|
| UNet (kuantisasi int8) | `qwen_image_2.1_int8_convrot.safetensors` | 6,8GB | `models/diffusion_models/` |
| Text encoder (kuantisasi int8) | `qwen3vl_8b_int8_convrot.safetensors` | 8,8GB | `models/text_encoders/` |
| VAE | `qwen_image_2.1_vae_bf16.safetensors` | 645MB | `models/vae/` |

Ini sudah cukup untuk teks-ke-gambar dan edit gambar (konfigurasi dasar, tanpa akselerasi).

### ② Direkomendasikan (konfigurasi tercepat yang terukur)

Seperti ditunjukkan benchmark di bawah, ini memberi **pembuatan 2,1–2,2× lebih cepat pada Full HD**
dengan perbedaan kualitas yang kecil. Tambahkan di atas ①.

| Peran | Sumber | Penempatan / langkah |
|---|---|---|
| **Node akselerasi Spectrum** | https://github.com/awdqwdasdg/Comfyui-Spectrum-Qwen2.1 | clone ke `custom_nodes/` (`SpectrumQwenImage21`). Tanpa dependensi tambahan |
| **Node SageAttention** | https://github.com/kijai/ComfyUI-KJNodes | clone ke `custom_nodes/` (`PathchSageAttentionKJ`) |
| **SageAttention** (wheel Windows) | https://github.com/woct0rdho/SageAttention/releases | `pip install triton-windows`, lalu pasang wheel yang sesuai dengan versi PyTorch/CUDA Anda |
| Text encoder bf16 (opsional) | repositori resmi yang sama | `qwen3vl_8b_bf16.safetensors` (17GB) → `models/text_encoders/`. **Kecepatan sama dengan int8**, jadi pakai jika VRAM lega |

Setelah memasang, **restart ComfyUI**, lalu periksa status instalasi di **Pengaturan → Model** pada aplikasi ini.

### ③ Semuanya (varian kuantisasi lain)

| Peran | Nama file | Ukuran | Kegunaan |
|---|---|---:|---|
| UNet bf16 (presisi penuh) | `qwen_image_2.1_bf16.safetensors` | 14GB | Tanpa kuantisasi; kualitas diutamakan |
| Text encoder w4a8 | `qwen3vl_8b_w4a8.safetensors` | 5,9GB | Paling ringan; untuk VRAM terbatas |

Keduanya ada di `Comfy-Org/Qwen-Image-2.1` dan diletakkan di folder yang sama dengan ① dan ②
(`diffusion_models/` / `text_encoders/`).

## Pengaturan yang direkomendasikan

Atur di **Pengaturan → Fitur tambahan**, atau lewat tombol "⚡ Fitur tambahan" di kanan atas layar pembuatan.

| Item | Rekomendasi | Alasan |
|---|---|---|
| **Sampler** | `euler` + `simple` | Bawaan workflow resmi Qwen-Image 2.1 |
| **UNET** | `int8 (convrot)` | 6,8GB; praktis tidak ada beda terlihat dari bf16 |
| **Text encoder** | `bf16` jika VRAM cukup, jika tidak `int8 (convrot)` | **Hampir tidak ada beda kecepatan** — pilih berdasarkan VRAM, bukan kecepatan |
| **Spectrum** | **ON** | **~2,0× lebih cepat** — efek terbesar. Detail halus sedikit lebih lembut |
| **SageAttention** | **ON** | Tambahan +5–10%, lebih besar pada resolusi tinggi |

Bawaan: 25 langkah dan CFG 1 (naikkan CFG hanya saat memakai negative prompt).

## Benchmark (terukur)

Kondisi: **1920×1088 / UNet `int8_convrot` tetap / euler + simple / 25 langkah /
CFG 1 / seed tetap / teks-ke-gambar**, pada backend ComfyUI lokal.

| Text encoder | Tanpa | Sage | **Spectrum** | **Sage + Spectrum** |
|---|---:|---:|---:|---:|
| **int8_convrot** (8,8GB) | 187,9s | 171,0s (1,10×) | 92,6s (2,03×) | **88,3s (2,13×)** |
| **bf16** (17GB) | 196,2s | 175,3s (1,12×) | 93,0s (2,11×) | **87,9s (2,23×)** |
| **w4a8** (5,9GB) | 191,7s | 176,0s (1,09×) | 96,9s (1,98×) | **90,7s (2,11×)** |

- Spectrum saja sekitar 2×, dengan encoder apa pun. Sage menambah +9–12% dan bisa digabung.
- Pilihan text encoder tidak berpengaruh berarti pada kecepatan (dalam rentang derau pengukuran).
- Sage lebih membantu pada resolusi lebih tinggi: 1,17× pada 2720×1536, 1,08× pada 1920×1088.

> **Tentang "Full HD":** meminta 1920×1080 sebenarnya menghasilkan **1920×1088**, karena model
> membulatkan ukuran gambar ke atas ke kelipatan 16. Jika Anda butuh tepat 1080p, gunakan
> **Pengaturan → Fitur tambahan → Output & sistem → Pangkas setelah pembuatan** untuk memotong 8px
> di atas dan bawah (tersedia preset 480/720/1080).

## Layar pembuatan — satu tata letak tanpa menebak-nebak

![Layar pembuatan](screenshots/generation.jpg)

Beralih antara teks-ke-gambar dan Edit (dengan gambar referensi) lewat tab. Antrean di sebelah kanan
menampilkan gambar yang sudah selesai begitu pekerjaan rampung, dan mengeklik thumbnail membuka
penampil jendela penuh / layar penuh. Ukuran dan rasio aspek dipilih lewat preset, dengan resolusi
sebenarnya yang akan dikirim ditampilkan secara langsung.

## Fitur tambahan — pilih opsi akselerasi dengan yakin

![Layar Fitur tambahan](screenshots/extra-features.jpg)

Sampler dan scheduler (bawaan ditandai), varian UNET / text encoder, Spectrum dan SageAttention —
efek tiap opsi dijelaskan tepat di sebelahnya, dan laporan benchmark yang bisa dilipat menyorot
kombinasi yang direkomendasikan. Fitur tambahan juga memiliki sakelar **pencegah tidur** untuk
pekerjaan panjang (suara yang sangat pelan diputar berkala agar Windows tetap terjaga) dan opsi **pangkas**.

## Tab Model — lihat apa yang sudah terpasang

![Layar Model](screenshots/models.jpg)

Tab Model memeriksa file model dan custom node apa yang benar-benar dimiliki tiap ComfyUI yang
terhubung, sehingga Anda bisa langsung melihat apa yang kurang.

## Tips mode Edit

Qwen-Image 2.1 Edit bisa kurang andal jika hanya diberi **satu** gambar referensi. Aplikasi otomatis
menambahkan gambar pengisi abu-abu netral pada referensi tunggal agar berperilaku seperti kasus dua
gambar. Gambar referensi yang lebih dari ~1 megapiksel otomatis diperkecil (rasio aspek dipertahankan).

## Cocok untuk siapa

- Orang yang ingin memakai Qwen-Image 2.1 hanya lewat kontrol formulir, tanpa menyentuh graf node ComfyUI
- Orang yang ingin memilih kuantisasi dan akselerasi berdasarkan angka terukur, bukan tebakan
- Orang dengan beberapa mesin/GPU ComfyUI yang ingin pekerjaan otomatis dialihkan ke yang paling senggang

## Unduh

Installer Windows (mandiri, tanpa runtime tambahan) tersedia di Releases.

Lihat [USAGE.md](USAGE.md) untuk panduan langkah demi langkah dengan tangkapan layar.

## Masukan & Pertanyaan

Silakan gunakan [Issues](../../issues) untuk laporan bug, permintaan fitur, atau pertanyaan lainnya.

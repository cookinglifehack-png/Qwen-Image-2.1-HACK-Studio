# Qwen-Image 2.1 HACK Studio

**English** | [日本語](README.ja.md) | [中文](README.zh.md) | [한국어](README.ko.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Bahasa Indonesia](README.id.md)

### ⬇️ [Download the installer (Windows)](https://github.com/cookinglifehack-png/Qwen-Image-2.1-HACK-Studio/releases/latest/download/Qwen-Image_2.1_HACK_Studio_Setup.exe)

A Windows app that lets you use the image generation model **Qwen-Image 2.1**,
which runs on ComfyUI, the moment you install it. No ComfyUI workflow editing,
no PHP knowledge required — just double-click to launch, then generate
text-to-image and image-edit results entirely through browser-style form controls.

> **Unofficial tool.** This app is made by an independent third party and is not
> affiliated with the Qwen team. **The model is not included** and is not covered
> by this software's license: Qwen-Image 2.1 is distributed under the
> **Qwen Research License (non-commercial use only; commercial use requires a
> separate license from the licensor)**. Please read the model's terms yourself.

## Setup (ComfyUI + Qwen-Image 2.1 models)

The installer bundles the app itself, but **not** ComfyUI or the Qwen-Image 2.1
model weights — you still need a ComfyUI instance with the models installed, and
register it as a backend from the app's Settings screen. Download the files below
and place them under `ComfyUI/models/<folder>/` (the filenames must exactly match
what the app expects).

### ComfyUI itself

- Official install guide (Windows portable): https://docs.comfy.org/installation/comfyui_portable_windows

### ① Minimum (just to run Qwen-Image 2.1)

All of these come from the official [`Comfy-Org/Qwen-Image-2.1`](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) repository.

| Role | Filename | Size | Target folder |
|---|---|---:|---|
| UNet (int8 quantized) | `qwen_image_2.1_int8_convrot.safetensors` | 6.8GB | `models/diffusion_models/` |
| Text encoder (int8 quantized) | `qwen3vl_8b_int8_convrot.safetensors` | 8.8GB | `models/text_encoders/` |
| VAE | `qwen_image_2.1_vae_bf16.safetensors` | 645MB | `models/vae/` |

That is enough for both text-to-image and image editing (baseline, no acceleration).

### ② Recommended (fastest measured configuration)

As the benchmark below shows, this gives **2.1–2.2× faster generation at Full HD**
with only a slight quality difference. Add these on top of ①.

| Role | Source | Placement / steps |
|---|---|---|
| **Spectrum acceleration node** | https://github.com/awdqwdasdg/Comfyui-Spectrum-Qwen2.1 | clone into `custom_nodes/` (`SpectrumQwenImage21`). No extra dependencies |
| **SageAttention node** | https://github.com/kijai/ComfyUI-KJNodes | clone into `custom_nodes/` (`PathchSageAttentionKJ`) |
| **SageAttention itself** (Windows wheel) | https://github.com/woct0rdho/SageAttention/releases | `pip install triton-windows`, then pip install the wheel matching your PyTorch/CUDA version |
| Text encoder bf16 (optional) | same official repository | `qwen3vl_8b_bf16.safetensors` (17GB) → `models/text_encoders/`. **Same speed as int8**, so use it if you have spare VRAM |

After installing, **restart ComfyUI**, then check the installation status under
**Settings → Models** in this app.

### ③ Everything (other quantization variants)

| Role | Filename | Size | Use |
|---|---|---:|---|
| UNet bf16 (full precision) | `qwen_image_2.1_bf16.safetensors` | 14GB | No quantization; quality first |
| Text encoder w4a8 | `qwen3vl_8b_w4a8.safetensors` | 5.9GB | Lightest; for tight VRAM |

Both are in `Comfy-Org/Qwen-Image-2.1`, placed in the same folders as ① and ②
(`diffusion_models/` / `text_encoders/`).

## Recommended settings

Set these in **Settings → Extras**, or via the "⚡ Extras" button at the top right
of the generation screen.

| Item | Recommended | Why |
|---|---|---|
| **Sampler** | `euler` + `simple` | Default of the official Qwen-Image 2.1 workflow |
| **UNET** | `int8 (convrot)` | 6.8GB; practically no visible difference from bf16 |
| **Text encoder** | `bf16` if VRAM allows, otherwise `int8 (convrot)` | **Almost no speed difference** — choose by VRAM, not speed |
| **Spectrum** | **ON** | **~2.0× faster** — the biggest effect. Slightly softer fine detail |
| **SageAttention** | **ON** | Another +5–10%, more at higher resolutions |

Defaults are 25 steps and CFG 1 (raise CFG only when using a negative prompt).

## Benchmark (measured)

Conditions: **1920×1088 / UNet `int8_convrot` fixed / euler + simple / 25 steps /
CFG 1 / fixed seed / text-to-image**, on a local ComfyUI backend.

| Text encoder | None | Sage | **Spectrum** | **Sage + Spectrum** |
|---|---:|---:|---:|---:|
| **int8_convrot** (8.8GB) | 187.9s | 171.0s (1.10×) | 92.6s (2.03×) | **88.3s (2.13×)** |
| **bf16** (17GB) | 196.2s | 175.3s (1.12×) | 93.0s (2.11×) | **87.9s (2.23×)** |
| **w4a8** (5.9GB) | 191.7s | 176.0s (1.09×) | 96.9s (1.98×) | **90.7s (2.11×)** |

- Spectrum alone is about 2×, with any encoder. Sage adds +9–12% and stacks with it.
- The text-encoder choice has no meaningful effect on speed (within measurement noise).
- Sage helps more at higher resolution: 1.17× at 2720×1536, 1.08× at 1920×1088.

> **About "Full HD":** requesting 1920×1080 actually produces **1920×1088**, because
> the model rounds image sizes up to a multiple of 16. If you need exactly 1080p, use
> **Settings → Extras → Output & system → Trim after generation** to cut 8px off
> top and bottom (480/720/1080 presets available).

## Generation screen — a single, no-guesswork layout

![Generation screen](screenshots/generation.jpg)

Switch between text-to-image and Edit (with reference images) using tabs. The queue
on the right shows finished images the moment a job completes, and clicking a
thumbnail opens a full-window / fullscreen viewer. Size and aspect ratio are set
with presets, with the actual resolution that will be submitted shown live.

## Extras — pick acceleration options with confidence

![Extras screen](screenshots/extra-features.jpg)

Sampler and scheduler (with the default marked), UNET / text-encoder variant,
Spectrum and SageAttention — each option's effect is explained right next to it,
and a collapsible benchmark report highlights the recommended combination.
Extras also has a **sleep-prevention** toggle for long jobs (a very quiet sound is
played periodically to keep Windows awake) and a **trim** option.

## Models tab — see what is installed

![Models screen](screenshots/models.jpg)

The Models tab checks which model files and custom nodes each connected ComfyUI
actually has, so you can see at a glance what is missing.

## Edit mode tip

Qwen-Image 2.1 Edit can be unreliable when given only **one** reference image. The
app automatically pads a single reference with a neutral gray filler image so it
behaves like the two-image case. Reference images larger than ~1 megapixel are
automatically downscaled (aspect ratio preserved).

## Who this is for

- People who want to use Qwen-Image 2.1 through form controls alone, without
  touching ComfyUI's node graph
- People who'd rather choose quantization and acceleration options based on
  measured numbers, not guesses
- People with multiple ComfyUI machines/GPUs who want jobs routed to the least
  busy one automatically

## Download

The Windows installer (self-contained, no additional runtime required) is
available from Releases.

See [USAGE.md](USAGE.md) for a step-by-step walkthrough with screenshots.

## Feedback & Questions

Please use [Issues](../../issues) for bug reports, feature requests, or any
other questions.

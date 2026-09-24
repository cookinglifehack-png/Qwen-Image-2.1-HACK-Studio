# Usage Guide

[English](USAGE.md) | [日本語](USAGE.ja.md)

A walkthrough from installation to your first generated image, with screenshots
of the actual screens.

## 1. Install

Run the installer (`Qwen-Image_2.1_HACK_Studio_Setup.exe`), agree to the license
agreement, and install. Once done, launch it from the desktop or Start menu.

Your data (generated images, uploads, job history, settings) is stored in
`%LOCALAPPDATA%\Qwen-Image 2.1 HACK Studio\` and is **kept** when you uninstall or
upgrade.

## 2. Register a ComfyUI backend

On first launch, go to **Settings → ComfyUI Server** and register a ComfyUI
instance that already has the Qwen-Image 2.1 models installed (see the README).

![ComfyUI backend setup screen](screenshots/backend-setup.jpg)

- **Name**: any label you like (e.g. `Local`)
- **URL**: the ComfyUI URL (usually `http://127.0.0.1:8188` for the same machine)
- **input_dir** (optional): only fill this in if ComfyUI is on the same
  machine — it speeds up uploading reference images slightly. Leave it blank
  for a ComfyUI instance on another machine

You can register multiple backends (other GPUs on the same machine, or other
machines entirely) — each submission is automatically routed to whichever is
least busy.

## 3. Check the models

Open **Settings → Models**. Each model file and custom node is listed with
whether each backend has it.

![Models screen](screenshots/models.jpg)

Anything missing is marked; install it into ComfyUI (see the README's setup
tables), **restart ComfyUI**, and reload this screen.

## 4. (Optional) Choose acceleration options in Extras

**Settings → Extras** (or the "⚡ Extras" button on the generation screen) lets
you configure the sampler, UNET / text-encoder variant, Spectrum, SageAttention,
output trimming and sleep prevention. These aren't shown on the generation
form — whatever you choose here is always what's used.

![Extras screen](screenshots/extra-features.jpg)

The defaults work fine for first-time use. For speed, turn **Spectrum** and
**SageAttention** on — the collapsible benchmark report on this screen shows the
measured effect of every combination.

- **Trim after generation** cuts the top and bottom to reach 1080/720/480 px
  height exactly (the model itself outputs multiples of 16, e.g. 1080 → 1088).
- **Sleep prevention** keeps Windows awake during a job by playing a very quiet
  sound periodically. Turn it off if the sound bothers you.

## 5. Generate an image

Back on the main screen, pick a tab — text-to-image or Edit — and enter a prompt.

![Generation form](screenshots/generation.jpg)

- **Size / aspect ratio**: pick from presets (the actual pixel size that will be
  submitted is shown live)
- On the **Edit** tab, add one or more reference images. Large images are
  downscaled automatically to about 1 megapixel, keeping the aspect ratio

### 💡 Tip: Edit works best with two reference images

With a single reference image, Qwen-Image 2.1 Edit sometimes has little effect.
The app pads a single reference with a gray filler image automatically. If a
result still looks unchanged, try adding a second, related image.

Once everything's filled in, click **Generate** to add the job to the queue.

## 6. Check results in the queue

Submitted jobs appear in the queue; when a job finishes, its image is shown
inline. Click a thumbnail to open the viewer (full-window, or fullscreen), and use
"Restore to form" to bring the exact settings of a past job back into the form.

## Need help?

Use [Issues](../../issues) for questions or bug reports.

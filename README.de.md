# Qwen-Image 2.1 HACK Studio

[English](README.md) | [日本語](README.ja.md) | [中文](README.zh.md) | [한국어](README.ko.md) | **Deutsch** | [Español](README.es.md) | [Bahasa Indonesia](README.id.md)

### ⬇️ [Installer herunterladen (Windows)](https://github.com/cookinglifehack-png/Qwen-Image-2.1-HACK-Studio/releases/latest/download/Qwen-Image_2.1_HACK_Studio_Setup.exe)

Eine Windows-App, mit der Sie das Bildgenerierungsmodell **Qwen-Image 2.1**, das auf
ComfyUI läuft, gleich nach der Installation nutzen können. Kein Bearbeiten von
ComfyUI-Workflows, keine PHP-Kenntnisse nötig – per Doppelklick starten und Text-zu-Bild
sowie Bildbearbeitung komplett über browserähnliche Formularelemente erzeugen.

> **Inoffizielles Tool.** Diese App stammt von einem unabhängigen Dritten und steht in
> keiner Verbindung zum Qwen-Team. **Das Modell ist nicht enthalten** und nicht von der
> Lizenz dieser Software abgedeckt: Qwen-Image 2.1 wird unter der
> **Qwen Research License (nur nicht-kommerzielle Nutzung; kommerzielle Nutzung erfordert
> eine separate Lizenz des Lizenzgebers)** vertrieben. Bitte lesen Sie die Modellbedingungen selbst.

## Einrichtung (ComfyUI + Qwen-Image-2.1-Modelle)

Der Installer enthält nur die App selbst, **nicht** ComfyUI oder die Gewichte von Qwen-Image 2.1.
Sie benötigen eine ComfyUI-Instanz mit den installierten Modellen und registrieren sie in den
Einstellungen der App als Backend. Legen Sie die folgenden Dateien unter `ComfyUI/models/<Ordner>/`
ab (die Dateinamen müssen exakt mit den von der App erwarteten übereinstimmen).

### ComfyUI selbst

- Offizielle Installationsanleitung (Windows Portable): https://docs.comfy.org/installation/comfyui_portable_windows

### ① Minimum (nur Qwen-Image 2.1 ausführen)

Alles aus dem offiziellen Repository [`Comfy-Org/Qwen-Image-2.1`](https://huggingface.co/Comfy-Org/Qwen-Image-2.1).

| Rolle | Dateiname | Größe | Zielordner |
|---|---|---:|---|
| UNet (int8 quantisiert) | `qwen_image_2.1_int8_convrot.safetensors` | 6,8GB | `models/diffusion_models/` |
| Text-Encoder (int8 quantisiert) | `qwen3vl_8b_int8_convrot.safetensors` | 8,8GB | `models/text_encoders/` |
| VAE | `qwen_image_2.1_vae_bf16.safetensors` | 645MB | `models/vae/` |

Damit laufen Text-zu-Bild und Bildbearbeitung (Basiskonfiguration, ohne Beschleunigung).

### ② Empfohlen (schnellste gemessene Konfiguration)

Wie der Benchmark unten zeigt, ergibt sich in Full HD eine **2,1–2,2-fache Beschleunigung**
bei nur geringem Qualitätsunterschied. Zusätzlich zu ① installieren.

| Rolle | Quelle | Ablage / Schritte |
|---|---|---|
| **Spectrum-Beschleunigungsknoten** | https://github.com/awdqwdasdg/Comfyui-Spectrum-Qwen2.1 | in `custom_nodes/` klonen (`SpectrumQwenImage21`), keine zusätzlichen Abhängigkeiten |
| **SageAttention-Knoten** | https://github.com/kijai/ComfyUI-KJNodes | in `custom_nodes/` klonen (`PathchSageAttentionKJ`) |
| **SageAttention selbst** (Windows-Wheel) | https://github.com/woct0rdho/SageAttention/releases | `pip install triton-windows`, dann das zu PyTorch/CUDA passende Wheel installieren |
| Text-Encoder bf16 (optional) | gleiches offizielles Repository | `qwen3vl_8b_bf16.safetensors` (17GB) → `models/text_encoders/`. **Gleiche Geschwindigkeit wie int8** – bei genügend VRAM nutzbar |

Starten Sie nach der Installation **ComfyUI neu** und prüfen Sie den Status unter
**Einstellungen → Modelle** in dieser App.

### ③ Alles (weitere Quantisierungsvarianten)

| Rolle | Dateiname | Größe | Verwendung |
|---|---|---:|---|
| UNet bf16 (volle Präzision) | `qwen_image_2.1_bf16.safetensors` | 14GB | Keine Quantisierung; Qualität zuerst |
| Text-Encoder w4a8 | `qwen3vl_8b_w4a8.safetensors` | 5,9GB | Am leichtesten; für knappen VRAM |

Beide liegen in `Comfy-Org/Qwen-Image-2.1` und kommen in dieselben Ordner wie ① und ②
(`diffusion_models/` / `text_encoders/`).

## Empfohlene Einstellungen

Einstellbar unter **Einstellungen → Extras** oder über die Schaltfläche „⚡ Extras“ oben rechts im Generierungsbildschirm.

| Punkt | Empfehlung | Grund |
|---|---|---|
| **Sampler** | `euler` + `simple` | Standard des offiziellen Qwen-Image-2.1-Workflows |
| **UNET** | `int8 (convrot)` | 6,8GB; praktisch kein sichtbarer Unterschied zu bf16 |
| **Text-Encoder** | `bf16`, wenn VRAM reicht, sonst `int8 (convrot)` | **Kaum Geschwindigkeitsunterschied** – nach VRAM wählen, nicht nach Tempo |
| **Spectrum** | **AN** | **~2,0× schneller** – der größte Effekt. Feine Details werden etwas weicher |
| **SageAttention** | **AN** | Weitere +5–10 %, mehr bei höheren Auflösungen |

Standard sind 25 Schritte und CFG 1 (CFG nur bei Verwendung eines Negativ-Prompts erhöhen).

## Benchmark (gemessen)

Bedingungen: **1920×1088 / UNet `int8_convrot` fest / euler + simple / 25 Schritte /
CFG 1 / fester Seed / Text-zu-Bild**, auf einem lokalen ComfyUI-Backend.

| Text-Encoder | Keine | Sage | **Spectrum** | **Sage + Spectrum** |
|---|---:|---:|---:|---:|
| **int8_convrot** (8,8GB) | 187,9s | 171,0s (1,10×) | 92,6s (2,03×) | **88,3s (2,13×)** |
| **bf16** (17GB) | 196,2s | 175,3s (1,12×) | 93,0s (2,11×) | **87,9s (2,23×)** |
| **w4a8** (5,9GB) | 191,7s | 176,0s (1,09×) | 96,9s (1,98×) | **90,7s (2,11×)** |

- Spectrum allein bringt etwa das Doppelte, mit jedem Encoder. Sage bringt +9–12 % und lässt sich kombinieren.
- Die Wahl des Text-Encoders hat keinen nennenswerten Einfluss auf die Geschwindigkeit (im Messrauschen).
- Sage hilft bei höherer Auflösung mehr: 1,17× bei 2720×1536, 1,08× bei 1920×1088.

> **Zu „Full HD“:** Wer 1920×1080 anfordert, erhält tatsächlich **1920×1088**, weil das Modell
> Bildgrößen auf ein Vielfaches von 16 aufrundet. Für exakt 1080p nutzen Sie
> **Einstellungen → Extras → Ausgabe & System → Nach der Generierung beschneiden**, um oben und unten
> je 8px abzuschneiden (Voreinstellungen 480/720/1080 verfügbar).

## Generierungsbildschirm – ein Layout ohne Rätselraten

![Generierungsbildschirm](screenshots/generation.jpg)

Per Tabs wechseln Sie zwischen Text-zu-Bild und Edit (mit Referenzbildern). Die Warteschlange rechts
zeigt fertige Bilder, sobald ein Auftrag abgeschlossen ist; ein Klick auf die Miniatur öffnet einen
Vollfenster-/Vollbild-Viewer. Größe und Seitenverhältnis werden per Voreinstellung gewählt, die
tatsächlich übermittelte Auflösung wird live angezeigt.

## Extras – Beschleunigungsoptionen mit Vertrauen wählen

![Extras-Bildschirm](screenshots/extra-features.jpg)

Sampler und Scheduler (Standard markiert), UNET-/Text-Encoder-Variante, Spectrum und SageAttention –
die Wirkung jeder Option wird direkt daneben erklärt, und ein einklappbarer Benchmark-Bericht hebt
die empfohlene Kombination hervor. Unter Extras finden Sie außerdem einen **Ruhezustandsschutz**
für lange Aufträge (es wird regelmäßig ein sehr leiser Ton abgespielt, damit Windows wach bleibt)
und eine **Beschneiden**-Option.

## Modelle-Tab – sehen, was installiert ist

![Modelle-Bildschirm](screenshots/models.jpg)

Der Modelle-Tab prüft, welche Modelldateien und Custom Nodes jede verbundene ComfyUI-Instanz
tatsächlich besitzt, sodass Sie auf einen Blick sehen, was fehlt.

## Tipp zum Edit-Modus

Qwen-Image 2.1 Edit kann unzuverlässig sein, wenn nur **ein** Referenzbild vorliegt. Die App ergänzt
ein einzelnes Referenzbild automatisch um ein neutrales graues Füllbild, sodass es sich wie der
Zwei-Bild-Fall verhält. Referenzbilder über ca. 1 Megapixel werden automatisch verkleinert
(Seitenverhältnis bleibt erhalten).

## Für wen ist das?

- Menschen, die Qwen-Image 2.1 nur über Formularelemente nutzen möchten, ohne den ComfyUI-Node-Graphen anzufassen
- Menschen, die Quantisierung und Beschleunigung lieber anhand gemessener Zahlen als nach Bauchgefühl wählen
- Menschen mit mehreren ComfyUI-Rechnern/GPUs, die Aufträge automatisch an den am wenigsten ausgelasteten verteilen möchten

## Download

Der Windows-Installer (eigenständig, keine zusätzliche Laufzeitumgebung nötig) ist unter Releases erhältlich.

Eine Schritt-für-Schritt-Anleitung mit Screenshots finden Sie in [USAGE.de.md](USAGE.de.md).

## Feedback & Fragen

Bitte nutzen Sie [Issues](../../issues) für Fehlerberichte, Funktionswünsche oder sonstige Fragen.

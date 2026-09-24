# Bedienungsanleitung

[English](USAGE.md) | [日本語](USAGE.ja.md) | [中文](USAGE.zh.md) | [한국어](USAGE.ko.md) | **Deutsch** | [Español](USAGE.es.md) | [Bahasa Indonesia](USAGE.id.md)

Eine Anleitung von der Installation bis zum ersten erzeugten Bild, mit Screenshots der tatsächlichen Bildschirme.

## 1. Installieren

Starten Sie den Installer (`Qwen-Image_2.1_HACK_Studio_Setup.exe`), stimmen Sie der Lizenzvereinbarung zu und
installieren Sie. Danach können Sie die App über den Desktop oder das Startmenü öffnen.

Ihre Daten (erzeugte Bilder, Uploads, Auftragsverlauf, Einstellungen) liegen in
`%LOCALAPPDATA%\Qwen-Image 2.1 HACK Studio\` und **bleiben** beim Deinstallieren oder Aktualisieren erhalten.

## 2. ComfyUI-Backend registrieren

Gehen Sie beim ersten Start zu **Einstellungen → ComfyUI-Server** und registrieren Sie eine ComfyUI-Instanz,
auf der die Qwen-Image-2.1-Modelle bereits installiert sind (siehe README).

![Bildschirm zur Backend-Einrichtung](screenshots/backend-setup.jpg)

- **Name**: beliebige Bezeichnung (z. B. `Local`)
- **URL**: die ComfyUI-Adresse (auf demselben Rechner meist `http://127.0.0.1:8188`)
- **input_dir** (optional): nur ausfüllen, wenn ComfyUI auf demselben Rechner läuft – das beschleunigt den
  Upload von Referenzbildern etwas. Bei einer ComfyUI-Instanz auf einem anderen Rechner leer lassen

Sie können mehrere Backends registrieren (weitere GPUs im selben Rechner oder andere Rechner) – jeder Auftrag
wird automatisch an das am wenigsten ausgelastete verteilt.

## 3. Modelle prüfen

Öffnen Sie **Einstellungen → Modelle**. Jede Modelldatei und jeder Custom Node wird aufgelistet, samt Angabe,
ob das jeweilige Backend sie besitzt.

![Modelle-Bildschirm](screenshots/models.jpg)

Fehlendes ist markiert; installieren Sie es gemäß den Tabellen in der README in ComfyUI, **starten Sie ComfyUI neu**
und laden Sie diesen Bildschirm neu.

## 4. (Optional) Beschleunigungsoptionen in den Extras wählen

Unter **Einstellungen → Extras** (oder über die Schaltfläche „⚡ Extras“ im Generierungsbildschirm) konfigurieren Sie
Sampler, UNET-/Text-Encoder-Variante, Spectrum, SageAttention, Ausgabe-Beschnitt und Ruhezustandsschutz.
Diese Optionen erscheinen nicht im Generierungsformular – was Sie hier wählen, wird immer verwendet.

![Extras-Bildschirm](screenshots/extra-features.jpg)

Für den Anfang genügen die Standardwerte. Für mehr Tempo schalten Sie **Spectrum** und **SageAttention** ein – der
einklappbare Benchmark-Bericht auf dieser Seite zeigt die gemessene Wirkung jeder Kombination.

- **Nach der Generierung beschneiden** schneidet oben und unten ab, um exakt 1080/720/480 px Höhe zu erreichen
  (das Modell selbst gibt Vielfache von 16 aus, z. B. 1080 → 1088).
- **Ruhezustandsschutz** hält Windows während eines Auftrags wach, indem regelmäßig ein sehr leiser Ton abgespielt wird.
  Schalten Sie ihn aus, wenn Sie der Ton stört.

## 5. Ein Bild erzeugen

Wählen Sie im Hauptbildschirm einen Tab – Text-zu-Bild oder Edit – und geben Sie einen Prompt ein.

![Generierungsformular](screenshots/generation.jpg)

- **Größe / Seitenverhältnis**: aus Voreinstellungen wählen (die tatsächlich übermittelte Pixelgröße wird live angezeigt)
- Im Tab **Edit** fügen Sie ein oder mehrere Referenzbilder hinzu. Große Bilder werden automatisch auf etwa
  1 Megapixel verkleinert, das Seitenverhältnis bleibt erhalten

### 💡 Tipp: Edit funktioniert am besten mit zwei Referenzbildern

Mit nur einem Referenzbild hat Qwen-Image 2.1 Edit manchmal kaum Wirkung. Die App ergänzt ein einzelnes
Referenzbild automatisch um ein graues Füllbild. Sieht das Ergebnis trotzdem unverändert aus, fügen Sie ein
zweites, passendes Bild hinzu.

Wenn alles ausgefüllt ist, klicken Sie auf **Generieren**, um den Auftrag in die Warteschlange zu stellen.

## 6. Ergebnisse in der Warteschlange prüfen

Übermittelte Aufträge erscheinen in der Warteschlange; sobald einer fertig ist, wird sein Bild direkt angezeigt.
Ein Klick auf die Miniatur öffnet den Viewer (Vollfenster oder Vollbild); mit „In Formular wiederherstellen“
holen Sie die exakten Einstellungen eines früheren Auftrags zurück ins Formular.

## Hilfe nötig?

Nutzen Sie [Issues](../../issues) für Fragen oder Fehlerberichte.

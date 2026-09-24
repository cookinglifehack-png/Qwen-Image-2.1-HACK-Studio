# Qwen-Image 2.1 HACK Studio

[English](README.md) | [日本語](README.ja.md) | [中文](README.zh.md) | [한국어](README.ko.md) | [Deutsch](README.de.md) | **Español** | [Bahasa Indonesia](README.id.md)

### ⬇️ [Descargar el instalador (Windows)](https://github.com/cookinglifehack-png/Qwen-Image-2.1-HACK-Studio/releases/latest/download/Qwen-Image_2.1_HACK_Studio_Setup.exe)

Una aplicación de Windows que permite usar el modelo de generación de imágenes **Qwen-Image 2.1**,
que se ejecuta sobre ComfyUI, nada más instalarla. Sin editar flujos de trabajo de ComfyUI y sin
conocimientos de PHP: haz doble clic para abrirla y genera texto a imagen y edición de imágenes
únicamente con controles de formulario al estilo del navegador.

> **Herramienta no oficial.** Esta aplicación es obra de un tercero independiente y no está
> afiliada al equipo de Qwen. **El modelo no está incluido** y no está cubierto por la licencia de
> este software: Qwen-Image 2.1 se distribuye bajo la **Qwen Research License (solo uso no comercial;
> el uso comercial requiere una licencia aparte del licenciante)**. Lee tú mismo los términos del modelo.

## Preparación (ComfyUI + modelos de Qwen-Image 2.1)

El instalador incluye la aplicación en sí, pero **no** ComfyUI ni los pesos del modelo Qwen-Image 2.1.
Necesitas una instancia de ComfyUI con los modelos instalados y registrarla como backend desde la
pantalla de Ajustes de la aplicación. Coloca los archivos siguientes en `ComfyUI/models/<carpeta>/`
(los nombres deben coincidir exactamente con los que espera la aplicación).

### ComfyUI

- Guía oficial de instalación (Windows portable): https://docs.comfy.org/installation/comfyui_portable_windows

### ① Mínimo (solo para ejecutar Qwen-Image 2.1)

Todo procede del repositorio oficial [`Comfy-Org/Qwen-Image-2.1`](https://huggingface.co/Comfy-Org/Qwen-Image-2.1).

| Función | Nombre de archivo | Tamaño | Carpeta de destino |
|---|---|---:|---|
| UNet (cuantizado int8) | `qwen_image_2.1_int8_convrot.safetensors` | 6,8GB | `models/diffusion_models/` |
| Codificador de texto (cuantizado int8) | `qwen3vl_8b_int8_convrot.safetensors` | 8,8GB | `models/text_encoders/` |
| VAE | `qwen_image_2.1_vae_bf16.safetensors` | 645MB | `models/vae/` |

Con esto funcionan tanto texto a imagen como edición de imágenes (configuración base, sin aceleración).

### ② Recomendado (configuración más rápida medida)

Como muestra el benchmark de abajo, se logra una **generación 2,1–2,2× más rápida en Full HD**
con solo una ligera diferencia de calidad. Añádelo sobre ①.

| Función | Origen | Ubicación / pasos |
|---|---|---|
| **Nodo de aceleración Spectrum** | https://github.com/awdqwdasdg/Comfyui-Spectrum-Qwen2.1 | clonar en `custom_nodes/` (`SpectrumQwenImage21`). Sin dependencias adicionales |
| **Nodo SageAttention** | https://github.com/kijai/ComfyUI-KJNodes | clonar en `custom_nodes/` (`PathchSageAttentionKJ`) |
| **SageAttention** (wheel para Windows) | https://github.com/woct0rdho/SageAttention/releases | `pip install triton-windows` y luego instalar el wheel que corresponda a tu versión de PyTorch/CUDA |
| Codificador de texto bf16 (opcional) | mismo repositorio oficial | `qwen3vl_8b_bf16.safetensors` (17GB) → `models/text_encoders/`. **Misma velocidad que int8**: úsalo si te sobra VRAM |

Tras instalar, **reinicia ComfyUI** y comprueba el estado en **Ajustes → Modelos** de esta aplicación.

### ③ Todo (otras variantes de cuantización)

| Función | Nombre de archivo | Tamaño | Uso |
|---|---|---:|---|
| UNet bf16 (precisión completa) | `qwen_image_2.1_bf16.safetensors` | 14GB | Sin cuantizar; prioriza la calidad |
| Codificador de texto w4a8 | `qwen3vl_8b_w4a8.safetensors` | 5,9GB | El más ligero; para VRAM justa |

Ambos están en `Comfy-Org/Qwen-Image-2.1` y van en las mismas carpetas que ① y ②
(`diffusion_models/` / `text_encoders/`).

## Ajustes recomendados

Se configuran en **Ajustes → Extras**, o con el botón «⚡ Extras» arriba a la derecha en la pantalla de generación.

| Elemento | Recomendado | Motivo |
|---|---|---|
| **Sampler** | `euler` + `simple` | Valor por defecto del flujo oficial de Qwen-Image 2.1 |
| **UNET** | `int8 (convrot)` | 6,8GB; prácticamente sin diferencia visible con bf16 |
| **Codificador de texto** | `bf16` si la VRAM lo permite, si no `int8 (convrot)` | **Casi sin diferencia de velocidad**: elige por VRAM, no por velocidad |
| **Spectrum** | **ON** | **~2,0× más rápido**: el mayor efecto. El detalle fino queda algo más suave |
| **SageAttention** | **ON** | Otro +5–10 %, más en resoluciones altas |

Por defecto: 25 pasos y CFG 1 (sube el CFG solo si usas un prompt negativo).

## Benchmark (medido)

Condiciones: **1920×1088 / UNet `int8_convrot` fijo / euler + simple / 25 pasos /
CFG 1 / semilla fija / texto a imagen**, en un backend ComfyUI local.

| Codificador de texto | Ninguno | Sage | **Spectrum** | **Sage + Spectrum** |
|---|---:|---:|---:|---:|
| **int8_convrot** (8,8GB) | 187,9s | 171,0s (1,10×) | 92,6s (2,03×) | **88,3s (2,13×)** |
| **bf16** (17GB) | 196,2s | 175,3s (1,12×) | 93,0s (2,11×) | **87,9s (2,23×)** |
| **w4a8** (5,9GB) | 191,7s | 176,0s (1,09×) | 96,9s (1,98×) | **90,7s (2,11×)** |

- Spectrum por sí solo da aproximadamente 2×, con cualquier codificador. Sage añade +9–12 % y se combina con él.
- La elección del codificador de texto no afecta de forma significativa a la velocidad (dentro del ruido de medición).
- Sage ayuda más a mayor resolución: 1,17× a 2720×1536, 1,08× a 1920×1088.

> **Sobre «Full HD»:** al pedir 1920×1080 se obtiene en realidad **1920×1088**, porque el modelo
> redondea el tamaño de imagen hacia arriba a un múltiplo de 16. Si necesitas exactamente 1080p, usa
> **Ajustes → Extras → Salida y sistema → Recortar tras generar** para quitar 8px arriba y abajo
> (hay preajustes 480/720/1080).

## Pantalla de generación: un único diseño sin adivinanzas

![Pantalla de generación](screenshots/generation.jpg)

Cambia entre texto a imagen y Edit (con imágenes de referencia) mediante pestañas. La cola de la
derecha muestra las imágenes terminadas en cuanto acaba cada trabajo, y al hacer clic en una miniatura
se abre un visor a ventana completa / pantalla completa. El tamaño y la relación de aspecto se eligen
con preajustes, y la resolución real que se enviará se muestra en directo.

## Extras: elige las opciones de aceleración con confianza

![Pantalla de Extras](screenshots/extra-features.jpg)

Sampler y scheduler (con el valor por defecto marcado), variante de UNET / codificador de texto,
Spectrum y SageAttention: el efecto de cada opción se explica justo al lado, y un informe de benchmark
plegable destaca la combinación recomendada. Extras también incluye un interruptor de **prevención de
suspensión** para trabajos largos (se reproduce periódicamente un sonido muy suave para mantener
Windows despierto) y una opción de **recorte**.

## Pestaña Modelos: ve qué está instalado

![Pantalla de Modelos](screenshots/models.jpg)

La pestaña Modelos comprueba qué archivos de modelo y nodos personalizados tiene realmente cada
ComfyUI conectado, para que veas de un vistazo qué falta.

## Consejo para el modo Edit

Qwen-Image 2.1 Edit puede ser poco fiable si solo se le da **una** imagen de referencia. La aplicación
completa automáticamente una única referencia con una imagen de relleno gris neutra para que se
comporte como en el caso de dos imágenes. Las imágenes de referencia de más de ~1 megapíxel se reducen
automáticamente (conservando la relación de aspecto).

## Para quién es

- Quien quiera usar Qwen-Image 2.1 solo con controles de formulario, sin tocar el grafo de nodos de ComfyUI
- Quien prefiera elegir cuantización y aceleración según cifras medidas y no por intuición
- Quien tenga varias máquinas/GPU con ComfyUI y quiera que los trabajos se reparta automáticamente a la menos ocupada

## Descarga

El instalador para Windows (autónomo, sin runtime adicional) está disponible en Releases.

Consulta [USAGE.md](USAGE.md) para un recorrido paso a paso con capturas de pantalla.

## Comentarios y preguntas

Usa [Issues](../../issues) para informes de errores, solicitudes de funciones o cualquier otra pregunta.

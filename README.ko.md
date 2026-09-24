# Qwen-Image 2.1 HACK Studio

[English](README.md) | [日本語](README.ja.md) | [中文](README.zh.md) | **한국어** | [Deutsch](README.de.md) | [Español](README.es.md) | [Bahasa Indonesia](README.id.md)

### ⬇️ [설치 프로그램 다운로드 (Windows)](https://github.com/cookinglifehack-png/Qwen-Image-2.1-HACK-Studio/releases/latest/download/Qwen-Image_2.1_HACK_Studio_Setup.exe)

ComfyUI에서 동작하는 이미지 생성 모델 **Qwen-Image 2.1**을 설치 즉시 사용할 수 있는
Windows 앱입니다. ComfyUI 워크플로 편집도, PHP 지식도 필요 없습니다. 더블클릭으로 실행하고,
브라우저 같은 폼 조작만으로 텍스트→이미지와 이미지 편집을 생성할 수 있습니다.

> **비공식 도구입니다.** 이 앱은 독립적인 제3자가 만든 것으로 Qwen 팀과 무관합니다.
> **모델은 포함되어 있지 않으며** 이 소프트웨어의 라이선스 대상이 아닙니다. Qwen-Image 2.1은
> **Qwen Research License(비상업적 사용에 한정, 상업적 사용은 배포처와 별도 라이선스 필요)**
> 로 배포됩니다. 모델의 이용 약관은 반드시 직접 확인해 주세요.

## 설정 (ComfyUI + Qwen-Image 2.1 모델)

설치 프로그램에는 앱 본체만 들어 있으며 ComfyUI와 Qwen-Image 2.1 모델 가중치는 **포함되지 않습니다**.
모델이 설치된 ComfyUI를 준비하고 앱의 설정 화면에서 백엔드로 등록하세요.
아래 파일을 `ComfyUI/models/<폴더>/`에 두세요(파일명은 앱이 기대하는 이름과 정확히 일치해야 합니다).

### ComfyUI 본체

- 공식 설치 가이드(Windows 포터블): https://docs.comfy.org/installation/comfyui_portable_windows

### ① 최소 구성 (Qwen-Image 2.1 실행만)

모두 공식 저장소 [`Comfy-Org/Qwen-Image-2.1`](https://huggingface.co/Comfy-Org/Qwen-Image-2.1)에 있습니다.

| 역할 | 파일명 | 크기 | 배치 폴더 |
|---|---|---:|---|
| UNet (int8 양자화) | `qwen_image_2.1_int8_convrot.safetensors` | 6.8GB | `models/diffusion_models/` |
| 텍스트 인코더 (int8 양자화) | `qwen3vl_8b_int8_convrot.safetensors` | 8.8GB | `models/text_encoders/` |
| VAE | `qwen_image_2.1_vae_bf16.safetensors` | 645MB | `models/vae/` |

이것만으로 텍스트→이미지와 이미지 편집이 모두 동작합니다(가속 없는 기본 구성).

### ② 권장 (실측 최고속 구성)

아래 벤치마크와 같이 Full HD에서 **2.1~2.2배 빨라지며** 화질 차이는 아주 작습니다.
①에 더해 설치하세요.

| 역할 | 출처 | 배치 / 절차 |
|---|---|---|
| **Spectrum 가속 노드** | https://github.com/awdqwdasdg/Comfyui-Spectrum-Qwen2.1 | `custom_nodes/`에 clone (`SpectrumQwenImage21`), 추가 의존성 없음 |
| **SageAttention 노드** | https://github.com/kijai/ComfyUI-KJNodes | `custom_nodes/`에 clone (`PathchSageAttentionKJ`) |
| **SageAttention 본체** (Windows wheel) | https://github.com/woct0rdho/SageAttention/releases | `pip install triton-windows` 후 PyTorch/CUDA 버전에 맞는 wheel 설치 |
| 텍스트 인코더 bf16 (선택) | 동일한 공식 저장소 | `qwen3vl_8b_bf16.safetensors`(17GB) → `models/text_encoders/`. **속도는 int8과 동일**하므로 VRAM 여유가 있으면 사용 |

설치 후 **ComfyUI를 재시작**하고, 이 앱의 **설정 → 모델**에서 설치 상태를 확인하세요.

### ③ 전부 (기타 양자화 버전)

| 역할 | 파일명 | 크기 | 용도 |
|---|---|---:|---|
| UNet bf16 (풀 정밀도) | `qwen_image_2.1_bf16.safetensors` | 14GB | 양자화 없음, 품질 우선 |
| 텍스트 인코더 w4a8 | `qwen3vl_8b_w4a8.safetensors` | 5.9GB | 가장 가벼움, VRAM이 빠듯한 환경용 |

둘 다 `Comfy-Org/Qwen-Image-2.1`에 있고 ①②와 같은 폴더(`diffusion_models/` / `text_encoders/`)에 둡니다.

## 권장 설정

**설정 → 추가 기능** 또는 생성 화면 오른쪽 위의 "⚡ 추가 기능" 버튼에서 설정합니다.

| 항목 | 권장 | 이유 |
|---|---|---|
| **샘플러** | `euler` + `simple` | Qwen-Image 2.1 공식 워크플로의 기본값 |
| **UNET** | `int8 (convrot)` | 6.8GB, bf16과 실사용상 거의 차이 없음 |
| **텍스트 인코더** | VRAM 여유 시 `bf16`, 아니면 `int8 (convrot)` | **속도 차이 거의 없음** — 속도가 아닌 VRAM으로 선택 |
| **Spectrum** | **ON** | **약 2.0배** — 가장 큰 효과. 세부 묘사가 약간 부드러워짐 |
| **SageAttention** | **ON** | 추가로 +5~10%, 해상도가 높을수록 효과 큼 |

기본값은 25 스텝, CFG 1입니다(네거티브 프롬프트를 쓸 때만 CFG를 올리세요).

## 벤치마크 (실측)

조건: **1920×1088 / UNet `int8_convrot` 고정 / euler + simple / 25 steps / CFG 1 /
seed 고정 / 텍스트→이미지**, 로컬 ComfyUI 백엔드에서 측정.

| 텍스트 인코더 | 없음 | Sage | **Spectrum** | **Sage + Spectrum** |
|---|---:|---:|---:|---:|
| **int8_convrot** (8.8GB) | 187.9s | 171.0s (1.10×) | 92.6s (2.03×) | **88.3s (2.13×)** |
| **bf16** (17GB) | 196.2s | 175.3s (1.12×) | 93.0s (2.11×) | **87.9s (2.23×)** |
| **w4a8** (5.9GB) | 191.7s | 176.0s (1.09×) | 96.9s (1.98×) | **90.7s (2.11×)** |

- Spectrum 단독으로 약 2배이며 어떤 인코더에서도 같은 배율입니다. Sage는 +9~12%로 함께 쓸 수 있습니다.
- 텍스트 인코더 선택은 속도에 실질적 영향이 없습니다(측정 오차 범위).
- 해상도가 높을수록 Sage가 더 효과적: 2720×1536에서 1.17×, 1920×1088에서 1.08×.

> **"Full HD"에 대해:** 1920×1080을 지정해도 실제 출력은 **1920×1088**입니다. 모델이 이미지
> 크기를 16의 배수로 올림하기 때문입니다. 정확히 1080p가 필요하면
> **설정 → 추가 기능 → 출력 및 시스템 → 생성 후 자르기**로 위아래 8px를 잘라내세요(480/720/1080 프리셋 제공).

## 생성 화면 — 헷갈리지 않는 하나의 레이아웃

![생성 화면](screenshots/generation.jpg)

탭으로 텍스트→이미지와 참조 이미지를 쓰는 편집(Edit)을 전환합니다. 오른쪽 큐에는 작업이
끝나는 즉시 이미지가 표시되고, 썸네일을 클릭하면 창 가득/전체 화면 뷰어가 열립니다.
크기와 종횡비는 프리셋으로 고르며 실제 제출될 해상도가 실시간으로 표시됩니다.

## 추가 기능 — 안심하고 가속 옵션 선택

![추가 기능 화면](screenshots/extra-features.jpg)

샘플러/스케줄러(기본값 표시), UNET/텍스트 인코더 종류, Spectrum, SageAttention —
각 옵션의 효과를 옆의 설명으로 확인하며 고를 수 있고, 접이식 벤치마크 리포트가 권장 조합을 보여 줍니다.
긴 작업용 **절전 방지**(Windows를 깨어 있게 하려고 아주 작은 소리를 주기적으로 재생)와 **자르기** 옵션도 여기에 있습니다.

## 모델 탭 — 무엇이 설치됐는지 한눈에

![모델 화면](screenshots/models.jpg)

연결된 각 ComfyUI에 모델 파일과 커스텀 노드가 실제로 있는지 확인해 표시하므로 부족한 것을 바로 알 수 있습니다.

## 편집(Edit) 모드 팁

참조 이미지가 **한 장**뿐이면 Qwen-Image 2.1 Edit가 불안정할 수 있습니다. 앱은 한 장일 때 중립적인
회색 채움 이미지를 자동으로 더해 두 장일 때와 같게 동작하도록 합니다. 약 100만 화소를 넘는 참조 이미지는
종횡비를 유지한 채 자동 축소됩니다.

## 이런 분께

- ComfyUI 노드 그래프를 건드리지 않고 폼 조작만으로 Qwen-Image 2.1을 쓰고 싶은 분
- 양자화·가속 옵션을 감이 아닌 실측 수치로 고르고 싶은 분
- 여러 대의 ComfyUI(머신/GPU)를 갖고 있어 가장 한가한 곳으로 자동 분배하고 싶은 분

## 다운로드

Windows 설치 프로그램(자체 완결형, 추가 런타임 불필요)은 Releases에서 받을 수 있습니다.

스크린샷이 있는 단계별 안내는 [USAGE.md](USAGE.md)를 참고하세요.

## 피드백 및 질문

버그 보고, 기능 요청, 기타 질문은 [Issues](../../issues)를 이용해 주세요.

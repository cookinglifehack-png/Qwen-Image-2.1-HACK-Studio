# Qwen-Image 2.1 HACK Studio

[English](README.md) | **日本語** | [中文](README.zh.md) | [한국어](README.ko.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Bahasa Indonesia](README.id.md)

### ⬇️ [インストーラーをダウンロード（Windows）](https://github.com/cookinglifehack-png/Qwen-Image-2.1-HACK-Studio/releases/latest/download/Qwen-Image_2.1_HACK_Studio_Setup.exe)

ComfyUI 上で動く画像生成モデル **Qwen-Image 2.1** を、インストールしてすぐ使える
Windows アプリです。ComfyUI のワークフロー編集も PHP の知識も不要。ダブルクリックで
起動し、テキスト→画像・画像編集をブラウザ風のフォーム操作だけで生成できます。

> **非公式ツールです。** 本アプリは Qwen チームとは無関係の第三者による制作物です。
> **モデルは同梱されておらず**、本ソフトウェアのライセンスの対象外です。Qwen-Image 2.1 は
> **Qwen Research License（非商用のみ。商用利用には配布元との別途ライセンスが必要）**
> で配布されています。モデルの利用条件は必ずご自身でご確認ください。

## セットアップ（ComfyUI + Qwen-Image 2.1 モデル）

インストーラーにはアプリ本体のみが含まれ、ComfyUI と Qwen-Image 2.1 のモデルは**含まれません**。
モデルを導入済みの ComfyUI を用意し、アプリの設定画面からバックエンドとして登録してください。
下記のファイルを `ComfyUI/models/<フォルダ>/` に置きます（ファイル名はアプリの想定と
完全一致させてください）。

### ComfyUI 本体

- 公式インストールガイド（Windows ポータブル版）: https://docs.comfy.org/installation/comfyui_portable_windows

### ① 最低限（Qwen-Image 2.1 を動かすだけ）

すべて公式リポジトリ [`Comfy-Org/Qwen-Image-2.1`](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) にあります。

| 役割 | ファイル名 | サイズ | 配置先フォルダ |
|---|---|---:|---|
| UNet（int8 量子化） | `qwen_image_2.1_int8_convrot.safetensors` | 6.8GB | `models/diffusion_models/` |
| テキストエンコーダ（int8 量子化） | `qwen3vl_8b_int8_convrot.safetensors` | 8.8GB | `models/text_encoders/` |
| VAE | `qwen_image_2.1_vae_bf16.safetensors` | 645MB | `models/vae/` |

これだけでテキスト→画像・画像編集の両方が動きます（高速化なしの基本構成）。

### ② オススメ（実測最速構成）

下のベンチマークのとおり、**FullHD で 2.1〜2.2 倍速**になり、画質の低下はごくわずかです。
①に加えて導入してください。

| 役割 | 入手先 | 配置先 / 手順 |
|---|---|---|
| **Spectrum 高速化ノード** | https://github.com/awdqwdasdg/Comfyui-Spectrum-Qwen2.1 | `custom_nodes/` に clone（`SpectrumQwenImage21`）。追加依存なし |
| **SageAttention ノード** | https://github.com/kijai/ComfyUI-KJNodes | `custom_nodes/` に clone（`PathchSageAttentionKJ`） |
| **SageAttention 本体**（Windows wheel） | https://github.com/woct0rdho/SageAttention/releases | `pip install triton-windows` 後、PyTorch/CUDA 版に合う wheel を pip install |
| テキストエンコーダ bf16（任意） | 同じ公式リポジトリ | `qwen3vl_8b_bf16.safetensors`（17GB）→ `models/text_encoders/`。**速度は int8 と同等**なので VRAM に余裕があればこちら |

導入後は **ComfyUI を再起動**し、このアプリの **設定 → モデル** で導入状況を確認できます。

### ③ 全部（他の量子化バリエーション）

| 役割 | ファイル名 | サイズ | 用途 |
|---|---|---:|---|
| UNet bf16（フル精度） | `qwen_image_2.1_bf16.safetensors` | 14GB | 量子化なし。品質優先 |
| テキストエンコーダ w4a8 | `qwen3vl_8b_w4a8.safetensors` | 5.9GB | いちばん軽い。VRAM が厳しい環境向け |

どちらも `Comfy-Org/Qwen-Image-2.1` にあり、配置先は①②と同じです
（`diffusion_models/` / `text_encoders/`）。

## オススメ設定

**設定 → 追加機能**、または生成画面右上の「⚡ 追加機能」ボタンから設定します。

| 項目 | オススメ | 理由 |
|---|---|---|
| **サンプラー** | `euler` ＋ `simple` | Qwen-Image 2.1 公式ワークフローの既定 |
| **UNET** | `int8 (convrot)` | 6.8GB で bf16 と実用上そん色なし |
| **テキストエンコーダ** | VRAM に余裕があれば `bf16`、標準は `int8 (convrot)` | **速度差はほぼゼロ**。選択基準は速度ではなく VRAM |
| **Spectrum** | **ON** | **約 2.0 倍**。いちばん効く。細部がごくわずかに柔らかくなる |
| **SageAttention** | **ON** | さらに +5〜10%。高解像度ほど効く |

ステップ数は既定の 25、CFG は 1 です（CFG を上げるのはネガティブプロンプトを使う時だけ）。

## ベンチマーク（実測）

条件: **1920×1088 / UNet `int8_convrot` 固定 / euler + simple / 25 steps / CFG 1 /
seed 固定 / テキスト→画像**、ローカルの ComfyUI バックエンドで測定。

| テキストエンコーダ | なし | Sage | **Spectrum** | **Sage + Spectrum** |
|---|---:|---:|---:|---:|
| **int8_convrot**（8.8GB） | 187.9s | 171.0s (1.10×) | 92.6s (2.03×) | **88.3s (2.13×)** |
| **bf16**（17GB） | 196.2s | 175.3s (1.12×) | 93.0s (2.11×) | **87.9s (2.23×)** |
| **w4a8**（5.9GB） | 191.7s | 176.0s (1.09×) | 96.9s (1.98×) | **90.7s (2.11×)** |

- Spectrum 単体で約 2 倍。どのエンコーダでも同じ倍率で効きます。Sage は +9〜12% で、併用できます。
- エンコーダの違いによる速度差は測定誤差の範囲です。
- Sage は高解像度ほど効きます（2720×1536 で 1.17×、1920×1088 で 1.08×）。

> **「FullHD」について:** 1920×1080 を指定しても、実際の出力は **1920×1088** です。モデルが
> 画像サイズを 16 の倍数へ切り上げるためです。1080P ぴったりが必要な場合は、
> **設定 → 追加機能 → 出力とシステム → 生成後のトリミング** で上下 8px を切り落として
> ください（480/720/1080 のプリセットあり）。

## 生成画面 — 迷わない、ひとつの画面

![生成画面](screenshots/generation.jpg)

テキスト→画像と、参照画像つきの画像編集（Edit）をタブで切り替えます。右側のキューには
完了した画像がその場で表示され、サムネイルをクリックするとウィンドウいっぱい／
フルスクリーンのビューワが開きます。サイズとアスペクト比はプリセットで選べ、実際に
送信されるピクセル数がリアルタイムで表示されます。

## 追加機能 — 高速化オプションを納得して選ぶ

![追加機能画面](screenshots/extra-features.jpg)

サンプラー／スケジューラ（既定値の表記つき）、UNET・テキストエンコーダの種類、
Spectrum、SageAttention など、各オプションの効果を横の説明で確認しながら選べます。
折りたたみのベンチマークレポートで、オススメの組み合わせも一目で分かります。
長時間の生成向けの**スリープ回避**（Windows のスリープ防止のため、ごく小さな音を
定期的に鳴らします）と、**トリミング**の設定もここにあります。

## モデルタブ — 何が導入済みかひと目で

![モデル画面](screenshots/models.jpg)

接続中の各 ComfyUI に、モデルファイルとカスタムノードが実際に入っているかを
チェックして表示します。足りないものがすぐ分かります。

## 画像編集（Edit）のコツ

Qwen-Image 2.1 の編集は、参照画像が **1枚** だと効きにくいことがあります。本アプリは
1枚のとき、無彩色のグレーのダミー画像を自動で足して、2枚のときと同じ挙動にします。
約 100 万画素を超える参照画像は、アスペクト比を保ったまま自動で縮小されます。

## こんな人向け

- ComfyUI のノードグラフを触らず、フォーム操作だけで Qwen-Image 2.1 を使いたい人
- 量子化や高速化のオプションを、勘ではなく実測値で選びたい人
- 複数の ComfyUI（マシン／GPU）を持ち、空いているところへ自動で振り分けたい人

## ダウンロード

Windows 用インストーラー（自己完結型、追加ランタイム不要）は Releases から入手できます。

画面つきの手順は [USAGE.ja.md](USAGE.ja.md) を参照してください。

## フィードバック・質問

不具合報告・機能要望・ご質問は [Issues](../../issues) へお願いします。

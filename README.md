# MiniMax H3 Studio

[Download the workflow ZIP](https://github.com/BiggerFishy/comfyui-minimax-h3-studio/releases/latest/download/MiniMax-H3-Studio.zip) · [Latest release](https://github.com/BiggerFishy/comfyui-minimax-h3-studio/releases/latest)

A local ComfyUI workflow for video inpainting and character replacement, with a preconfigured Barn Owl example. Version **1.0.3** fixes source-video loading by reading prepared frames sequentially in one decoder process. It retains the native PyTorch attention profile, Windows CPU weight reader, LoRA controls, trim popup, and layout with five main nodes and three removable mode notes.

Use **MiniMax-H3-Studio.zip** linked above. It includes the workflow, custom nodes, launchers, and demo media. GitHub's automatically generated Source code archives are not the installation package.

## Included

- Organized Studio controls with a restored video-trim popup, reference images, adjustable quality, and video mask previews.
- Sequential source-video loading with exact frame-count checks and errors that report missing frame counts and FFmpeg details.
- SAM3, rectangle, or full-frame editing, plus a preserve selection and reusable saved characters.
- Publisher links and individual model download buttons. Model weights are downloaded separately.
- The Barn Owl portrait, source clip, and saved character. The example opens in Video inpainting with the supplied character and scene prompts and SAM3 target **“the man”**. It processes the first **3 seconds**, at **0.8 MP**, with fixed seed **688636457668368**.
- Separate reference-to-video and first-to-last-frame modes, each with its own empty media selections. These two modes are **experimental and may not work well**.
- A Windows launcher and a Python launcher for the stable loading profile.
- LoRA controls in the green settings panel. Turbo V4 Step 600 pruned is the sole default LoRA for reference-to-video and video inpainting; the endpoint mode keeps its own matching Turbo.

Each of the three mode notes is separate and can be deleted. Save Character stores the reference image and character description together for reuse. Choose **Seed behavior > Randomize** to try variations; results can take several attempts. A fixed seed helps compare runs with the same environment and settings, but does not guarantee identical results across different hardware or software.

## Setup

1. Use a current ComfyUI build with native MiniMax H3, SAM3.1, subgraphs, and the `--disable-fast-disk` and `--use-pytorch-cross-attention` startup options. A compatible PyTorch/CUDA environment is required. The INT8 ConvRot models' publisher recommends PyTorch with CUDA 13.0; keep ComfyUI's own requirements current and follow the relevant model's requirements.
2. Extract the ZIP and **merge the contents of its `ComfyUI` folder into your existing ComfyUI folder**. Update both `ComfyUI-H3-Studio` and `ComfyUI-H3-Studio-Support` from the same release; both must be inside `custom_nodes`. The files `Start-MiniMax-H3-Studio.bat` and `h3_studio_start.py` must sit **beside ComfyUI's `main.py`**, not inside another nested `ComfyUI` folder.
3. Install **both** `custom_nodes/ComfyUI-H3-Studio/requirements.txt` and `custom_nodes/ComfyUI-H3-Studio-Support/requirements.txt` using the Python environment that runs ComfyUI. The included Studio requirements specify **safetensors 0.8.0 or newer**. Ensure **both FFmpeg and ffprobe** are available on PATH. [FFmpeg's download page](https://ffmpeg.org/download.html) lists platform packages. The `imageio-ffmpeg` fallback supplies FFmpeg only, not ffprobe.
4. Close the existing ComfyUI server, then start the included stable launcher as described below. A fresh server process is required for startup settings to take effect; reopening the browser alone does not change them.
5. Open **MiniMax H3 Studio** from ComfyUI's Workflows list, or drag `MiniMax H3 Studio.json` from the archive into ComfyUI.
6. Use the Models node's mode filter to see the weights needed for your selected mode. Check the publisher links and model terms, then use the per-model download buttons. Nothing downloads merely from opening the workflow or pressing Run. Downloads check SHA-256 before installation and never replace an existing model file.

For Windows portable ComfyUI, run these dependency commands from the ComfyUI folder:

```bat
..\python_embeded\python.exe -s -m pip install -r custom_nodes\ComfyUI-H3-Studio\requirements.txt
..\python_embeded\python.exe -s -m pip install -r custom_nodes\ComfyUI-H3-Studio-Support\requirements.txt
```

For a virtual environment, activate the environment used by ComfyUI and run:

```sh
python -m pip install -r custom_nodes/ComfyUI-H3-Studio/requirements.txt
python -m pip install -r custom_nodes/ComfyUI-H3-Studio-Support/requirements.txt
```

Restart the ComfyUI server after installing or updating these requirements. If Studio's runtime check reports an older safetensors version or an incompatible startup profile, install the requirements in the same Python environment that runs the server and restart with the included launcher.

Model weights, ComfyUI, PyTorch/CUDA, GPU drivers, and external media tools are not bundled. The supplied custom nodes and launchers do not patch ComfyUI core files, rewrite model files, or change GPU drivers or system settings. Model terms are linked on each publisher's page.

## Start with the stable profile

**Windows:** open `Start-MiniMax-H3-Studio.bat` from the ComfyUI folder. It looks for ComfyUI's Python in this order: `../python_embeded/python.exe`, `.venv/Scripts/python.exe`, then `venv/Scripts/python.exe`. It does not fall back to an unrelated Python installation. For another environment layout, activate your ComfyUI environment and run `python h3_studio_start.py`.

**Linux or macOS:** activate the Python environment used by ComfyUI, open the ComfyUI folder, and run:

```sh
python h3_studio_start.py
```

The launcher requires `--disable-fast-disk` and `--use-pytorch-cross-attention`, selecting ComfyUI's native PyTorch attention while **keeping Dynamic VRAM memory management available on supported GPU configurations**. It also adds `--disable-triton-backend`, `--disable-async-offload`, `--disable-comfy-compiler`, and `--disable-pinned-memory` when the installed ComfyUI declares those options. Disabling pinned memory limits additional pinned buffers during CPU weight loading and can reduce RAM pressure. Missing optional settings are reported. If either required option is unavailable, the launcher stops with an explanation. SageAttention is not required.

An internal **H3 Studio Attention Safety** node also selects PyTorch attention for the H3 model in video inpainting, reference-to-video, and first-to-last-frame mode. Studio adds it to the execution graph when a mode runs, so the five main nodes and their controls stay unchanged. Use the included launcher as well: the model attention node does not configure the server's other backends or recover a failed CUDA session.

On Windows, loading the Studio custom-node pack at server startup installs a **low-copy mapped CPU reader** in ComfyUI's safetensors loading utility. It validates the file's tensor metadata and uses a read-only file mapping to expose CPU tensors without allocating another full copy of the model. The reader supplies ordinary CPU tensors without direct-file transfer descriptors, so transfers use the RAM-copy path while retaining dynamic GPU allocation. Together with the startup profile, this bypasses the direct aimdo disk-to-GPU reader and PyTorch storage slicing involved in the reported failures.

This reader remains active for the lifetime of that ComfyUI server process. Other CPU model loads that use the same ComfyUI utility also use it. The change replaces the utility's reader reference in memory; it does not replace the installed safetensors package, edit ComfyUI core files, or rewrite model weights. A new server installs the reader again when it loads the Studio pack.

Ordinary arguments, such as `--port 8189` and `--listen 127.0.0.1`, can follow either launcher command. The launcher rejects options that conflict with this profile, including Dynamic VRAM disabling, fast-disk or asynchronous transfers, Sage/Flash/split/quad/ComfyKitchen attention, and Triton enabling. Abbreviated conflicting flags are rejected too. Close any existing ComfyUI server before starting another one.

This profile preserves ComfyUI's dynamic memory management while removing the direct disk-to-GPU transfer path for weights loaded through the custom CPU reader. RAM use and load times still depend on the model, settings, and available memory; ordinary RAM transfers may be slower than fast-disk transfers. The profile does not guarantee that every model, setting, or device will fit or run successfully. After a CUDA illegal-memory-access failure, close the failed server process before starting a fresh one. The launcher does not attempt to continue generation inside a failed GPU session.

Two upstream reports explain the additional backend choices. [SageAttention issue #386](https://github.com/thu-ml/SageAttention/issues/386) reports integer-index overflow with large, strided attention tensors. [ComfyKitchen issue #136](https://github.com/Comfy-Org/comfy-kitchen/issues/136) reports a separate integer-index overflow in Triton INT8 matrix multiplication for large MiniMax H3 workloads. These are distinct reported failure paths; they do not establish the cause of every CUDA error. A later weight-copy or cleanup synchronization error can be where an earlier asynchronous GPU failure becomes visible.

## LoRAs and trimming

The green settings panel contains the restored LoRA controls. The shared workflow starts with only `minimax_h3_turbo_v4_step600_pruned_comfyui.safetensors` enabled at strength **1.0** for reference-to-video and video inpainting. Other LoRA rows are empty. There is no hidden reference Turbo 8-step LoRA applied underneath the visible stack.

The Models node on the left includes the exact non-EMA **Turbo V4 Step 600 pruned** publisher link and a verified download button. The reference Turbo 8-step file remains an optional alternative: select it **instead of** V4, not alongside it. The experimental first-to-last-frame mode uses its own matching `minimax_h3_fl2v_turbo_8step_v1.0_768p_comfyui_bf16.safetensors`; download that file only if using the endpoint mode.

The [Turbo V4 publisher](https://huggingface.co/drbaph/MiniMax-H3-Turbo-Lora-ComfyUI) describes this file as a partial compatibility conversion with **51 incompatible AdaLN adapter pairs removed**. It should not be assumed to reproduce the original full-model LoRA's behavior. Additional LoRAs must be compatible with the selected MiniMax H3 model.

Use the restored trim popup to set the source-video range. The bundled example remains set to the first **3 seconds**; its resolution and seed remain **0.8 MP** and **688636457668368**.

## Support packs and saved characters

The portable support pack supplies media loading, mask caching, memory settings, and saved-character storage. No external SAM3 plugin or Qwen captioner custom-node pack is required. The workflow uses the native SAM3.1 checkpoint and the H3 conditioning encoder listed in its Models node.

Studio uses its own internal video-preparation node from the portable support pack, so an older **ComfyUI-MiniMax-Safe** installation cannot select an older decoder for Studio. If that larger pack is present, it remains authoritative for the shared masks, memory settings, and character-library routes. The portable pack avoids registering duplicates for those shared names. Do not install a partial or unrelated folder under the MiniMax-Safe name.

The file loader first prepares the selected trim at 24 fps and the existing analysis size, then reads its frames sequentially in batches of eight. This removes the repeated timestamp seeks that could return an incomplete section near a chunk boundary. The final IMAGE tensor still contains the whole prepared clip; this update bounds the extra decoder buffers and does not remove that final allocation. Audio retains the selected trim and prepared-video duration. Missing, partial, or extra frames and FFmpeg failures produce an error instead of filling or dropping frames silently. Restart ComfyUI after updating both packs.

Saved characters are stored locally in `ComfyUI/user/default/h3_character_presets`, with durable images under `ComfyUI/input/Character_Presets`. Only the supplied Barn Owl saved character and two demo media files are bundled. Other saved characters from the creator's computer are not included. Review those local folders before sharing your own installation or character library. The support pack does not download tools or models automatically.

## Validation

On **October 3, 2026**, all **19 CPU decoder tests** passed using synthetic media only. They covered a 284-frame clip decoded with one process into 36 batches, exact frame order and window boundaries, a nonzero trim with audio, partial final batches, silence for a source without audio, missing/partial/extra frame errors, late FFmpeg failures, fragmented pipe reads, cancellation, and process/staging cleanup. All **3 isolated support-routing checks** also passed: Studio selects its own decoder with or without the older MiniMax-Safe pack, shared routes avoid duplication, and a missing updated support pack produces an actionable error. These checks opened no personal media or model weights.

Version 1.0.3 changes video preparation and decoder routing. No new GPU generation was run for this patch. The GPU results below are historical evidence for earlier versions and backend settings; they do not establish new v1.0.3 generation results or visual quality.

### Historical GPU results

The PyTorch-attention backend settings completed two video-inpainting diagnostic runs on **October 2, 2026**: **15 seconds at 0.55 MP** in about **29m 10s**, then **3 seconds at 0.8 MP** in about **4m 45s** after memory cleanup in the same server process. Both used **`CUDA_LAUNCH_BLOCKING=1`**; these are diagnostic timings, **not normal generation-speed measurements**. The outputs contained 360 frames at 544 × 992 and 72 frames at 672 × 1184, respectively, both at 24 fps with audio. Full output decoding passed. These checks preceded installation of the internal attention safety node and validate the backend settings only.

The exact installed **v1.0.2** package then completed a normal-launcher check on a fresh server: **3 seconds at 0.8 MP**, **8 steps**, in **4m 14s**, with CUDA launch blocking disabled. The runtime log confirmed that the internal H3 attention safety node selected PyTorch attention. The output contained **72 frames at 672 × 1184**, **24 fps**, with audio; full decoding passed. These October 2 checks ran on Windows with an RTX 4090 with 24 GB VRAM and 32 GB system RAM.

Historical checks on **October 1, 2026**, used the previous SageAttention profile: the supplied **0.8 MP**, **8-step** demo completed at **3 seconds** in **2m 49s** and **8 seconds** in **9m 51s**, using the fixed example seed, INT8 ConvRot reference model, and Turbo V4 Step 600 pruned LoRA. Output decoding passed. That machine used Windows, an RTX 4090 with 24 GB VRAM, 32 GB RAM, a roughly 31 GB pagefile, ComfyUI 0.37.0, PyTorch 2.9.1+cu130, and safetensors 0.8.0. Those earlier runs validate only that earlier profile and are **not evidence that v1.0.2 or longer repeated runs passed**.

Execution and file-integrity checks do not establish visual quality or support for arbitrary lengths, other hardware, or the experimental modes. Historical normal-launcher validation covers the three-second v1.0.2 case; the historical fifteen-second and post-cleanup checks used diagnostic synchronization.

No generated demo output is included in the installation archive.

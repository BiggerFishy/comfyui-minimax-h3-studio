# MiniMax H3 Studio — Barn Owl Edition

[Download the workflow ZIP](https://github.com/BiggerFishy/comfyui-minimax-h3-studio/releases/download/v1.0.0/MiniMax-H3-Studio-Barn-Owl-Edition.zip) · [Release page](https://github.com/BiggerFishy/comfyui-minimax-h3-studio/releases/tag/v1.0.0)

A local ComfyUI workflow for video inpainting and character replacement, with a ready-to-run Barn Owl example.

Use the workflow ZIP linked above. It includes the workflow, custom nodes, and demo media. GitHub's automatically generated Source code archives are not the installation package.

## Included

- Organized Studio controls with video trimming, reference images, adjustable quality, and video mask previews.
- SAM3, rectangle, or full-frame editing, plus a preserve selection and reusable saved characters.
- Model links and download buttons. Model weights are downloaded separately.
- The Barn Owl portrait, source clip, and saved character. The example processes the first **3 seconds**, at **0.8 MP**, with fixed seed **688636457668368**.
- Separate reference-to-video and first-to-last-frame modes. These two modes are **experimental and may not work well**.

## Setup

1. Extract the ZIP. Use a current ComfyUI build with native MiniMax H3, SAM3.1, and subgraph support.
2. Merge the included `ComfyUI` folder into your ComfyUI folder; both included custom-node folders are needed.
3. Install `custom_nodes/ComfyUI-H3-Studio-Support/requirements.txt` using your ComfyUI Python. Ensure FFmpeg and ffprobe are available, then restart ComfyUI. See the included `README.txt` for details.
4. Open the Barn Owl workflow, or drag its JSON into ComfyUI.
5. Download the required models using the Models node, then run the example.

The fixed seed makes the example repeatable. Choose **Seed behavior > Randomize** to try variations; results can take several attempts. Only the Barn Owl saved character is bundled.

Model weights, ComfyUI, and GPU drivers are not included. Model terms are linked on the models' publisher pages.

ZIP SHA-256: `e95f1dbeacbe74fe80cd712249cce389db89b49b60f488deff626ae156d66604`

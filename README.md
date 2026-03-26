# Image Enhancement Pipeline (BasicSR + ESRGAN + Real-ESRGAN)

A practical, notebook-first image enhancement project built on top of BasicSR.

This project takes the original demo-style ESRGAN notebook and turns it into a streamlined, reusable pipeline for:
- Single-image enhancement
- Multi-model visual comparison
- Colab/Kaggle-friendly execution
- GPU-first inference with CPU fallback

## Table of Contents
1. Project Goals
2. What Is Implemented
3. Repository Structure
4. Models Included
5. End-to-End Workflow
6. Quick Start (Colab/Kaggle)
7. Notebook Cell Guide
8. Output Artifacts
9. Troubleshooting
10. Current Limitations
11. Roadmap
12. Reporting and Presentation Docs

## Project Goals
- Convert a long research/demo notebook into a clean production-style inference flow.
- Make enhancement easy for one input image with minimal setup friction.
- Support comparing multiple models on the same input for quality evaluation.
- Keep the solution runnable in hosted notebook environments.

## What Is Implemented

### 1) Streamlined Single-Image Pipeline
Implemented in:
- multimodel_single_image_enhancement_pipeline.ipynb

Features:
- Clones BasicSR at runtime (for ephemeral notebook environments)
- Installs dependencies and sets up BasicSR package
- Accepts one image via upload or direct path
- Runs super-resolution inference
- Saves output image
- Shows side-by-side preview

### 2) Multi-Model Comparison (Implementation 1)
Added model comparison in the same notebook:
- ESRGAN
- RealESRGAN_x4plus
- RealESRGAN_general_x4v3

Features:
- Config-driven model selection (`SELECTED_MODELS`)
- Registry-based model definitions (`MODEL_CONFIGS`)
- Mixed architecture support:
  - RRDBNet
  - SRVGGNetCompact
- Single inference pass per selected model
- Unified visual comparison panel

### 3) Download Reliability Hardening
Observed issue:
- Direct release URL failures (HTTP 404) for some model weights

Fixes:
- ESRGAN downloads via BasicSR official downloader script
- RealESRGAN_x4plus downloads from a single validated release URL
- RealESRGAN_general_x4v3 downloads from explicit stable URL

## Repository Structure

Top-level files in this workspace:
- multimodel_single_image_enhancement_pipeline.ipynb: Main runnable enhancement notebook
- PROJECT_UPDATE_AND_MANAGER_BRIEF.md: Delivery summary and manager presentation notes
- plan.md: Implementation roadmap (if present in your branch/history)
- BasicSR/: Upstream framework cloned in runtime and available in workspace

## Models Included

### ESRGAN
- Type: RRDB-based super-resolution model
- Use case: strong perceptual SR baseline
- Weight path:
  - experiments/pretrained_models/ESRGAN/ESRGAN_SRx4_DF2KOST_official-ff704c30.pth

### RealESRGAN_x4plus
- Type: RRDB-based real image restoration model
- Use case: robust real-world image enhancement
- Weight path:
  - experiments/pretrained_models/RealESRGAN/RealESRGAN_x4plus.pth

### RealESRGAN_general_x4v3
- Type: SRVGG-based compact model
- Use case: efficient general restoration
- Weight path:
  - experiments/pretrained_models/RealESRGAN/realesr-general-x4v3.pth

## End-to-End Workflow
1. Clone BasicSR in notebook runtime.
2. Install Python dependencies.
3. Download model weights.
4. Choose input mode:
- Upload one image (Colab)
- Provide direct file path (Kaggle/local)
5. Select one or more models.
6. Run inference for selected models.
7. Save outputs in a results directory.
8. Visualize input and all outputs side by side.

## Quick Start (Colab/Kaggle)

<a target="_blank" href="https://github.com/akashshah3/image-super-resolution/blob/master/multimodel_single_image_enhancement_pipeline.ipynb">
  <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>

### Step 1: Open the notebook
- multimodel_single_image_enhancement_pipeline.ipynb

### Step 2: Run setup cells in order
- Clone repo cell
- Install dependencies cell
- Download model weights cell

### Step 3: Configure input and models
In the configuration cell:
- Set `USE_UPLOAD = True` for Colab upload flow
- Or set `USE_UPLOAD = False` and define `INPUT_IMAGE_PATH`
- Set `SELECTED_MODELS` to the models you want to compare

Example:
```python
SELECTED_MODELS = ['ESRGAN', 'RealESRGAN_x4plus', 'RealESRGAN_general_x4v3']
```

### Step 4: Run inference and visualize
- Run inference cell to generate outputs
- Run visualization cell to compare quality

## Notebook Cell Guide

Typical sequence in multimodel_single_image_enhancement_pipeline.ipynb:
1. Introduction markdown
2. Runtime clone/setup
3. Dependency install
4. Model weight download
5. Input mode explanation
6. Config (`USE_UPLOAD`, `INPUT_IMAGE_PATH`, `SELECTED_MODELS`, `MODEL_CONFIGS`)
7. Input acquisition (upload/path)
8. Multi-model inference
9. Side-by-side comparison display

## Output Artifacts

Default output directory:
- results/compare

Naming convention:
- <input_stem>_<model_name>.png

Examples:
- photo_ESRGAN.png
- photo_RealESRGAN_x4plus.png
- photo_RealESRGAN_general_x4v3.png

## Troubleshooting

### 1) HTTP 404 while downloading weights
- Re-run the download cell (it includes fallback handling).
- If a model still fails, temporarily remove it from `SELECTED_MODELS`.

### 2) CUDA not available
- Notebook automatically falls back to CPU.
- Expect slower inference on CPU.

### 3) Import/module errors
- Ensure setup cells were run in order from top.
- Re-run dependency and setup cells after runtime restart.

### 4) Input image not found
- For direct path mode, verify `INPUT_IMAGE_PATH` exactly.
- For upload mode, rerun upload cell and confirm the printed path.

### 5) Model architecture mismatch
- Ensure model is mapped to the correct architecture in `MODEL_CONFIGS`:
  - RRDB models -> `arch: 'rrdb'`
  - realesr-general-x4v3 -> `arch: 'srvgg'`

## Current Limitations
- Single-image mode only (no folder batch mode yet).
- Visual comparison is qualitative (no metric table yet).
- Download step depends on external network access.

## Roadmap

### Next major item
- Optional denoising preprocessing before super-resolution (Implementation 2).

### Planned improvements
1. Denoise toggle and strength controls.
2. Before/after denoise preview.
3. Optional quality metrics (PSNR/SSIM when GT is available).
4. Runtime and performance summary per model.
5. Optional export cell for zipped outputs.

## Credits
- BasicSR framework by xPixelGroup and contributors
- ESRGAN and Real-ESRGAN model families by the original authors/contributors

## License Note
This project uses the upstream BasicSR codebase and pretrained models. Refer to the respective upstream repository license files for usage and redistribution conditions.

# Image Enhancement Pipeline

A notebook-first, single-image enhancement workflow built on BasicSR with optional denoising and side-by-side multi-model comparison.

Primary entry point:
- multimodel_single_image_enhancement_pipeline.ipynb

## What This Project Does

- Enhances one input image using one or more super-resolution models.
- Optionally denoises the input before enhancement.
- Saves all outputs to a unified results directory.
- Visualizes input, denoised image (if enabled), and all model outputs.
- Runs in Colab, Kaggle, or local Jupyter.

## Models Included

Denoising models:
- SwinIR color denoising
  - SwinIR_color_dn_noise15
  - SwinIR_color_dn_noise25
  - SwinIR_color_dn_noise50
- RIDNet (optional; requires compatible checkpoint)

Enhancement models:
- ESRGAN
- RealESRGAN_x4plus
- RealESRGAN_general_x4v3

## Refactored Notebook Architecture

The notebook is organized as clear stages:

1. Shared utilities and runtime detection
- Logging setup
- Runtime context detection (Colab/Kaggle/local)
- Command execution helpers

2. Dependency setup
- Installs PyTorch, BasicSR requirements, and BasicSR editable package

3. Download manager
- Downloads model checkpoints with retry behavior
- Uses a centralized model download registry

4. Typed configuration
- Defines ModelConfig and PipelineConfig
- Validates selected denoise/enhancement models

5. Checkpoint diagnostics (optional)
- Inspects checkpoint key structure
- Gives RIDNet compatibility hints

6. Input resolution
- Upload mode for Colab
- Direct path mode for Kaggle/local

7. Inference pipeline
- Modular classes:
  - ImageProcessor
  - ModelRegistry
  - InferenceEngine
  - EnhancementPipeline
- Produces a single result object: PIPELINE_RESULT

8. Visualization
- Renders side-by-side comparison from PIPELINE_RESULT

9. Validation
- Smoke checks output files and result schema

## Quick Start

## 1) Open the notebook
- multimodel_single_image_enhancement_pipeline.ipynb
<a target="_blank" href="https://colab.research.google.com/github/akashshah3/image-super-resolution/blob/master/multimodel_single_image_enhancement_pipeline.ipynb">
  <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>

## 2) Run from top to bottom once
Run all cells in order to ensure dependencies and model files are available.

## 3) Update user settings in the configuration cell
Change fields in PIPELINE_CONFIG:
- use_upload
- input_image_path
- enable_denoising
- denoise_model_name
- selected_models

Typical examples:

```python
# Colab upload flow
PIPELINE_CONFIG.use_upload = True
PIPELINE_CONFIG.input_image_path = ''

# Local/Kaggle file path flow
PIPELINE_CONFIG.use_upload = False
PIPELINE_CONFIG.input_image_path = 'datasets/upload/sample.png'

# Denoising + comparison models
PIPELINE_CONFIG.enable_denoising = True
PIPELINE_CONFIG.denoise_model_name = 'SwinIR_color_dn_noise25'
PIPELINE_CONFIG.selected_models = ['ESRGAN', 'RealESRGAN_x4plus', 'RealESRGAN_general_x4v3']
```

## 4) Run input -> inference -> visualization -> validation
- Resolve input path
- Run modular pipeline
- Inspect visual comparison
- Confirm smoke checks pass

## Output Artifacts

Default output directory:
- results/compare

Output files:
- <input_stem>_<model_name>.png
- <input_stem>_<denoise_model>_denoised.png (if denoising enabled)

Runtime result object:
- PIPELINE_RESULT['input_path']
- PIPELINE_RESULT['denoised_path']
- PIPELINE_RESULT['enhancements']
- PIPELINE_RESULT['metadata']

## How To Add a New Model

If the architecture already exists in ModelRegistry.build_model:
1. Add a new ModelConfig entry under AVAILABLE_MODEL_CONFIGS.
2. Add the new model name to PIPELINE_CONFIG.selected_models.

If the architecture is new:
1. Add the new ModelConfig entry.
2. Extend ModelRegistry.build_model with a builder branch for the new arch.
3. Keep state_key_priority aligned with the checkpoint format.

## Environment Notes

Colab:
- Upload mode works via google.colab.files.upload().

Kaggle/local:
- Prefer direct path mode with use_upload = False.

GPU/CPU:
- Pipeline uses CUDA when available.
- Falls back to CPU automatically if CUDA is unavailable.

## Troubleshooting

1. Import warnings in editor (for example google.colab or basicsr)
- These can appear before running setup cells.
- Run the setup/dependency cells first.

2. Missing checkpoint file
- Re-run the download stage.
- Verify expected file path under experiments/pretrained_models.

3. RIDNet loading fails
- Checkpoint may be incompatible with BasicSR RIDNet implementation.
- Keep ridnet_allow_fallback enabled and set a SwinIR fallback.

4. Input path not found
- Use a valid absolute/relative path when use_upload is False.
- In upload mode, rerun upload cell and confirm the logged path.

5. No output images generated
- Verify selected_models is not empty.
- Run validation cell to pinpoint missing artifacts.

## Current Limitations

- Single-image mode only.
- No built-in quantitative quality metrics table yet.
- External model download depends on network availability.

## Roadmap

- Add optional quality metrics (PSNR/SSIM where GT exists).
- Add runtime benchmarking per model.
- Add batch folder processing mode.
- Add optional output export packaging.

## Credits

- BasicSR by XPixelGroup and contributors
- ESRGAN and Real-ESRGAN model authors and contributors
- SwinIR and RIDNet authors and contributors

## License

This workspace uses upstream BasicSR code and model assets. Review license files in BasicSR and associated pretrained model sources before redistribution or commercial use.

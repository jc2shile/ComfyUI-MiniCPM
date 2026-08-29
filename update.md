# ComfyUI-MiniCPM Update Log

## V1.1.2 (2026-08-29)

### New Features
- **MiniCPM-V-4.6 (Transformers, native)**: first dropdown entry; loads via the native transformers implementation (model_type `minicpmv4_6`, requires `transformers>=5.7`, repo `openbmb/MiniCPM-V-4.6`, ~2.6GB bf16; SigLIP2-400M + Qwen3.5-0.8B hybrid-linear-attention base). The fast path for batch prompt reverse-engineering.
- **Architecture auto-detection**: the loader reads each checkpoint's local `config.json`. Checkpoints with `auto_map` (<= V4.5) keep the legacy `trust_remote_code` + `model.chat()` path; native architectures (V4.6+) load through `AutoProcessor` + `AutoModelForImageTextToText` + `apply_chat_template`/`generate`.
- **`downsample_mode` input (16x / 4x)** on both Transformers nodes: mixed visual-token compression for V4.6+ (16x = fast, 4x = finer detail; measured 249 vs 858 input tokens per image). Ignored by older models. Implementation detail: set `processor.image_processor.downsample_mode` and pass the same value to `model.generate` — passing it only to `apply_chat_template` is silently dropped in transformers 5.x and breaks token/feature alignment.
- **GGUF nodes: dynamic model discovery**: the dropdown enumerates every `*.gguf` under `models/LLM/GGUF` (recursive; any repo/quant), auto-pairing the vision projector `mmproj*.gguf` from the same folder or nearest parent directory; unpaired files are skipped with a console notice. Refresh the page to pick up newly added files.

### Fixes
- Legacy Transformers path: the system prompt is now actually inserted into `msgs` (it was silently discarded before).
- GGUF nodes no longer depend on the hard-coded `gguf_models` config list or `hf_hub_download` (config section kept but unused).

### Requirements Notes
- MiniCPM-V-4.6 needs `transformers>=5.7`; on ComfyUI environments pinned to transformers 4.x use V4.5/V4.0 instead.
- MiniCPM-V-4.5 GGUF inference needs llama-cpp-python compiled from llama.cpp after PR #15575.
- Measured locally (RTX 4060 Ti 16GB): V4.6 load ~4s / 2.5GB VRAM; single-image caption 2-5s.

## V1.1.1 (2025-08-28)
![MiniCPM v4 VS v45](https://github.com/user-attachments/assets/ad47e274-2a03-4fda-a7b2-7fa60825eb1e)
- **Internationalization (i18n) Support**: Added support for multiple languages, including EN, FR, JP, KO, RU, ZH.
- **Updated Example Workflows**: Revised and expanded example workflows for improved clarity and usability.
- **Renaming of Custom Nodes**: Enhanced organization and naming conventions for custom nodes to improve structure and readability.
- **Expanded Preset Prompts**: Added additional preset prompts to increase flexibility and functionality.
## V1.1.0 (2025-08-27)
### New Features
[![MiniCPM v4 VS v45](example_workflows/MiniCPM_v4VSv45.jpg)](example_workflows/MiniCPM_v4VSv45.json)
- **MiniCPM-V-4.5 Model Support**: Added comprehensive support for the latest MiniCPM-V-4.5 models
  - New Transformers model: `MiniCPM-V-4.5` (openbmb/MiniCPM-V-4_5) - Latest full precision version
  - New quantized model: `MiniCPM-V-4.5-int4` (openbmb/MiniCPM-V-4_5-int4) - Memory-efficient 4-bit version
- **Performance Improvements**: Added hf_xet dependency for faster downloads
- **Model Priority**: V4.5 models now appear first in dropdown menus

### Changes
- V4.5 GGUF models temporarily disabled due to llama-cpp-python 3.16 compatibility
- Enhanced error messages for unsupported models
- Improved model ordering and descriptions

### Usage Notes
- **Recommended**: Use MiniCPM-V-4.5 Transformers models for latest features and enhanced capabilities
- **Alternative**: Continue using MiniCPM-V-4.0 GGUF models (all quantizations available, see table above)
- **Coming Soon**: V4.5 GGUF support will be restored once llama-cpp-python compatibility is resolved

## V1.0.0 (2025-08-25)
### Initial Release
- **MiniCPM-V-4 Transformers Support**: Full integration with Hugging Face transformers models
[![MiniCPM-V-4](example_workflows/MiniCPM-V-4.jpg)](example_workflows/MiniCPM-V-4.json)
  - MiniCPM-V-4 full precision model
  - MiniCPM-V-4-int4 quantized model
- **MiniCPM-V-4 GGUF Support**: Comprehensive GGUF model support via llama-cpp-python
[![MiniCPM-V-4-GGUF](example_workflows/MiniCPM-V-4-GGUF.jpg)](example_workflows/MiniCPM-V-4-GGUF.json)
  - Multiple quantization levels (Q4_0 to Q8_0)
  - Optimized memory usage and performance
- **Dual Node Architecture**: 
  - Basic nodes for simple usage
  - Advanced nodes with full parameter control
- **Image & Video Processing**: Support for both image and video input processing
[![MiniCPM-V-4_video](example_workflows/MiniCPM-V-4_video.jpg)](example_workflows/MiniCPM-V-4_video.json)
- **Memory Management**: Three modes - Keep in Memory, Clear After Run, Global Cache
- **Caption Types**: 14 different preset prompt types for various use cases
- **Legacy Compatibility**: Backward compatible node for existing workflows

[![MiniCPM-V-4_batchImages](example_workflows/MiniCPM-V-4_batchImages.jpg)](example_workflows/MiniCPM-V-4_batchImages.json)

### Core Features
- **Multi-Modal Processing**: Image and video content understanding
- **Flexible Parameters**: Customizable temperature, top-p, top-k, repetition penalty
- **Auto-Download**: Automatic model downloading from Hugging Face
- **Cross-Platform**: Windows, macOS, and Linux support
- **CUDA Acceleration**: GPU acceleration support for faster inference

- **System Prompts**: Customizable system prompts for different use cases
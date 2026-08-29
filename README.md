# ComfyUI-MiniCPM

A custom ComfyUI node for MiniCPM vision-language models, supporting v4, v4.5, v4.6 and GGUF formats, enabling high-quality image captioning and visual analysis.

**🎉 Now supports MiniCPM-V-4.6! Ultra-efficient native-transformers model (~2.6GB, SigLIP2-400M + Qwen3.5-0.8B), ideal for fast prompt reverse-engineering.**

---
## News & Updates
- **2026/08/29**: Update ComfyUI-MiniCPM to **v1.1.2** ( [update.md](update.md#v112-2026-08-29) ): MiniCPM-V-4.6 support, `downsample_mode` control, dynamic GGUF model discovery
- **2025/08/28**: Update ComfyUI-MIniCPM to **v1.1.1** ( [update.md](update.md#v111-2025-08-28) )
- **2025/08/27**: Update ComfyUI-MIniCPM to **v1.1.0** ( [update.md](update.md#v110-2025-08-27) )
[![MiniCPM v4 VS v45](example_workflows/MiniCPM_v4VSv45.jpg)](example_workflows/MiniCPM_v4VSv45.json)
- Added support for **MiniCPM-V-4.5** models (Transformers)
  
## Features
- MiniCPM-V-4 GGUF
[![MiniCPM-V-4-GGUF](example_workflows/MiniCPM-V-4-GGUF.jpg)](example_workflows/MiniCPM-V-4-GGUF.json)
- MiniCPM-V-4 Batch Images
[![MiniCPM-V-4_batchImages](example_workflows/MiniCPM-V-4_batchImages.jpg)](example_workflows/MiniCPM-V-4_batchImages.json)
- MiniCPM-V-4 video
[![MiniCPM-V-4_video](example_workflows/MiniCPM-V-4_video.jpg)](example_workflows/MiniCPM-V-4_video.json)

- Supports **MiniCPM-V-4.6 / 4.5 / 4 (Transformers)** and **any local GGUF file** models
- **Latest MiniCPM-V-4.6** runs on the native transformers implementation (`transformers>=5.7`), ~2.6GB, very fast for batch captioning
- **`downsample_mode` (16x / 4x)** on V4.6: trade visual detail for speed (mixed 4x/16x visual token compression)
- Multiple caption types to suit different use cases (Describe, Caption, Analyze, etc.)
- Memory management options to balance VRAM usage and speed
- Transformers models auto-download on first use; GGUF models are discovered dynamically from `models/LLM/GGUF`
- Customizable parameters: max tokens, temperature, top-p/k sampling, repetition penalty
- Advanced node with full parameter control
- Legacy node for backward compatibility
- GGUF dropdown enumerates every `*.gguf` on disk (any repo/quant), auto-pairing `mmproj*.gguf` vision files

---

## Installation

Clone the repo into your ComfyUI custom nodes folder:

```bash
cd ComfyUI/custom_nodes
git clone https://github.com/1038lab/comfyui-minicpm.git
```

Install required dependencies:

```bash
cd ComfyUI/custom_nodes/comfyui-minicpm
ComfyUI\python_embeded\python pip install -r requirements.txt
ComfyUI\python_embeded\python llama_cpp_install.py
```

> [!note]
> `llama-cpp-python` CUDA Installation for ComfyUI Portable
> - [llama_cpp_install.md](llama_cpp_install/llama_cpp_install.md)
---

## Supported Models

### Transformers Models
| Model                | Description                                    |
| -------------------- | ---------------------------------------------- |
| **MiniCPM-V-4.6**        | 🌟 **Latest: native transformers (>=5.7), ~2.6GB, 4x/16x visual token compression, fastest** |
| **MiniCPM-V-4.5**        | V4.5 version with enhanced capabilities |
| **MiniCPM-V-4.5-int4**   | 🌟 **V4.5 4-bit quantized version, smaller memory footprint** |
| MiniCPM-V-4          | V4.0 full precision version, higher quality   |
| MiniCPM-V-4-int4     | V4.0 4-bit quantized version, smaller memory footprint |

https://huggingface.co/openbmb/MiniCPM-V-4.6
https://huggingface.co/openbmb/MiniCPM-V-4_5  
https://huggingface.co/openbmb/MiniCPM-V-4_5-int4  
https://huggingface.co/openbmb/MiniCPM-V-4
https://huggingface.co/openbmb/MiniCPM-V-4-int4

### GGUF Models

> **How it works now**: the GGUF nodes no longer rely on a built-in download list. Any `*.gguf` placed under `models/LLM/GGUF` (subfolders allowed) appears in the model dropdown after a page refresh; the vision projector is auto-paired from the same folder (or nearest parent) by matching `mmproj*.gguf`. Files without an mmproj pair are skipped with a console notice.
>
> **V4.5 GGUF**: requires a `llama-cpp-python` built from llama.cpp after PR #15575 (2025-08-26). Older builds will raise a compatibility error — use the V4.5 Transformers model, or rebuild llama-cpp-python from source.

#### Example: MiniCPM-V-4.0 quantizations
| Model                | Size      | Description                           |
| -------------------- | --------- | ------------------------------------- |
| **MiniCPM-V-4 (Q4_K_M)** | ~2.19GB   | **Recommended balance of quality/size** |
| MiniCPM-V-4 (Q4_0)      | ~2.08GB   | Standard 4-bit quantization          |
| MiniCPM-V-4 (Q4_1)      | ~2.29GB   | 4-bit quantization improved          |
| MiniCPM-V-4 (Q4_K_S)    | ~2.09GB   | 4-bit K-quants small                 |
| MiniCPM-V-4 (Q5_0)      | ~2.51GB   | 5-bit quantization                   |
| MiniCPM-V-4 (Q5_1)      | ~2.72GB   | 5-bit quantization improved          |
| MiniCPM-V-4 (Q5_K_M)    | ~2.56GB   | 5-bit K-quants medium                |
| MiniCPM-V-4 (Q5_K_S)    | ~2.51GB   | 5-bit K-quants small                 |
| MiniCPM-V-4 (Q6_K)      | ~2.96GB   | Very high quality                    |
| MiniCPM-V-4 (Q8_0)      | ~3.83GB   | Highest quality quantized            |

https://huggingface.co/openbmb/MiniCPM-V-4-gguf

> Transformers models auto-download to `models/LLM/<repo-name>` on first use (manual placement into the same folder also works).
> GGUF models are **not** auto-downloaded anymore: drop the `.gguf` + its `mmproj-model-f16.gguf` into any folder under `models/LLM/GGUF` and refresh.

---

## Available Nodes

### 1. MiniCPM-4-V-Transformers
- Basic transformers-based node with essential parameters
- `downsample_mode` (16x/4x) for MiniCPM-V-4.6+ (ignored by older models)
- Supports image and video input
- Memory management options
- Preset prompt types

### 2. MiniCPM-4-V-Transformers Advanced
- Full-featured transformers-based node
- All parameters customizable
- System prompt support
- Advanced video processing options

### 3. MiniCPM-4-V-GGUF
- GGUF-based node with essential parameters
- Model list discovered dynamically from `models/LLM/GGUF`

### 4. MiniCPM-4-V-GGUF Advanced
- Full-featured GGUF-based node
- All parameters customizable

### 5. MiniCPM (Legacy)
- Original node for backward compatibility
- Basic functionality

---

## Usage

1. Add the **MiniCPM** node from the `🧪AILab` category in ComfyUI.
2. Connect an image or video input node to the MiniCPM node.
3. Select the model variant (transformers dropdown defaults to the first entry, currently MiniCPM-V-4.6).
4. Choose caption type and adjust parameters as needed.
5. Execute your workflow to generate captions or analysis.

---

## Configuration Defaults

```json
{
  "context_window": 4096,
  "gpu_layers": -1,
  "cpu_threads": 4,
  "default_max_tokens": 1024,
  "default_temperature": 0.7,
  "default_top_p": 0.9,
  "default_top_k": 100,
  "default_repetition_penalty": 1.10,
  "default_system_prompt": "You are MiniCPM-V, a helpful, concise and knowledgeable vision-language assistant. Answer directly and stay on task."
}
```

---

## Caption Types

* **Describe:** Describe this image in detail.
* **Caption:** Write a concise caption for this image.
* **Analyze:** Analyze the main elements and scene in this image.
* **Identify:** What objects and subjects do you see in this image?
* **Explain:** Explain what's happening in this image.
* **List:** List the main objects visible in this image.
* **Scene:** Describe the scene and setting of this image.
* **Details:** What are the key details in this image?
* **Summarize:** Summarize the key content of this image in 1-2 sentences.
* **Emotion:** Describe the emotions or mood conveyed by this image.
* **Style:** Describe the artistic or visual style of this image.
* **Location:** Where might this image be taken? Analyze the setting or location.
* **Question:** What question could be asked based on this image?
* **Creative:** Describe this image as if writing the beginning of a short story.

---

## Memory Management Options

* **Keep in Memory:** Model stays loaded for faster subsequent runs
* **Clear After Run:** Model is unloaded after each run to save memory
* **Global Cache:** Model is cached globally and shared between nodes

---

## Tips

### VRAM Requirements
* **~3GB VRAM**: MiniCPM-V-4.6 (bf16, fastest — best for batch prompt reverse-engineering)
* **4-6GB VRAM**: Use MiniCPM-V-4-int4 or GGUF Q4 models
* **8GB VRAM**: Use MiniCPM-V-4.5-int4 (recommended)
* **12GB+ VRAM**: Can use full MiniCPM-V-4.5
* **CUDA OOM Error**: Try int4 quantized models or CPU mode

### General Tips
* 🌟 **MiniCPM-V-4.6**: fastest and lightest; use `16x` for bulk tagging, `4x` when finer visual detail matters
* **MiniCPM-V-4.5 Transformers**: highest caption quality of this plugin's models (8B base)
* For **best balance**: use MiniCPM-V-4 (Q4_K_M) GGUF model
* For **highest quality**: use MiniCPM-V-4.5 Transformers
* For **low VRAM**: use MiniCPM-V-4.5-int4 or MiniCPM-V-4 (Q4_0) GGUF
* Adjust temperature (0.6–0.8) for balancing creativity and coherence.
* Use top-p (0.9) and top-k (80) sampling for natural output diversity.
* Lower max tokens or precision (bf16/fp16) for faster generation on less powerful GPUs.
* Memory modes help optimize VRAM usage: default, balanced, max savings.
* Transformers models offer better quality but use more memory.
* GGUF models are more memory-efficient but may have slightly lower quality.

---

## License


GPL-3.0 License

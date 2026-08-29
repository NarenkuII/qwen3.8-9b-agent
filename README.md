# Qwen3.8 9B Agentic - Build & Setup Guide (Ollama & Opencode)

## 1. Description
Optimized agentic configuration for Qwen3.8 9B (Q4_K_M) targeting 8 GB VRAM GPUs (e.g. RTX 2080) with 100% GPU offload and a 32k extended context window.

## 2. Base Model
- **HuggingFace Source:** `hf.co/ge525/Qwen3.8-9B-Distill-uncensored-heretic-Q4_K_M-GGUF`
- **Architecture:** Qwen 9B
- **Quantization:** Q4_K_M (~5.8 GB)

## 3. Ollama Modelfile

A ready-to-use `Modelfile` is included in this repository:

```dockerfile
FROM hf.co/ge525/Qwen3.8-9B-Distill-uncensored-heretic-Q4_K_M-GGUF

# 100% GPU Offload (all layers on GPU)
PARAMETER num_gpu 99

# Extended agentic context window
PARAMETER num_ctx 32768
PARAMETER num_batch 512
PARAMETER num_thread 6

# Sampling parameters tuned for agentic reasoning and tool use
PARAMETER temperature 0.6
PARAMETER top_p 0.9
PARAMETER top_k 20
PARAMETER repeat_penalty 1.05
```

## 4. Build Command

Run in terminal:
```bash
ollama create qwen3.8-9b-agent -f ./Modelfile
```

## 5. Opencode Configuration (`~/.config/opencode/opencode.json`)

Add the local Ollama provider to your Opencode configuration:

```json
{
  "providers": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (Local)",
      "options": {
        "baseURL": "http://127.0.0.1:11434/v1"
      },
      "models": {
        "qwen3.8-9b-agent": {
          "name": "Qwen3.8 9B Agentic (Local GPU)",
          "limit": {
            "context": 32768,
            "output": 4096
          }
        }
      }
    }
  }
}
```

## 6. Hardware Requirements
- **VRAM:** ~6.6 GB peak (fits comfortably in 8 GB VRAM)
- **RAM:** 16 GB+
- **VRAM Offload:** 100% on GPU (0 layers on CPU)

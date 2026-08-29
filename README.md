# Qwen3.8 9B Agentic - Build & Setup Guide (Ollama & Opencode)

Configuration agentique optimisée pour **Qwen3.8 9B** (Q4_K_M) avec offload GPU à 100%, support étendu de contexte et compatibilité Opencode / Ollama.

---

## 1. Configurations GPU

| Profil | GPU Cible | VRAM Requise | Contexte Max | Modelfile |
| :--- | :--- | :--- | :--- | :--- |
| **RTX 2080 (8 GB)** | RTX 2080 / 3070 / 4060 (8 GB) | ~6.6 GB | **32,768 (32k)** | `Modelfile` |
| **RTX 3080 (10/12 GB)** | RTX 3080 / 4070 / 3080 Ti | ~8.8 - 10.2 GB | **65,536 (64k)** | `Modelfile.rtx3080` |

---

## 2. Base Model
- **Source HuggingFace :** `hf.co/ge525/Qwen3.8-9B-Distill-uncensored-heretic-Q4_K_M-GGUF`
- **Architecture :** Qwen 9B
- **Quantization :** Q4_K_M (~5.8 GB)
- **MTP (Speculative Decoding) :** Compatible

---

## 3. Ollama Modelfiles

### A. Profil RTX 2080 / 8 GB (`Modelfile`)
```dockerfile
FROM hf.co/ge525/Qwen3.8-9B-Distill-uncensored-heretic-Q4_K_M-GGUF

# 100% GPU Offload
PARAMETER num_gpu 99

# Context 32k
PARAMETER num_ctx 32768
PARAMETER num_batch 512
PARAMETER num_thread 6

# Sampling
PARAMETER temperature 0.6
PARAMETER top_p 0.9
PARAMETER top_k 20
PARAMETER repeat_penalty 1.05
```

### B. Profil RTX 3080 / 10-12 GB (`Modelfile.rtx3080`)
```dockerfile
FROM hf.co/ge525/Qwen3.8-9B-Distill-uncensored-heretic-Q4_K_M-GGUF

# 100% GPU Offload
PARAMETER num_gpu 99

# Context 64k & larger evaluation batch size
PARAMETER num_ctx 65536
PARAMETER num_batch 1024
PARAMETER num_thread 8

# Sampling
PARAMETER temperature 0.6
PARAMETER top_p 0.9
PARAMETER top_k 20
PARAMETER repeat_penalty 1.05
```

---

## 4. Commandes de Build

Pour RTX 2080 (8 GB) :
```bash
ollama create qwen3.8-9b-agent -f ./Modelfile
```

Pour RTX 3080 (10 GB / 12 GB) :
```bash
ollama create qwen3.8-9b-agent-3080 -f ./Modelfile.rtx3080
```

---

## 5. Configuration Opencode (`~/.config/opencode/opencode.json`)

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
          "name": "Qwen3.8 9B Agentic (RTX 2080 - 32k)",
          "limit": {
            "context": 32768,
            "output": 4096
          }
        },
        "qwen3.8-9b-agent-3080": {
          "name": "Qwen3.8 9B Agentic (RTX 3080 - 64k)",
          "limit": {
            "context": 65536,
            "output": 8192
          }
        }
      }
    }
  }
}
```

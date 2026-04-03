# 🦙 Local AI Models with Ollama

> **Run powerful AI models 100% offline, free, and private on your own hardware.**  
> A complete guide to hosting, configuring, and using local LLMs for coding, chat, reasoning, and more.

[
[
[

***

## 📋 Table of Contents

- [Why Local AI?](#why-local-ai)
- [Installation](#installation)
  - [Linux](#linux)
  - [macOS](#macos)
  - [Windows](#windows)
- [Quick Start](#quick-start)
- [CLI Cheat Sheet](#cli-cheat-sheet)
- [Models by Use Case](#models-by-use-case)
- [Coding Models by Hardware](#coding-models-by-hardware)
- [Modelfiles: The Complete Guide](#modelfiles-the-complete-guide)
- [Integration with AI Coding Agents](#integration-with-ai-coding-agents)
- [Performance Tuning](#performance-tuning)
- [Troubleshooting](#troubleshooting)
- [Advanced Topics](#advanced-topics)
- [Resources](#resources)
- [**🔥 NEW: AMD GPU Setup Guide (ROCm & Vulkan)**](#-amd-gpu-setup-guide-rocm--vulkan)
- [**🔥 NEW: User Service vs System Service**](#-user-service-vs-system-service)
- [**🔥 NEW: Real-World Troubleshooting Cases**](#-real-world-troubleshooting-cases)

***

## 🎯 Why Local AI?

| Benefit | Description |
|---------|-------------|
| 🔒 **Privacy** | Your code, data, and prompts never leave your machine |
| 💰 **Free** | No API costs, unlimited usage |
| ⚡ **Low Latency** | No network round-trips, instant responses |
| 🌐 **Offline** | Works on planes, trains, and air-gapped systems |
| 🛠️ **Customizable** | Fine-tune, quantize, and configure models your way |
| 📦 **Portable** | Export and share models easily |

***

## 📥 Installation

### Linux

#### Ubuntu/Debian-based (including CachyOS, Arch, Fedora)

```bash
# One-line install (official script)
curl -fsSL https://ollama.com/install.sh | sh

# Verify installation
ollama --version

# Start the server (runs in background automatically)
ollama serve

# Test with a small model
ollama run llama3.2:1b
```

#### Arch Linux / CachyOS

```bash
# From AUR (using yay or paru)
yay -S ollama

# Or from official repos (if available)
sudo pacman -S ollama

# Enable systemd service
sudo systemctl enable --now ollama
```

#### Manual Installation (All Distros)

```bash
# Download binary
curl -L https://ollama.com/download/ollama-linux-amd64.tgz -o ollama.tgz

# Extract and install
sudo tar -C /usr -xzf ollama.tgz

# Add to PATH (if not already)
echo 'export PATH=$PATH:/usr/bin' >> ~/.bashrc
source ~/.bashrc
```

***

### macOS

#### Homebrew (Recommended)

```bash
# Install via Homebrew
brew install ollama

# Start the server
ollama serve

# Test
ollama run llama3.2:1b
```

#### Direct Download

```bash
# Download from official site
curl -L https://ollama.com/download/Ollama-darwin.zip -o Ollama.zip

# Extract and move to Applications
unzip Ollama.zip
sudo mv Ollama.app /Applications/

# Launch from Spotlight or Applications folder
```

**Apple Silicon Note:** Ollama automatically uses Metal acceleration on M1/M2/M3 chips for 5–10× speedup 🚀

***

### Windows

#### Installer (Recommended)

1. Download from [ollama.com](https://ollama.com)
2. Run `OllamaSetup.exe`
3. Accept defaults (installs to `%LOCALAPPDATA%\Programs\Ollama`)
4. Ollama runs in system tray automatically

#### PowerShell (Automated)

```powershell
# Download and install
irm https://ollama.com/install.ps1 | iex

# Verify
ollama --version

# Test
ollama run llama3.2:1b
```

#### WSL2 (Linux on Windows)

```bash
# Inside WSL2 (Ubuntu recommended)
curl -fsSL https://ollama.com/install.sh | sh

# Access from Windows: http://localhost:11434
```

**GPU Acceleration:** NVIDIA GPUs work automatically via CUDA. AMD GPUs require ROCm setup (see [Troubleshooting](#troubleshooting)).

***

## 🚀 Quick Start

```bash
# 1. Pull a model
ollama pull llama3.2:3b

# 2. Run it interactively
ollama run llama3.2:3b

# 3. One-off query
ollama run llama3.2:3b "Explain quantum computing in 2 sentences"

# 4. Check what's running
ollama ps

# 5. List all downloaded models
ollama list

# 6. Remove a model to free space
ollama rm llama3.2:3b
```

***

## 📖 CLI Cheat Sheet

### Core Commands

| Command | Description | Example |
|---------|-------------|---------|
| `ollama serve` | Start the API server (port 11434) | `ollama serve` |
| `ollama run <model>` | Interactive chat with a model | `ollama run llama3.2` |
| `ollama pull <model>` | Download a model | `ollama pull qwen2.5-coder:7b` |
| `ollama push <model>` | Upload to registry | `ollama push my-model` |
| `ollama list` / `ollama ls` | List downloaded models | `ollama ls` |
| `ollama ps` | Show running (loaded) models | `ollama ps` |
| `ollama stop <model>` | Unload a running model | `ollama stop llama3.2` |
| `ollama rm <model>` | Delete a model | `ollama rm llama3.2:70b` |
| `ollama cp <src> <dst>` | Copy a model | `ollama cp qwen2.5-coder:7b my-coder` |
| `ollama show <model>` | Display model details | `ollama show llama3.2 --modelfile` |
| `ollama create <name>` | Create custom model from Modelfile | `ollama create my-model -f Modelfile` |

### Run Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--verbose` / `-v` | Show timing stats (tokens/s) | `ollama run llama3.2 -v` |
| `--format json` | Force JSON output | `ollama run llama3.2 --format json` |
| `-p`, `--parameters` | Inline parameters | `ollama run llama3.2 -p "temperature 0.7"` |
| `--nowordwrap` | Disable word wrapping | `ollama run llama3.2 --nowordwrap` |
| `--insecure` | Allow HTTP registries | `ollama pull --insecure my-model` |

### API Endpoints

```bash
# Chat completion (OpenAI-compatible)
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.2",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'

# Generate (native Ollama)
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.2",
    "prompt": "Explain AI",
    "stream": false
  }'

# List models via API
curl http://localhost:11434/api/tags
```

***

## 🎯 Models by Use Case

### 💻 Coding & Development

| Model | Size | Min RAM | Context | Best For |
|-------|------|---------|---------|----------|
| **Qwen2.5-Coder-7B-Instruct** | 7B | 6 GB | 128K | 🏆 Best overall coding model |
| **Qwen2.5-Coder-32B** | 32B | 20 GB | 128K | GPT-4-level coding |
| **DeepSeek-Coder-V2-Lite** | 16B | 12 GB | 128K | Multi-language support |
| **CodeLlama-7B-Instruct** | 7B | 6 GB | 16K | Autocomplete, legacy support |
| **StarCoder2-7B** | 7B | 6 GB | 16K | Code completion, fill-in-middle |

```bash
# Pull the best all-rounder
ollama pull qwen2.5-coder:7b-instruct
```

### 🧠 Reasoning & Problem Solving

| Model | Size | Min RAM | Context | Best For |
|-------|------|---------|---------|----------|
| **DeepSeek-R1-Distill-Qwen-14B** | 14B | 10 GB | 128K | Chain-of-thought, math |
| **QwQ-32B** | 32B | 20 GB | 128K | Complex reasoning tasks |
| **Llama-3.3-70B** | 70B | 40 GB | 128K | GPT-4-class reasoning |

```bash
ollama pull deepseek-r1:14b
```

### 💬 Chat & General Assistance

| Model | Size | Min RAM | Context | Best For |
|-------|------|---------|---------|----------|
| **Llama-3.2-3B** | 3B | 2 GB | 128K | Fast chat, low-end hardware |
| **Llama-3.2-7B** | 7B | 6 GB | 128K | Balanced chat assistant |
| **Mistral-7B-Instruct-v0.3** | 7B | 6 GB | 32K | Instruction following |
| **Gemma-2-9B** | 9B | 7 GB | 8K | Google's efficient model |

```bash
ollama pull llama3.2:3b
```

### 📚 Long Context & Document Analysis

| Model | Size | Min RAM | Context | Best For |
|-------|------|---------|---------|----------|
| **GLM-4-Flash** | ~10B | 8 GB | 128K | 🏆 Fast + huge context |
| **Phi-3.5-MoE** | 16B | 12 GB | 128K | Efficient long-doc handling |
| **Yi-1.5-34B** | 34B | 22 GB | 256K | Massive context window |

```bash
ollama pull glm-4.7-flash
```

### 🖼️ Multimodal (Image + Text)

| Model | Size | Min RAM | Context | Best For |
|-------|------|---------|---------|----------|
| **LLaVA-1.6-Vicuna-7B** | 7B | 8 GB | 4K | Image understanding |
| **BakLLaVA-7B** | 7B | 8 GB | 4K | OCR, chart analysis |
| **Moondream2** | 1.4B | 2 GB | 2K | Ultra-fast image QA |

```bash
ollama pull llava:7b
```

***

## 💻 Coding Models by Hardware

### 🖥️ Hardware Classes

| Class | RAM/VRAM | GPU | Example Machines |
|-------|----------|-----|------------------|
| **Ultra-Light** | 4–8 GB | None/Integrated | Old laptops, Raspberry Pi 5 |
| **Lightweight** | 8–16 GB | GTX 1650 / RTX 3050 | Budget gaming laptops |
| **Mid-Range** | 16–24 GB | RTX 3060 / 4060 | Modern gaming laptops |
| **High-End** | 24–48 GB | RTX 3080 / 4070 Ti | Workstations |
| **Enthusiast** | 48+ GB | RTX 3090 / 4090 | AI workstations, Mac Studio |

***

### Ultra-Light (4–8 GB RAM, No GPU)

```bash
# Best for: Old laptops, CPU-only, 4–8 GB RAM

# 1. Qwen2.5-Coder-3B (🏆 Top pick)
ollama pull qwen2.5-coder:3b-instruct

# 2. Llama-3.2-3B (Fast generalist)
ollama pull llama3.2:3b

# 3. Phi-3.5-Mini (Microsoft's efficient model)
ollama pull phi3.5:3.8b

# Create 64K context for agentic tools
echo -e "FROM qwen2.5-coder:3b-instruct\nPARAMETER num_ctx 65536" > Modelfile
ollama create qwen2.5-coder-3b-64k -f Modelfile
```

**Expected Performance:** 5–15 tokens/sec (CPU)

***

### Lightweight (8–16 GB RAM, Entry GPU)

```bash
# Best for: GTX 1650, RTX 3050, 8–16 GB RAM

# 1. Qwen2.5-Coder-7B (🏆 Best balance)
ollama pull qwen2.5-coder:7b-instruct

# 2. DeepSeek-Coder-V2-Lite (16B, MoE)
ollama pull deepseek-coder-v2:16b

# 3. CodeLlama-7B (Legacy support)
ollama pull codellama:7b-instruct

# Create 64K context version
echo -e "FROM qwen2.5-coder:7b-instruct\nPARAMETER num_ctx 65536" > Modelfile
ollama create qwen2.5-coder-7b-64k -f Modelfile
```

**Expected Performance:** 15–40 tokens/sec (GPU), 8–20 tokens/sec (CPU)

***

### Mid-Range (16–24 GB RAM, RTX 3060/4060)

```bash
# Best for: RTX 3060 12GB, RTX 4060 8GB, 16–24 GB RAM

# 1. Qwen2.5-Coder-14B (🏆 Sweet spot)
ollama pull qwen2.5-coder:14b

# 2. DeepSeek-R1-14B (Reasoning + coding)
ollama pull deepseek-r1:14b

# 3. Mistral-Nemo-12B (General purpose)
ollama pull mistral-nemo:12b

# Create 64K context version
echo -e "FROM qwen2.5-coder:14b\nPARAMETER num_ctx 65536" > Modelfile
ollama create qwen2.5-coder-14b-64k -f Modelfile
```

**Expected Performance:** 30–60 tokens/sec (GPU)

***

### High-End (24–48 GB RAM, RTX 3080/4070 Ti)

```bash
# Best for: RTX 3080 12GB, RTX 4070 Ti 12GB, 24–32 GB RAM

# 1. Qwen2.5-Coder-32B (🏆 Near GPT-4 coding)
ollama pull qwen2.5-coder:32b

# 2. Llama-3.3-70B (Q4_K_M quantized)
ollama pull llama3.3:70b

# 3. DeepSeek-Coder-V2 (Full 236B MoE, activated 21B)
ollama pull deepseek-coder-v2:236b

# Create 64K context version
echo -e "FROM qwen2.5-coder:32b\nPARAMETER num_ctx 65536" > Modelfile
ollama create qwen2.5-coder-32b-64k -f Modelfile
```

**Expected Performance:** 20–40 tokens/sec (GPU, 32B), 5–15 tokens/sec (70B)

***

### Enthusiast (48+ GB RAM, RTX 3090/4090, Mac Studio)

```bash
# Best for: RTX 3090 24GB, RTX 4090 24GB, Mac M2/M3 Ultra, 48+ GB RAM

# 1. Llama-3.3-70B (Full precision possible)
ollama pull llama3.3:70b

# 2. Qwen2.5-Coder-32B (Unquantized)
ollama pull qwen2.5-coder:32b-instruct

# 3. Falcon-H1R-7B (256K context beast)
ollama pull falcon-h1r:7b

# Create 128K context for massive projects
echo -e "FROM llama3.3:70b\nPARAMETER num_ctx 131072" > Modelfile
ollama create llama3.3-70b-128k -f Modelfile
```

**Expected Performance:** 40–80 tokens/sec (4090, 32B), 15–30 tokens/sec (70B)

***

## 📝 Modelfiles: The Complete Guide

### What is a Modelfile?

A **Modelfile** is a simple configuration file that customizes a model's behavior, parameters, and system prompts. Think of it as a Dockerfile for LLMs.

### Syntax

```dockerfile
# Base model
FROM <model-name>:<tag>

# System prompt (optional)
SYSTEM """
You are an expert software engineer.
Always write clean, documented, and tested code.
"""

# Parameters
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER num_ctx 65536
PARAMETER num_gpu 1

# Templates (advanced)
TEMPLATE """
{{ if .System }}<|system|>
{{ .System }}</s>
{{ end }}
<|user|>
{{ .Prompt }}</s>
<|assistant|>
{{ .Response }}
"""
```

***

### Essential Modelfiles for Coding Agents

#### 1. **Universal 64K Context (Most Agentic Tools)**

Use with: **Claude Code, Cline, Aider, Continue.dev**

```dockerfile
# Modelfile for qwen2.5-coder-7b-64k
FROM qwen2.5-coder:7b-instruct
PARAMETER num_ctx 65536
PARAMETER temperature 0.7
PARAMETER top_p 0.9
```

```bash
# Build it
ollama create qwen2.5-coder-7b-64k -f Modelfile

# Use it
ollama run qwen2.5-coder-7b-64k
```

***

#### 2. **Claude Code Optimized (Qwen2.5-Coder-7B)**

```dockerfile
# Modelfile for claude-code-qwen-7b
FROM qwen2.5-coder:7b-instruct
PARAMETER num_ctx 65536
PARAMETER temperature 0.5
PARAMETER top_p 0.85
PARAMETER repeat_penalty 1.1
SYSTEM """
You are an expert full-stack developer assisting with coding tasks.
Always:
- Write complete, runnable code
- Include error handling
- Add comments for complex logic
- Follow best practices for the language
- Test your code mentally before outputting
"""
```

```bash
ollama create claude-code-qwen-7b -f Modelfile
ollama launch claude --model claude-code-qwen-7b
```

***

#### 3. **128K Context for Large Codebases**

Use with: **GLM-4-Flash, Yi-1.5, large repos**

```dockerfile
# Modelfile for qwen2.5-coder-7b-128k
FROM qwen2.5-coder:7b-instruct
PARAMETER num_ctx 131072
PARAMETER temperature 0.6
PARAMETER top_p 0.9
```

```bash
ollama create qwen2.5-coder-7b-128k -f Modelfile
```

***

#### 4. **Fast Autocomplete (Low Latency)**

```dockerfile
# Modelfile for qwen2.5-coder-3b-fast
FROM qwen2.5-coder:3b-instruct
PARAMETER num_ctx 8192
PARAMETER temperature 0.8
PARAMETER top_p 0.95
PARAMETER num_predict 256
```

```bash
ollama create qwen2.5-coder-3b-fast -f Modelfile
```

***

#### 5. **Multi-File Refactoring Specialist**

```dockerfile
# Modelfile for refactor-specialist
FROM qwen2.5-coder:14b
PARAMETER num_ctx 65536
PARAMETER temperature 0.3
PARAMETER top_p 0.8
PARAMETER repeat_penalty 1.2
SYSTEM """
You are a refactoring specialist.
When asked to refactor:
1. Analyze the entire codebase structure first
2. Propose a step-by-step plan
3. Execute changes file by file
4. Ensure all imports and dependencies are updated
5. Run mental tests for breaking changes
"""
```

```bash
ollama create refactor-specialist -f Modelfile
```

***

### Which Modelfile for Which Hardware?

| Hardware | Model | Modelfile | Context |
|----------|-------|-----------|---------|
| 4–8 GB RAM | `qwen2.5-coder:3b` | 64K basic | 65536 |
| 8–16 GB RAM | `qwen2.5-coder:7b` | Claude Code optimized | 65536 |
| 16–24 GB RAM | `qwen2.5-coder:14b` | 64K or 128K | 65536–131072 |
| 24+ GB RAM | `qwen2.5-coder:32b` | 128K for large repos | 131072 |
| 48+ GB RAM | `llama3.3:70b` | 128K+ for monorepos | 131072+ |

***

## 🤖 Integration with AI Coding Agents

### Claude Code + Ollama

**Requirements:**
- Ollama ≥ 0.5.0
- Claude Code CLI installed
- Model with **tool support** + **64K context**

```bash
# 1. Pull and configure model
ollama pull qwen2.5-coder:7b-instruct

# 2. Create 64K Modelfile
cat > Modelfile << EOF
FROM qwen2.5-coder:7b-instruct
PARAMETER num_ctx 65536
PARAMETER temperature 0.5
EOF

ollama create claude-code-qwen -f Modelfile

# 3. Set environment variables
export OLLAMA_HOST=http://localhost:11434
export CLAUDE_CODE_MODEL=claude-code-qwen

# 4. Launch
ollama launch claude --model claude-code-qwen
```

**Test:**
```bash
claude "Create a React component for a todo list"
```

***

### Cline (VS Code Extension)

**Setup:**

1. Install Cline extension in VS Code
2. Open Cline settings (`Ctrl+,`)
3. Set:
   - **Provider:** Ollama
   - **Model:** `qwen2.5-coder:7b-instruct`
   - **Base URL:** `http://localhost:11434`

**Modelfile for Cline:**

```dockerfile
FROM qwen2.5-coder:7b-instruct
PARAMETER num_ctx 65536
PARAMETER temperature 0.6
```

***

### Aider (CLI Pair Programming)

```bash
# Install aider
pip install aider-chat

# Run with Ollama
aider --model ollama/qwen2.5-coder:7b-instruct \
      --openai-api-base http://localhost:11434/v1 \
      --openai-api-key ollama

# Or create a config file (~/.aider.conf.yml)
model: ollama/qwen2.5-coder:7b-instruct
openai-api-base: http://localhost:11434/v1
openai-api-key: ollama
```

***

### Continue.dev (VS Code / JetBrains)

**config.json:**

```json
{
  "models": [
    {
      "title": "Local Qwen Coder",
      "provider": "ollama",
      "model": "qwen2.5-coder:7b-instruct",
      "apiBase": "http://localhost:11434"
    }
  ],
  "tabAutocompleteModel": {
    "title": "Qwen Autocomplete",
    "provider": "ollama",
    "model": "qwen2.5-coder:3b-instruct"
  }
}
```

***

### Gemini CLI + Ollama

**Note:** Gemini CLI officially supports cloud models only, but you can proxy Ollama:

```bash
# Use Ollama's OpenAI-compatible endpoint
export OPENAI_API_BASE=http://localhost:11434/v1
export OPENAI_API_KEY=ollama

# Then use any OpenAI-compatible tool
```

***

## ⚙️ Performance Tuning

### Environment Variables

```bash
# Increase parallel requests (default: 1)
export OLLAMA_NUM_PARALLEL=4

# Limit GPU layers (for VRAM management)
export OLLAMA_NUM_GPU=1

# Set keep-alive time (seconds, default: 300)
export OLLAMA_KEEP_ALIVE=600

# Change listen port (default: 11434)
export OLLAMA_HOST=0.0.0.0:11434

# Enable debug logging
export OLLAMA_DEBUG=1
```

### Modelfile Parameters

| Parameter | Description | Recommended Value |
|-----------|-------------|-------------------|
| `num_ctx` | Context window size | 65536 (agents), 4096 (chat) |
| `temperature` | Randomness (0–2) | 0.5–0.7 (coding), 0.8–1.0 (creative) |
| `top_p` | Nucleus sampling | 0.85–0.95 |
| `top_k` | Top-K sampling | 40–100 |
| `repeat_penalty` | Penalize repetition | 1.1–1.3 |
| `num_predict` | Max tokens to generate | 2048–4096 |
| `num_gpu` | GPU layers to offload | 99 (max), reduce if OOM |

### Quantization Guide

| Quant | Size Reduction | Quality Loss | Use Case |
|-------|---------------|--------------|----------|
| Q8_0 | ~15% | None | Production, high accuracy |
| Q6_K | ~25% | Minimal | Best balance |
| Q5_K_M | ~30% | Negligible | 🏆 Recommended default |
| Q4_K_M | ~40% | Slight | Low VRAM, still great |
| Q3_K_M | ~50% | Noticeable | Very low VRAM |
| Q2_K | ~60% | Significant | Last resort |

```bash
# Pull specific quantization
ollama pull qwen2.5-coder:7b-instruct-q5_K_M
ollama pull qwen2.5-coder:7b-instruct-q4_K_M
```

***

## 🐛 Troubleshooting

### Common Issues

#### 1. **"Model does not support tools" Error**

**Cause:** Model lacks function/tool calling support.

**Fix:**
```bash
# Use a compatible model
ollama pull qwen2.5-coder:7b-instruct
# NOT deepseek-coder:latest (no tool support)
```

***

#### 2. **Out of Memory (OOM)**

**Symptoms:** Model fails to load, system freezes.

**Fixes:**
```bash
# 1. Use smaller quantization
ollama pull qwen2.5-coder:7b-instruct-q4_K_M

# 2. Reduce context
echo -e "FROM qwen2.5-coder:7b\nPARAMETER num_ctx 32768" > Modelfile
ollama create my-model -f Modelfile

# 3. Limit GPU layers in Modelfile
echo -e "FROM qwen2.5-coder:7b\nPARAMETER num_gpu 50" > Modelfile
```

***

#### 3. **Slow Inference on CPU**

**Fixes:**
```bash
# 1. Use smaller model
ollama pull qwen2.5-coder:3b-instruct

# 2. Enable AVX2 (if supported)
# Most modern CPUs have this enabled by default

# 3. Close other apps to free RAM

# 4. Use Q4_K_M quantization
ollama pull qwen2.5-coder:7b-instruct-q4_K_M
```

***

#### 4. **AMD GPU Not Detected (Linux)**

```bash
# Install ROCm
sudo apt install rocm-libs  # Ubuntu/Debian
# OR
sudo pacman -S rocm-opencl-runtime  # Arch

# Set environment variable
export HSA_OVERRIDE_GFX_VERSION=11.0.0  # For RX 7000 series

# Restart Ollama
sudo systemctl restart ollama
```

***

#### 5. **CUDA Out of Memory (NVIDIA)**

```bash
# Check VRAM usage
nvidia-smi

# Reduce GPU layers in Modelfile
echo -e "FROM qwen2.5-coder:7b\nPARAMETER num_gpu 30" > Modelfile
ollama create my-model -f Modelfile

# Or use smaller quantization
ollama pull qwen2.5-coder:7b-instruct-q4_K_M
```

***

#### 6. **Ollama Service Not Starting**

```bash
# Check status
systemctl status ollama  # Linux
brew services list  # macOS

# Restart
sudo systemctl restart ollama  # Linux
brew services restart ollama  # macOS

# Check logs
journalctl -u ollama -f  # Linux
```

***

## 🚀 Advanced Topics

### Running Ollama as a Remote Server

```bash
# On server (enable remote access)
export OLLAMA_HOST=0.0.0.0:11434
ollama serve

# On client
export OLLAMA_HOST=http://server-ip:11434
ollama list  # Should show server's models
```

**Security:** Use a reverse proxy (Nginx, Caddy) with HTTPS and authentication for production.

***

### Docker Deployment

```dockerfile
FROM ollama/ollama:latest

# Pre-pull models
RUN ollama pull qwen2.5-coder:7b-instruct

# Expose port
EXPOSE 11434

# Volume for persistence
VOLUME /root/.ollama
```

```bash
# Run container
docker run -d -p 11434:11434 \
  -v ollama-data:/root/.ollama \
  --gpus all \
  --name ollama \
  ollama/ollama
```

***

### Custom System Prompts

```dockerfile
# Modelfile with custom system prompt
FROM qwen2.5-coder:7b-instruct

SYSTEM """
You are a senior Python engineer at a FAANG company.
Your code is:
- PEP 8 compliant
- Fully typed with type hints
- Includes docstrings
- Has comprehensive error handling
- Includes unit tests

Always explain your reasoning before showing code.
"""

PARAMETER num_ctx 65536
PARAMETER temperature 0.5
```

```bash
ollama create senior-python-dev -f Modelfile
```

***

### Model Merging (Experimental)

```dockerfile
# Merge two models
FROM qwen2.5-coder:7b-instruct
ADAPTER mistral-7b-instruct-v0.3
PARAMETER num_ctx 65536
```

***

### API Rate Limiting

```bash
# Limit concurrent requests
export OLLAMA_NUM_PARALLEL=2

# Use a reverse proxy (Nginx example)
# /etc/nginx/nginx.conf
http {
    limit_req_zone $binary_remote_addr zone=ollama:10m rate=10r/s;
    
    server {
        location / {
            limit_req zone=ollama burst=20;
            proxy_pass http://localhost:11434;
        }
    }
}
```

***

## 🔥 AMD GPU Setup Guide (ROCm & Vulkan)

### Why This Matters

AMD GPU support in Ollama has improved dramatically in 2025–2026, but **RDNA4 cards (RX 9000 series)** like the RX 9060 XT require special configuration. This guide covers both **ROCm** (native, best performance) and **Vulkan** (fallback, still great).

***

### Hardware Requirements

| GPU Series | Architecture | Backend | Status |
|------------|--------------|---------|--------|
| RX 6000–7000 | RDNA2/3 | ROCm | ✅ Fully supported |
| RX 9000 | RDNA4 | ROCm | ✅ Supported (GFX1200) |
| Integrated Radeon 780M | RDNA3 | ROCm/Vulkan | ✅ Supported |
| Older GCN cards | GCN/RDNA1 | Vulkan | ⚠️ Fallback only |

***

### Option 1: ROCm (Best Performance)

#### Step 1: Install ROCm Dependencies

**Arch/CachyOS:**
```bash
sudo pacman -S rocm-hip-sdk rocm-opencl-runtime
```

**Ubuntu/Debian:**
```bash
sudo apt install rocm-hip-sdk rocm-opencl-runtime
```

#### Step 2: Set GPU Environment Variables

For **RDNA4 (RX 9060 XT, RX 9070, etc.)**:
```bash
export HSA_OVERRIDE_GFX_VERSION=12.0.0
export ROCR_VISIBLE_DEVICES=0
export HIP_VISIBLE_DEVICES=0
```

For **RDNA3 (RX 7000 series)**:
```bash
export HSA_OVERRIDE_GFX_VERSION=11.0.0
export ROCR_VISIBLE_DEVICES=0
```

#### Step 3: Test GPU Detection

```bash
# Check Vulkan detection (works for all AMD GPUs)
vulkaninfo --summary | grep -i "deviceName"
# Should show: AMD Radeon RX 9060 XT (RADV GFX1200)

# Check ROCm detection
rocminfo | grep -i "name"  # May not work on all setups
```

#### Step 4: Run Ollama with ROCm

**Manual (testing):**
```bash
HSA_OVERRIDE_GFX_VERSION=12.0.0 ROCR_VISIBLE_DEVICES=0 ollama serve
```

**Systemd service (permanent):**
```bash
# Create user service directory
mkdir -p ~/.config/systemd/user

# Create service file
cat > ~/.config/systemd/user/ollama.service << EOF
[Unit]
Description=Ollama Service (User Mode)
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/ollama serve
Environment="HSA_OVERRIDE_GFX_VERSION=12.0.0"
Environment="ROCR_VISIBLE_DEVICES=0"
Environment="HIP_VISIBLE_DEVICES=0"
Restart=on-failure
RestartSec=3

[Install]
WantedBy=default.target
EOF

# Enable and start
systemctl --user daemon-reload
systemctl --user enable --now ollama

# Check status
systemctl --user status ollama
```

#### Step 5: Verify GPU Offload

```bash
# Run a model
ollama run qwen2.5-coder:7b-instruct "Hello" &
sleep 10

# Check GPU usage
ollama ps
# Should show: 70–90% GPU / 10–30% CPU
```

**Success indicators in logs:**
```
inference compute ... id=GPU-xxx library=ROCm compute=gfx1200
offloaded 39/49 layers to GPU
```

***

### Option 2: Vulkan (Fallback, Still Great)

If ROCm doesn't work or your GPU isn't fully supported:

#### Step 1: Enable Vulkan Backend

```bash
# Manual test
OLLAMA_VULKAN=1 GGML_VK_VISIBLE_DEVICES=0 ollama serve
```

#### Step 2: User Service with Vulkan

```bash
cat > ~/.config/systemd/user/ollama.service << EOF
[Unit]
Description=Ollama Service (User Mode)
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/ollama serve
Environment="OLLAMA_VULKAN=1"
Environment="GGML_VK_VISIBLE_DEVICES=0"
Restart=on-failure
RestartSec=3

[Install]
WantedBy=default.target
EOF

systemctl --user daemon-reload
systemctl --user enable --now ollama
```

#### Step 3: Verify

```bash
ollama ps
# Should show: 100% GPU (Vulkan) or GPU/CPU split
```

**Expected Performance:**
- ROCm: 20–50 tokens/sec (7B model), 10–25 tokens/sec (30B model)
- Vulkan: 15–40 tokens/sec (7B), 8–20 tokens/sec (30B)
- CPU-only: 1–5 tokens/sec (7B), 0.5–2 tokens/sec (30B)

***

### Modelfiles for AMD GPUs

#### Large Models (30B+) with Limited VRAM

```dockerfile
# Modelfile for qwen3-coder-30b-safe
FROM qwen3-coder:30b
PARAMETER num_ctx 32768
PARAMETER num_gpu 99
PARAMETER temperature 0.5
PARAMETER top_p 0.85
```

```bash
ollama create qwen3-coder-30b-safe -f Modelfile
```

This reduces context to fit in VRAM while maximizing GPU offload.

***

### AMD GPU Troubleshooting

#### Issue: "0% GPU, 100% CPU"

**Check:**
```bash
# Verify Vulkan works
vulkaninfo --summary | grep "deviceName"

# Check Ollama logs
journalctl --user -u ollama -f | grep -i "gpu\|rocm\|vulkan"
```

**Fix:**
```bash
# Try Vulkan fallback
OLLAMA_VULKAN=1 ollama serve
```

#### Issue: ROCm Crash on Startup

**Symptoms:** `rocblas_abort` or segmentation fault in logs.

**Fix:** Remove ROCm vars and use Vulkan:
```bash
# Edit user service
systemctl --user edit ollama

# Remove HSA_OVERRIDE_GFX_VERSION and ROCR_VISIBLE_DEVICES
# Keep only:
Environment="OLLAMA_VULKAN=1"
Environment="GGML_VK_VISIBLE_DEVICES=0"
```

#### Issue: Permission Denied on Home Directory

**Error:** `mkdir /home/christoph: permission denied`

**Cause:** System service running as `ollama` user can't access your home directory.

**Fix:** Use **user service** instead of system service:
```bash
# Stop system service
sudo systemctl stop ollama
sudo systemctl disable ollama

# Use user service (runs as your user)
systemctl --user enable --now ollama
```

***

## 🔥 User Service vs System Service

### Why User Service?

| Feature | System Service | User Service |
|---------|----------------|--------------|
| **Runs as** | `ollama` user (root-managed) | Your user account |
| **Model location** | `/var/lib/ollama/models/` | `~/.ollama/models/` |
| **Home dir access** | ❌ No (permission issues) | ✅ Yes |
| **Auto-start** | On boot | On login (or with linger) |
| **GPU access** | ⚠️ Complex setup | ✅ Automatic |
| **Best for** | Multi-user servers | Personal laptops/desktops |

**Recommendation:** Use **user service** for personal machines, especially with AMD GPUs.

***

### Setup User Service (Recommended)

#### Step 1: Disable System Service

```bash
sudo systemctl stop ollama
sudo systemctl disable ollama
# Optional: remove completely
sudo rm -f /etc/systemd/system/ollama.service
sudo rm -rf /etc/systemd/system/ollama.service.d/
sudo systemctl daemon-reload
```

#### Step 2: Create User Service

```bash
mkdir -p ~/.config/systemd/user

cat > ~/.config/systemd/user/ollama.service << EOF
[Unit]
Description=Ollama Service (User Mode)
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/ollama serve
Environment="OLLAMA_VULKAN=1"
Environment="GGML_VK_VISIBLE_DEVICES=0"
# Or for ROCm:
# Environment="HSA_OVERRIDE_GFX_VERSION=12.0.0"
# Environment="ROCR_VISIBLE_DEVICES=0"
Restart=on-failure
RestartSec=3

[Install]
WantedBy=default.target
EOF

# Enable and start
systemctl --user daemon-reload
systemctl --user enable --now ollama
```

#### Step 3: Optional – Enable Linger (24/7 Operation)

If you want Ollama running even when logged out:

```bash
sudo loginctl enable-linger christoph
```

**Check status:**
```bash
loginctl show-user christoph | grep Linger
# Should show: Linger=yes
```

**To disable later:**
```bash
sudo loginctl disable-linger christoph
```

#### Step 4: Verify

```bash
# Check service status
systemctl --user status ollama

# List models
ollama list

# Test GPU usage
ollama run qwen2.5-coder:7b-instruct "Hello" &
sleep 10
ollama ps
```

***

### User Service Commands

```bash
# Start/stop/restart
systemctl --user start ollama
systemctl --user stop ollama
systemctl --user restart ollama

# Check status
systemctl --user status ollama
systemctl --user is-active ollama

# View logs
journalctl --user -u ollama -f

# Reset failure counter (if stuck)
systemctl --user reset-failed ollama
```

***

## 🔥 Real-World Troubleshooting Cases

### Case 1: RX 9060 XT – 100% CPU, RAM Flooding, Crashes

**User:** Christoph, CachyOS, RX 9060 XT 16GB, 16GB RAM  
**Problem:** qwen3-coder:30b loads entirely into RAM, floods swap, crashes PC.

**Symptoms:**
```
offloading 0 repeating layers to GPU
offloaded 0/49 layers to GPU
model weights device=CPU size="17.3 GiB"
```

**Root Cause:** Ollama system service couldn't access home directory, ROCm not configured.

**Solution:**

1. **Switch to user service:**
```bash
sudo systemctl stop ollama
sudo systemctl disable ollama
systemctl --user enable --now ollama
```

2. **Enable ROCm for RDNA4:**
```bash
# Edit user service
systemctl --user edit ollama

# Add:
[Service]
Environment="HSA_OVERRIDE_GFX_VERSION=12.0.0"
Environment="ROCR_VISIBLE_DEVICES=0"
```

3. **Create safe Modelfile:**
```dockerfile
FROM qwen3-coder:30b
PARAMETER num_ctx 32768
PARAMETER num_gpu 99
PARAMETER temperature 0.5
EOF

ollama create qwen3-coder-30b-safe -f Modelfile
```

**Result:**
```
offloaded 39/49 layers to GPU
79% GPU / 21% CPU
No more crashes!
```

***

### Case 2: "Model Does Not Support Tools" Error

**User:** Trying to use Claude Code with deepseek-coder  
**Error:** `API Error: 400 {"error":{"message":"deepseek-coder:latest does not support tools"}}`

**Root Cause:** Base `deepseek-coder` lacks function/tool calling support required by agentic tools.

**Solution:**
```bash
# Use a compatible model
ollama pull qwen2.5-coder:7b-instruct

# Create 64K context Modelfile
cat > Modelfile << EOF
FROM qwen2.5-coder:7b-instruct
PARAMETER num_ctx 65536
EOF

ollama create qwen2.5-coder-7b-64k -f Modelfile

# Use with Claude Code
ollama launch claude --model qwen2.5-coder-7b-64k
```

**Compatible models for Claude Code:**
- ✅ `qwen2.5-coder:*-instruct` (all sizes)
- ✅ `qwen3-coder:*` (all sizes)
- ✅ `deepseek-coder-v2:*` (V2 only, not V1)
- ✅ `llama3.*-instruct`
- ❌ `deepseek-coder:latest` (V1, no tool support)
- ❌ `codellama:*` (limited tool support)

***

### Case 3: Models Disappear After Reinstall

**User:** Reinstalled Ollama, now `ollama list` shows nothing.

**Root Cause:** Models are in `~/.ollama/models/`, but system service looks in `/var/lib/ollama/models/`.

**Solution:**

**Option A: Point system service to home directory**
```bash
sudo systemctl edit ollama

# Add:
[Service]
Environment="OLLAMA_MODELS=/home/christoph/.ollama/models"
```

**Option B (Better): Use user service**
```bash
sudo systemctl stop ollama
sudo systemctl disable ollama
systemctl --user enable --now ollama
```

Models are automatically found in `~/.ollama/models/`.

***

### Case 4: Port Already in Use

**Error:** `listen tcp 127.0.0.1:11434: bind: address already in use`

**Cause:** Multiple Ollama instances running (manual + systemd).

**Fix:**
```bash
# Kill all instances
pkill -9 ollama

# Wait 2 seconds
sleep 2

# Start only one
systemctl --user start ollama
```

**Prevent recurrence:**
```bash
# Check what's running
ps aux | grep ollama
ss -tlnp | grep 11434

# Stop system service if using user service
sudo systemctl stop ollama
sudo systemctl disable ollama
```

***

### Case 5: Systemd Service Fails After Edit

**Error:** Service won't start after editing override.conf.

**Symptoms:**
```
Active: failed (Result: exit-code)
Start request repeated too quickly
```

**Fix:**
```bash
# Reset failure counter
systemctl --user reset-failed ollama

# Check for syntax errors
systemctl --user cat ollama

# Fix override file
systemctl --user edit ollama

# Restart
systemctl --user restart ollama

# View detailed logs
journalctl --user -u ollama -n 50 --no-pager
```

***

## 📚 Resources

### Official Links

- [Ollama Website](https://ollama.com)
- [Ollama GitHub](https://github.com/ollama/ollama)
- [Model Library](https://ollama.com/library)
- [Documentation](https://github.com/ollama/ollama/blob/main/docs/modelfile.md)
- [AMD GPU Guide](https://docs.ollama.com/gpu)

### Community

- [r/LocalLLaMA](https://reddit.com/r/LocalLLaMA)
- [r/Ollama](https://reddit.com/r/Ollama)
- [Ollama Discord](https://discord.gg/ollama)

### Model Recommendations

- [Open LLM Leaderboard](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard)
- [LMArena](https://lmarena.ai)
- [WhatLLM](https://whatllm.org)

### Tools & Extensions

- [Cline (VS Code)](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev)
- [Continue.dev](https://continue.dev)
- [Aider](https://aider.chat)
- [Open WebUI](https://openwebui.com)

### AMD-Specific Resources

- [ROCm Documentation](https://rocm.docs.amd.com)
- [RDNA4 GFX1200 Support Thread](https://github.com/ollama/ollama/issues/8679)
- [Vulkan Backend Guide](https://github.com/ggerganov/llama.cpp/blob/master/docs/build-vulkan.md)

***

## 🤝 Contributing

Contributions welcome! Please feel free to submit a Pull Request.

1. Fork the repo
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

***

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

***

## 🙏 Acknowledgments

- [Ollama Team](https://ollama.com) for making local AI accessible
- [Meta AI](https://ai.meta.com) for Llama models
- [Alibaba Cloud](https://www.alibabacloud.com) for Qwen models
- [DeepSeek](https://deepseek.com) for coding models
- [AMD](https://amd.com) for ROCm and open GPU drivers
- The entire open-source AI community

***

<div align="center">

**Made with ❤️ by the Local AI Community**

*Special thanks to real-world users whose troubleshooting journeys made this guide possible.*

[⬆ Back to Top](#-local-ai-models-with-ollama)

</div>
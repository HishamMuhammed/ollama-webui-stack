Here’s your **updated README** with the cross-platform port fix cleanly integrated into the *Troubleshooting* section 👇

---

# 🧠 AI Stack (Ollama + Open WebUI + SearXNG)

Run a fully local AI stack with a clean web interface, local LLMs, and private web search — all in Docker.

![Docker](https://img.shields.io/badge/docker-ready-blue)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 📸 Preview

<!-- Add screenshots in /assets folder -->

![Open WebUI](./assets/webui.png)

---

## ✨ Features

* 🧠 Run local LLMs via Ollama
* 🌐 Clean UI with Open WebUI
* 🔍 Private web search using SearXNG
* ⚡ Works on NVIDIA, AMD, Intel iGPU, CPU, and macOS
* 📱 Accessible from phone (same network or Tailscale)
* 💾 Persistent storage (models & chat history)

---

## 🚀 Quick Start (5 Minutes)

### 1. Clone the repo

```bash
git clone https://github.com/yourusername/aistack
cd aistack
```

---

### 2. Configure (optional but recommended)

```bash
cp .env.example .env
```

---

### 3. Start the stack

#### 🟢 CPU (default)

```bash
docker compose up -d
```

#### 🟢 NVIDIA (CUDA)

```bash
docker compose -f docker-compose.yml -f docker-compose.nvidia.yml up -d
```

#### 🟢 AMD / Intel iGPU

```bash
docker compose -f docker-compose.yml -f docker-compose.amd.yml up -d
```

### ⚠️ AMD GPU Configuration (ROCm)

If your GPU is not officially supported, you can override values:

#### 🔹 RX 6000 series (RDNA2)
HSA_OVERRIDE_GFX_VERSION=10.3.0  
HCC_AMDGPU_TARGET=gfx1030  

#### 🔹 RX 7000 series (RDNA3)
HSA_OVERRIDE_GFX_VERSION=11.0.0  
HCC_AMDGPU_TARGET=gfx1100  

#### 🔹 Older GPUs (experimental)
HSA_OVERRIDE_GFX_VERSION=9.0.0  
HCC_AMDGPU_TARGET=gfx900  


### 🔍 Find your GPU target

Run:

```bash
rocminfo | grep gfx
```
---

### 4. Open Web UI

```
http://localhost:3000
```

---

### 5. Download your first model

```bash
docker exec -it ollama ollama pull phi3
```

---

## 🍎 macOS Setup

Docker cannot access GPU on macOS — run Ollama natively:

```bash
brew install ollama
ollama serve
```

Then run:

```bash
docker compose up -d open-webui searxng
```

---

## 🌐 Services

| Service    | URL                                            |
| ---------- | ---------------------------------------------- |
| Open WebUI | [http://localhost:3000](http://localhost:3000) |
| SearXNG    | [http://localhost:8888](http://localhost:8888) |

---

## 📱 Access from Phone

### Same WiFi

Find your PC IP:

```bash
ip a
```

Open on phone:

```
http://<your-ip>:3000
```

---

### 🌍 Remote Access (Recommended)

Use Tailscale:

```bash
tailscale ip -4
```

Then open:

```
http://<tailscale-ip>:3000
```

---

## 📦 Common Commands

| Action  | Command                  |
| ------- | ------------------------ |
| Start   | `docker compose up -d`   |
| Stop    | `docker compose down`    |
| Restart | `docker compose restart` |
| Logs    | `docker compose logs -f` |
| Status  | `docker compose ps`      |

---

## 🧠 Model Management

### List models

```bash
docker exec ollama ollama list
```

### Download model

```bash
docker exec -it ollama ollama pull mistral:7b
```

### Run model (CLI)

```bash
docker exec -it ollama ollama run phi3
```

---

## ⚡ Recommended Models (8GB VRAM)

| Model       | Use Case      |
| ----------- | ------------- |
| phi3        | Fast, general |
| gemma2:2b   | Lightweight   |
| mistral:7b  | Balanced      |
| llama3.2:3b | Coding        |
| qwen2.5:7b  | All-rounder   |

> ⚠️ Avoid models larger than 7B on 8GB VRAM systems

---

## ⚙️ Configuration

Optional `.env` overrides:

```env
WEBUI_PORT=3000
SEARXNG_PORT=8888
```

---

## 🔐 Security

* Your UI is accessible to anyone on your local network
* Do NOT expose ports (3000/11434) publicly
* Enable authentication inside Open WebUI
* Prefer Tailscale for remote access

---

## ⚠️ Known Limitations

* AMD GPU support depends on ROCm compatibility
* macOS does not support GPU acceleration in Docker
* Large models (>7B) may be slow on 8GB VRAM systems

---

## 💡 First Run Tip

The first response may be slow because the model is loading into memory.
Subsequent responses will be significantly faster.

---

## 🛠 Troubleshooting

### Check logs

```bash
docker compose logs -f
```

---

### ⚠️ Port Already in Use (Ollama Conflict)

If you encounter an error like:

```
failed to bind port 11434: address already in use
```

This usually means Ollama is already running on your system.

---

#### 🔍 Check what’s using the port

**Linux / macOS**

```bash
lsof -i :11434
```

**Windows (PowerShell)**

```powershell
netstat -ano | findstr :11434
```

---

#### 🛑 Stop or kill the process

**Linux (system service)**

```bash
sudo systemctl stop ollama
sudo systemctl disable ollama
```

**Linux (quick fix)**

```bash
sudo fuser -k 11434/tcp
```

**macOS**

```bash
kill -9 $(lsof -t -i:11434)
```

**Windows (PowerShell)**

```powershell
taskkill /PID <PID> /F
```

---

#### 💡 Alternative (recommended)

Instead of stopping Ollama, change the port in `docker-compose.yml`:

```yaml
ports:
  - "11435:11434"
```

---

After resolving the conflict, restart:

```bash
docker compose up -d
```
---
### ❌ Containers cannot access internet (Linux)

Error:
Temporary failure resolving / network is unreachable

Fix (firewalld):

sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload
sudo systemctl restart docker

---

### Reset everything

```bash
docker compose down -v
```

---

## 📂 Project Structure

```
aistack/
├── docker-compose.yml
├── docker-compose.nvidia.yml
├── docker-compose.amd.yml
├── .env.example
├── docs/
│   ├── setup.md
│   ├── usage.md
│   ├── remote-access.md
│   ├── troubleshooting.md
│   └── performance.md
└── searxng/
```

---

## 📚 Documentation

* Setup → `docs/setup.md`
* Usage → `docs/usage.md`
* Remote Access → `docs/remote-access.md`
* Troubleshooting → `docs/troubleshooting.md`
* Performance → `docs/performance.md`

---

## 🧠 Architecture

```
[ Phone / Browser ]
          ↓
    Open WebUI (3000)
          ↓
       Ollama (11434)
          ↓
        Models
          ↓
       GPU / CPU

+ SearXNG (8888) for web search
```

---

## 🚀 Future Improvements

* HTTPS + domain support
* Reverse proxy (Caddy / Nginx)
* Auto model download
* Multi-user setup

---

## ⭐ Contributing

Feel free to open issues, suggest improvements, or fork the project.

---

## 📜 License

MIT License

---

If you want, next we can:

* fix that `version` warning
* make ports configurable via `.env`
* or give this README a 🔥 “top GitHub project” polish (badges, gifs, sections, etc.)

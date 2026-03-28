# 🧠 AI Stack (Ollama + Open WebUI + SearXNG)

Run a fully local AI stack with a clean web interface, local LLMs, and private web search — all in Docker.

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

### 2. Start the stack

```bash
docker compose up -d
```

---

### 3. Open Web UI

Open in your browser:

```
http://localhost:3000
```

---

### 4. Download your first model

```bash
docker exec -it ollama ollama pull phi3
```

---

## 🧩 GPU Support

### 🟢 CPU (default)

```bash
docker compose up -d
```

---

### 🟢 NVIDIA (CUDA)

Requirements:

* NVIDIA GPU
* NVIDIA Container Toolkit

```bash
docker compose -f docker-compose.yml -f docker-compose.nvidia.yml up -d
```

---

### 🟢 AMD / Intel iGPU

```bash
docker compose -f docker-compose.yml -f docker-compose.amd.yml up -d
```

---

### 🍎 macOS

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

| Service    | URL                   |
| ---------- | --------------------- |
| Open WebUI | http://localhost:3000 |
| SearXNG    | http://localhost:8888 |

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

## 🔒 Security Notes

* Your UI is accessible to anyone on your local network
* Enable authentication inside Open WebUI
* Avoid exposing ports directly to the internet
* Prefer Tailscale for remote access

---

## 🛠 Troubleshooting

### Check logs

```bash
docker compose logs -f
```

---

### Port already in use

```bash
sudo lsof -i :3000
```

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

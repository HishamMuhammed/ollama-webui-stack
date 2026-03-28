Here’s your **cleaned, professional version** — no emojis, no fluff, just the unified + full guide.

---

# AI Stack Docker Management Guide (Unified Edition)

Complete guide for managing your Ollama + Open WebUI + SearXNG stack across NVIDIA, AMD, Intel iGPU, CPU, and macOS.
Based on your original guide with unified cross-platform improvements 

---

# Quick Reference

| Action         | Command                                       |
| -------------- | --------------------------------------------- |
| Start all      | `docker compose up -d`                        |
| Stop all       | `docker compose down`                         |
| Restart all    | `docker compose restart`                      |
| Check status   | `docker compose ps`                           |
| View logs      | `docker compose logs -f`                      |
| Update images  | `docker compose pull && docker compose up -d` |
| List models    | `docker exec ollama ollama list`              |
| Download model | `docker exec -it ollama ollama pull phi3`     |

---

# 0. Unified Setup

This stack supports:

* NVIDIA (CUDA)
* AMD / Intel iGPU (`/dev/dri`)
* CPU-only
* macOS (native Ollama)

---

## docker-compose.yml

```yaml
version: "3.9"

name: aistack

services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped
    profiles: ["local-gpu", "cpu"]
    ports:
      - "11434:11434"
    volumes:
      - ollama:/root/.ollama

    deploy:
      resources:
        reservations:
          devices:
            - driver: ${GPU_DRIVER:-none}
              count: ${GPU_COUNT:-0}
              capabilities: [gpu]

    devices:
      - ${GPU_DEVICE_PATH:-/dev/null}

    environment:
      - OLLAMA_KEEP_ALIVE=10m

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    restart: unless-stopped
    depends_on:
      - ollama
    ports:
      - "3000:8080"
    volumes:
      - open-webui:/app/backend/data
    environment:
      - OLLAMA_BASE_URL=${OLLAMA_BASE_URL:-http://ollama:11434}

  searxng:
    image: searxng/searxng:latest
    container_name: searxng
    restart: unless-stopped
    ports:
      - "8888:8080"
    volumes:
      - ./searxng:/etc/searxng

volumes:
  ollama:
  open-webui:
```

---

## Environment Configuration (.env)

### NVIDIA

```env
GPU_DRIVER=nvidia
GPU_COUNT=1
GPU_DEVICE_PATH=/dev/null
```

---

### AMD / Intel iGPU

```env
GPU_DRIVER=none
GPU_COUNT=0
GPU_DEVICE_PATH=/dev/dri:/dev/dri
```

---

### CPU

```env
GPU_DRIVER=none
GPU_COUNT=0
GPU_DEVICE_PATH=/dev/null
```

---

### macOS

```env
OLLAMA_BASE_URL=http://host.docker.internal:11434
```

Run:

```bash
docker compose up -d open-webui searxng
```

Install Ollama natively:

```bash
brew install ollama
ollama serve
```

---

# 1. Starting & Stopping the Stack

### Start Everything

```bash
cd ~/aistack
docker compose up -d
```

### Stop Everything

```bash
docker compose down
```

### Stop Individual Services

```bash
docker compose stop ollama
docker compose stop open-webui
docker compose stop searxng
```

### Start Individual Services

```bash
docker compose start ollama
docker compose start open-webui
docker compose start searxng
```

### Restart a Service

```bash
docker compose restart ollama
```

---

# 2. Viewing Status & Logs

### Check Status

```bash
docker compose ps
```

### View Logs

```bash
docker compose logs -f
```

### Service Logs

```bash
docker compose logs -f ollama
docker compose logs -f open-webui
docker compose logs -f searxng
```

### Resource Usage

```bash
docker stats
```

---

# 3. Managing Ollama Models

### List Models

```bash
docker exec ollama ollama list
```

### Download Models

```bash
docker exec -it ollama ollama pull phi3
docker exec -it ollama ollama pull mistral:7b
docker exec -it ollama ollama pull llama3.2
```

### Run Model

```bash
docker exec -it ollama ollama run phi3
```

### Delete Model

```bash
docker exec ollama ollama rm phi3
```

### Model Info

```bash
docker exec ollama ollama show phi3
```

---

## Recommended Models (8GB VRAM)

| Model       | Use Case      |
| ----------- | ------------- |
| phi3        | Fast, general |
| phi3:mini   | Faster        |
| gemma2:2b   | Lightweight   |
| mistral:7b  | Balanced      |
| llama3.2:3b | Coding        |
| qwen2.5:7b  | All-rounder   |

Avoid models larger than 7B on 8GB VRAM systems.

---

# 4. Updating the Stack

```bash
cd ~/aistack
docker compose pull
docker compose up -d
```

---

# 5. Backup & Restore

### Backup Ollama Models

```bash
docker run --rm -v ollama:/data -v $(pwd):/backup alpine \
tar czf /backup/ollama-backup.tar.gz -C /data .
```

### Backup Open WebUI Data

```bash
docker run --rm -v open-webui:/data -v $(pwd):/backup alpine \
tar czf /backup/open-webui-backup.tar.gz -C /data .
```

### Backup SearXNG Config

```bash
cp -r ~/aistack/searxng ~/aistack/searxng-backup
```

---

### Restore Ollama Models

```bash
docker run --rm -v ollama:/data -v $(pwd):/backup alpine \
sh -c "cd /data && tar xzf /backup/ollama-backup.tar.gz"
```

### Restore Open WebUI Data

```bash
docker run --rm -v open-webui:/data -v $(pwd):/backup alpine \
sh -c "cd /data && tar xzf /backup/open-webui-backup.tar.gz"
```

---

# 6. Troubleshooting

## GPU Not Detected

### NVIDIA

```bash
docker exec ollama nvidia-smi
```

Ensure NVIDIA Container Toolkit is installed.

---

### AMD / Intel

```bash
ls -la /dev/dri
```

```bash
sudo usermod -aG render $USER
```

---

### macOS

Docker GPU acceleration is not supported. Use native Ollama.

---

## Port Already in Use

```bash
sudo lsof -i :3000
sudo lsof -i :8888
```

---

## Container Restart Issues

```bash
docker compose logs --tail 50 ollama
```

---

## Reset Everything

```bash
docker compose down -v
```

---

# 7. Useful Docker Commands

```bash
docker exec -it ollama bash
docker exec -it open-webui bash
docker exec -it searxng sh
docker volume ls
docker system prune
```

---

# 8. SearXNG Integration

Access:

```
http://localhost:8888
```

Open WebUI configuration:

* Settings → Tools
* Enable Web Search
* URL: `http://searxng:8080` or `http://localhost:8888`

---

# 9. Performance Tips

## GPU Monitoring

### NVIDIA

```bash
watch -n 1 nvidia-smi
```

### AMD

```bash
radeontop
```

---

## Optimization

* Use quantized models (`q4_k_m`, `q5_k_m`)
* Enable "Keep Model Loaded"
* Adjust context window size

---

# 10. File Locations

| Item            | Location                         |
| --------------- | -------------------------------- |
| Docker Compose  | `~/aistack/docker-compose.yml`   |
| SearXNG Config  | `~/aistack/searxng/settings.yml` |
| Ollama Models   | Docker volume `ollama`           |
| Open WebUI Data | Docker volume `open-webui`       |

---

# 11. Common Workflows

### Daily Startup

```bash
cd ~/aistack && docker compose up -d
```

### Shutdown

```bash
docker compose down
```

---

### Try New Model

```bash
docker exec -it ollama ollama pull llama3.2:3b
docker exec -it ollama ollama run llama3.2:3b
```

---

### Clean Disk Space

```bash
docker exec ollama ollama rm old-model
docker image prune -f
```

---

### Move to Another PC

1. Backup volumes
2. Copy `~/aistack`
3. Run `docker compose up -d`
4. Restore backups

---

---
---

# AI Stack Docker Management Guide

> Complete guide for managing your Ollama + Open WebUI + SearXNG Docker stack

---

## Quick Reference

| Action | Command |
|--------|---------|
| Start all | `docker compose up -d` |
| Stop all | `docker compose down` |
| Restart all | `docker compose restart` |
| Check status | `docker compose ps` |
| View logs | `docker compose logs -f` |
| Update images | `docker compose pull && docker compose up -d` |
| List models | `docker exec ollama ollama list` |
| Download model | `docker exec -it ollama ollama pull phi3` |

---

## 1. Starting & Stopping the Stack

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

## 2. Viewing Status & Logs

### Check What's Running

```bash
docker compose ps
```

Expected output:

```
NAME         STATUS    PORTS
ollama       Up        11434/tcp
open-webui   Up        0.0.0.0:3000->8080/tcp
searxng      Up        0.0.0.0:8888->8080/tcp
```

### View All Logs (Live)

```bash
docker compose logs -f
```

Press `Ctrl+C` to exit.

### View Logs for Specific Service

```bash
docker compose logs -f ollama
docker compose logs -f open-webui
docker compose logs -f searxng
```

### View Last N Lines

```bash
docker compose logs --tail 100 ollama
```

### Check Container Resource Usage

```bash
docker stats
```

Press `Ctrl+C` to exit.

---

## 3. Managing Ollama Models

### List Downloaded Models

```bash
docker exec ollama ollama list
```

### Download a Model

```bash
docker exec -it ollama ollama pull phi3
docker exec -it ollama ollama pull mistral:7b
docker exec -it ollama ollama pull llama3.2
```

### Chat with a Model (Terminal)

```bash
docker exec -it ollama ollama run phi3
```

Type `/bye` to exit.

### Delete a Model

```bash
docker exec ollama ollama rm phi3
```

### Get Model Info

```bash
docker exec ollama ollama show phi3
```

### Recommended Models for RX 6600 (8GB VRAM)

| Model | Size | Use Case |
|-------|------|----------|
| `phi3` | ~2GB | Fast, general purpose |
| `phi3:mini` | ~2GB | Even faster |
| `gemma2:2b` | ~1.6GB | Very fast |
| `mistral:7b` | ~4GB | Balanced |
| `llama3.2:3b` | ~2GB | Good for coding |
| `qwen2.5:7b` | ~4GB | Good all-rounder |

> ⚠️ Avoid models larger than 7B - they'll be slow and may not fit in VRAM.

---

## 4. Updating the Stack

### Update All Images

```bash
cd ~/aistack
docker compose pull
docker compose up -d
```

### Update a Specific Service

```bash
docker compose pull ollama
docker compose up -d ollama
```

### Check Current Image Versions

```bash
docker compose images
```

### Remove Old Images (Cleanup)

```bash
docker image prune -f
```

---

## 5. Backup & Restore

### Backup Ollama Models

```bash
docker run --rm -v ollama:/data -v $(pwd):/backup alpine tar czf /backup/ollama-backup.tar.gz -C /data .
```

### Backup Open WebUI Data

```bash
docker run --rm -v open-webui:/data -v $(pwd):/backup alpine tar czf /backup/open-webui-backup.tar.gz -C /data .
```

### Backup SearXNG Config

```bash
cp -r ~/aistack/searxng ~/aistack/searxng-backup
```

Or:

```bash
tar czf searxng-backup.tar.gz -C ~/aistack searxng
```

### Restore Ollama Models

```bash
docker run --rm -v ollama:/data -v $(pwd):/backup alpine sh -c "cd /data && tar xzf /backup/ollama-backup.tar.gz"
```

### Restore Open WebUI Data

```bash
docker run --rm -v open-webui:/data -v $(pwd):/backup alpine sh -c "cd /data && tar xzf /backup/open-webui-backup.tar.gz"
```

---

## 6. Troubleshooting

### Container Won't Start

```bash
docker compose logs ollama
```

Check for error messages.

### GPU Not Detected

```bash
docker exec ollama rocminfo
```

If this fails, check:

1. Docker has access to GPU devices:

```bash
ls -la /dev/kfd /dev/dri
```

2. User is in `video` and `render` groups:

```bash
groups
```

If not:

```bash
sudo usermod -aG video,render $USER
```

Then reboot.

### Port Already in Use

Find what's using the port:

```bash
sudo lsof -i :3000
sudo lsof -i :8888
```

Kill the process or change the port in `docker-compose.yml`.

### Container Keeps Restarting

Check logs:

```bash
docker compose logs --tail 50 ollama
```

### Volume Warnings

If you see:

```
volume "ollama" already exists but was not created by Docker Compose
```

This is normal. It means the volume already existed. To suppress, add to `docker-compose.yml`:

```yaml
volumes:
  ollama:
    external: true
  open-webui:
    external: true
```

### Reset a Service (Keep Data)

```bash
docker compose down ollama
docker compose up -d ollama
```

### Reset Everything (WARNING: Deletes Data)

```bash
docker compose down -v
```

### Recreate a Container

```bash
docker compose up -d --force-recreate ollama
```

---

## 7. Useful Docker Commands

### Enter Container Shell

```bash
docker exec -it ollama bash
docker exec -it open-webui bash
docker exec -it searxng sh
```

### Copy File from Container

```bash
docker cp ollama:/root/.ollama/models ./models-backup
```

### Copy File to Container

```bash
docker cp ./config.json open-webui:/app/backend/data/
```

### List All Volumes

```bash
docker volume ls
```

### Inspect a Volume

```bash
docker volume inspect ollama
```

### Clean Up Unused Resources

```bash
docker system prune
```

This removes:
- Stopped containers
- Unused networks
- Dangling images
- Build cache

### Clean Up Everything (Including Volumes)

```bash
docker system prune -a --volumes
```

> ⚠️ This will delete all your models and chat history!

### Check Docker Disk Usage

```bash
docker system df
```

---

## 8. SearXNG Integration

### Access SearXNG

Open in browser:

```
http://localhost:8888
```

### Use with Open WebUI

In Open WebUI settings:

1. Go to Settings → Tools
2. Enable Web Search
3. Set search engine to SearXNG
4. URL: `http://searxng:8080` (internal) or `http://localhost:8888` (external)

### Check SearXNG Status

```bash
curl http://localhost:8888/healthz
```

---

## 9. Performance Tips

### GPU Optimization

Your RX 6600 uses ROCm with workaround:

```yaml
environment:
  - HSA_OVERRIDE_GFX_VERSION=10.3.0
  - HCC_AMDGPU_TARGET=gfx1030
```

### Model Loading Speed

First response is always slow because:
1. Model loads from disk to VRAM
2. Model initializes

Keep models loaded in memory (Open WebUI setting):
- Settings → Models → Keep Model Loaded

### Reduce Memory Usage

Use quantized models:

```bash
docker exec -it ollama ollama pull phi3:q4_k_m
docker exec -it ollama ollama pull mistral:7b-q4_K_M
```

### Monitor GPU Usage

```bash
watch -n 1 radeontop
```

Or:

```bash
rocm-smi
```

### Context Window Size

Adjust in Open WebUI:
- Settings → Models → Context Window
- Lower = faster, less memory
- Higher = more context, slower

---

## 10. Quick Cheat Sheet

Print this and keep near your desk:

```
┌─────────────────────────────────────────────────────────────────┐
│                    AI STACK CHEAT SHEET                         │
├─────────────────────────────────────────────────────────────────┤
│ START        docker compose up -d                               │
│ STOP         docker compose down                                │
│ RESTART      docker compose restart                             │
│ STATUS       docker compose ps                                  │
│ LOGS         docker compose logs -f [service]                   │
│                                                                 │
│ MODELS LIST  docker exec ollama ollama list                     │
│ MODEL PULL   docker exec -it ollama ollama pull <model>         │
│ MODEL RUN    docker exec -it ollama ollama run <model>          │
│ MODEL DELETE docker exec ollama ollama rm <model>               │
│                                                                 │
│ UPDATE       docker compose pull && docker compose up -d        │
│ SHELL        docker exec -it <container> bash                   │
│ CLEANUP      docker system prune -f                             │
│                                                                 │
│ URLs:                                                           │
│   Open WebUI → http://localhost:3000                            │
│   SearXNG    → http://localhost:8888                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## 11. File Locations

| What | Location |
|------|----------|
| Docker Compose | `~/aistack/docker-compose.yml` |
| SearXNG Config | `~/aistack/searxng/settings.yml` |
| Ollama Models | Docker volume `ollama` |
| Open WebUI Data | Docker volume `open-webui` |

### Find Volume Location on Disk

```bash
docker volume inspect ollama --format '{{ .Mountpoint }}'
```

---

## 12. Common Workflows

### Daily Startup

```bash
cd ~/aistack && docker compose up -d
```

### End of Day

```bash
docker compose down
```

Or just leave it running with `restart: unless-stopped`.

### Try a New Model

```bash
docker exec -it ollama ollama pull llama3.2:3b
docker exec -it ollama ollama run llama3.2:3b
```

Then select it in Open WebUI.

### Clean Up Disk Space

```bash
docker exec ollama ollama rm old-model
docker image prune -f
```

### Move Stack to Another PC

1. Backup volumes (see section 5)
2. Copy `~/aistack` folder
3. On new PC: `docker compose up -d`
4. Restore volumes

---

## 13. Getting Help

### Check Logs First

```bash
docker compose logs -f
```

### Useful Resources

- Ollama docs: https://github.com/ollama/ollama
- Open WebUI docs: https://docs.openwebui.com
- SearXNG docs: https://docs.searxng.org
- Docker Compose docs: https://docs.docker.com/compose/

### GPU Issues

- ROCm docs: https://rocm.docs.amd.com
- Ollama ROCm: https://github.com/ollama/ollama/blob/main/docs/gpu/rocm.md

---

> Last updated: 2026-03-24
> 
> Stack location: `~/aistack`

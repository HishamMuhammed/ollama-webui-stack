# 🛠 Troubleshooting

---

## ❌ Container Not Starting

```bash
docker compose logs ollama
```

---

## 🔌 Port Already in Use

### Linux / macOS

```bash
sudo lsof -i :3000
```

---

### Windows

```powershell
netstat -ano | findstr :3000
```

---

## 🧠 GPU Not Detected

### NVIDIA

```bash
docker exec ollama nvidia-smi
```

---

### AMD / Intel

```bash
ls /dev/dri
```

---

## 🔄 Restart Loop

```bash
docker compose logs --tail 50
```

---

## 💾 Disk Usage

```bash
docker system df
docker volume prune
```

---

## ⚠️ Reset Everything

```bash
docker compose down -v
```

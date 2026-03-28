# 🧠 Usage Guide

Works the same across all platforms.

---

## ▶️ Start / Stop

Start:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

Restart:

```bash
docker compose restart
```

---

## 📊 Check Status

```bash
docker compose ps
```

---

## 📜 View Logs

```bash
docker compose logs -f
```

---

## 🧠 Model Management

List:

```bash
docker exec ollama ollama list
```

Pull:

```bash
docker exec -it ollama ollama pull phi3
```

Run:

```bash
docker exec -it ollama ollama run phi3
```

---

## 🌐 Access UI

| Platform | URL                   |
| -------- | --------------------- |
| Local    | http://localhost:3000 |
| SearXNG  | http://localhost:8888 |

---

## 🪟 Windows Note

If using PowerShell:

* Replace `$USER` with your username
* Use **Docker Desktop terminal** or WSL

---

## 🔄 Update Stack

```bash
docker compose pull
docker compose up -d
```

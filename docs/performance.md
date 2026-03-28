# ⚡ Performance Guide

---

## 🧠 Recommended Models (8GB VRAM)

* phi3
* gemma2:2b
* mistral:7b
* llama3.2:3b
* qwen2.5:7b

---

## ⚡ Use Quantized Models

```bash
docker exec -it ollama ollama pull phi3:q4_k_m
```

---

## 🚀 Improve Speed

* Enable "Keep Model Loaded" in Open WebUI
* Reduce context window
* Use smaller models

---

## 📊 Monitor GPU

### NVIDIA

```bash
watch -n 1 nvidia-smi
```

---

### AMD

```bash
radeontop
```

---

## 🪟 Windows Tip

Use Task Manager → GPU tab to monitor usage

---

## 💡 General Tips

* First response is slower (model loading)
* SSD improves performance
* More RAM = better multitasking

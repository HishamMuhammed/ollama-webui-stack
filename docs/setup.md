# ⚙️ Setup Guide (All Platforms)

This guide helps you install and run the AI stack on Linux, Windows, and macOS.

---

## 🧾 Requirements

* Docker
* Docker Compose

---

# 🐳 Install Docker

## 🐧 Linux

### Ubuntu / Debian

```bash
sudo apt update
sudo apt install docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
```

---

### Fedora

```bash
sudo dnf install docker docker-compose
sudo systemctl enable docker
sudo systemctl start docker
```

---

### Arch Linux

```bash
sudo pacman -S docker docker-compose
sudo systemctl enable docker
sudo systemctl start docker
```

---

### Add user to docker group (all Linux)

```bash
sudo usermod -aG docker $USER
```

Reboot or log out after this.

---

## 🪟 Windows

Install Docker Desktop:
https://www.docker.com/products/docker-desktop/

Requirements:

* Windows 10/11
* WSL2 enabled

---

## 🍎 macOS

Install Docker Desktop:
https://www.docker.com/products/docker-desktop/

---

# 🚀 Start the Stack

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

---

# 🧩 GPU Setup

## 🟢 NVIDIA (Linux only)

Install NVIDIA Container Toolkit:

### Ubuntu / Debian

```bash
sudo apt install nvidia-container-toolkit
```

### Fedora

```bash
sudo dnf install nvidia-container-toolkit
```

### Arch

```bash
sudo pacman -S nvidia-container-toolkit
```

Run:

```bash
docker compose -f docker-compose.yml -f docker-compose.nvidia.yml up -d
```

---

## 🟢 AMD / Intel iGPU (Linux)

```bash
sudo usermod -aG render $USER
```

Reboot.

Run:

```bash
docker compose -f docker-compose.yml -f docker-compose.amd.yml up -d
```

---

## 🟢 CPU

```bash
docker compose up -d
```

---

## 🍎 macOS GPU Note

Docker cannot use GPU on macOS.

Install Ollama natively:

```bash
brew install ollama
ollama serve
```

Then run:

```bash
docker compose up -d open-webui searxng
```

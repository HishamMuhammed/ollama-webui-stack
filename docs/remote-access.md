# 🌐 Remote Access

---

## 📱 Local Network (All Platforms)

### 🐧 Linux / macOS

```bash
ip a
```

---

### 🪟 Windows

```powershell
ipconfig
```

Look for:

```text
IPv4 Address: 192.168.x.x
```

---

## Access from phone

```
http://<your-ip>:3000
```

---

## 🌍 Remote Access (Recommended)

Use Tailscale:

https://tailscale.com/download

Install on both devices.

Get IP:

```bash
tailscale ip -4
```

Open:

```
http://<tailscale-ip>:3000
```

---

## 🔐 Security

* Anyone on your network can access your UI
* Do NOT expose ports publicly
* Use Tailscale instead of port forwarding
* Enable Open WebUI authentication

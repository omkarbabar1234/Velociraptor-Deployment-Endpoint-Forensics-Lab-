# ⭐ Velociraptor Deployment & Endpoint Forensics Lab
*Complete SOC / DFIR project*

![Project Banner](screenshots/banner.png)

---

## 👤 Author
**Babar Omkar Sukhdev**  
Modern College, Nanded

---

## 📌 Objective
**Install and configure Velociraptor on Linux** and deploy Linux + Windows clients to enable:

- **Endpoint monitoring**
- **Forensic artifact collection**
- **Threat hunting with VQL**
- **Live response operations**

This project reproduces a real SOC/DFIR workflow.

---

## 🖥️ Lab Setup & Requirements
**Server OS:** Ubuntu 20.04 / 22.04  
**Resources:** 4 vCPU, 8 GB RAM, 50 GB disk  
**Network:** Internet required for downloads  
**Ports:** `8000` (frontend), `8889` (GUI) — ensure allowed in firewall

**Components:**
- Velociraptor Server (Linux)  
- Velociraptor Linux client (.deb)  
- Velociraptor Windows client (.exe)  
- Optional: python3 http.server for transfers

---

---

## ⚙️ Server Installation (Linux)

### ### Step 1 — Prepare the Environment
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install wget openssl -y
sudo mkdir -p /opt/velociraptor
cd /opt/velociraptor
 ```

![Step 1 — Prepare Environment](screenshots/step1.png)

---
### Step 2 — Download Velociraptor Binary
```bash
sudo wget https://github.com/Velocidex/velociraptor/releases/download/v0.73/velociraptor-v0.73.2-linux-amd64
sudo mv velociraptor-v0.73.2-linux-amd64 velociraptor
sudo chmod +x velociraptor
```

![Step 2 — Download Binary](screenshots/step2.png)

---
### Step 3 — Interactive Configuration
```bash
sudo /opt/velociraptor/velociraptor config generate -i
 ```
**Suggested answers:**

•	OS: Linux

•	Datastore: /opt/velociraptor

•	SSL: Self-signed

•	Frontend port: 8000

•	GUI port: 8889

•	WebSocket: y

•	Admin username: 

•	Logsdir: /opt/velociraptor/


•	Save as: server.config.yaml


![Interactive Config](screenshots/interactive_setup.png)

---
### Step 4 — Update server.config.yaml

**Edit and set bind address and ports:**
```bash
frontend:
  bind_address: <SERVER_IP>
  frontend_port: 8000
  gui_port: 8889
```
![Config Update](screenshots/config_update.png)

---

### Step 5 — Build & Install Debian Package

**Create server package:**
```
./velociraptor --config /opt/velociraptor/server.config.yaml \
  debian server --binary /opt/velociraptor/velociraptor
```

**Install the .deb:**
```
sudo dpkg -i velociraptor-server_<version>_amd64.deb
sudo systemctl status velociraptor_server.service
```
![dpkg install](screenshots/dpkg_install.png)

---
### 🐧 Linux Client Deployment

**client.config.yaml:**
```
server_urls:
  - wss://<SERVER_IP>:8000/
```

**Create client package:**
```
./velociraptor --config /opt/velociraptor/client.config.yaml debian client
```

**Transfer & install:**
```
scp velociraptor_client_<version>.deb user@client:/tmp/
ssh user@client
sudo dpkg -i /tmp/velociraptor_client_<version>.deb
sudo systemctl status velociraptor_client.service
```
![Linux Client](screenshots/linux_client.png)

---



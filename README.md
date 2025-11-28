# ⭐ Velociraptor Deployment & Endpoint Forensics Lab
*Complete SOC / DFIR project*

![Project Banner](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/6f380d0c5c627011529a225dba06652d2a195872/screenshots/a1e8f366-5f04-4748-8ed7-ffa5470729cf.png)

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

## ⚙️ Server Installation (Linux)

### ### Step 1 — Prepare the Environment
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install wget openssl -y
sudo mkdir -p /opt/velociraptor
cd /opt/velociraptor
 ```

![Step 1 — Prepare Environment](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/78d4ec3ff222ffd374ad81f0484ded46872b8630/Screenshot%202025-11-28%20125335.png)

---
### Step 2 — Download Velociraptor Binary
```bash
sudo wget https://github.com/Velocidex/velociraptor/releases/download/v0.73/velociraptor-v0.73.2-linux-amd64
sudo mv velociraptor-v0.73.2-linux-amd64 velociraptor
sudo chmod +x velociraptor
```

![Step 2 — Download Binary](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/ae7dfb54cd52aed66c69579ea049afb466c9a180/Screenshot%202025-11-28%20125539.png)

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


![Interactive Config](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/ae7dfb54cd52aed66c69579ea049afb466c9a180/Screenshot%202025-11-28%20125810.png)

---
### Step 4 — Update server.config.yaml

**Edit and set bind address and ports:**
```bash
frontend:
  bind_address: <SERVER_IP>
  frontend_port: 8000
  gui_port: 8889
```
![Config Update](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/b3db680f017e5c991b7794fa727a41509282bcb3/Screenshot%202025-11-28%20134455.png)

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
![dpkg install](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/b3db680f017e5c991b7794fa727a41509282bcb3/Screenshot%202025-11-28%20132741.png)

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
![Linux Client](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/a18ded513832481fc36bf86a286549fa7219e0da/Screenshot%202025-11-28%20135100.png)![Linux Client](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/a18ded513832481fc36bf86a286549fa7219e0da/Screenshot%202025-11-28%20140516.png)
                   
---

### 🪟 Windows Client Deployment

**Download Windows binary on server:**
```
wget https://github.com/Velocidex/velociraptor/releases/download/v0.73/velociraptor-v0.73.1-windows-amd64.exe
```

**Repack with client config:**
```
./velociraptor --config /opt/velociraptor/client.config.yaml \
  config repack --exe velociraptor-v0.73.1-windows-amd64.exe \
  /opt/velociraptor/client.config.yaml windows-client.exe
```

**Host file and download on Windows:**
```
sudo python3 -m http.server 9999
# On Windows browser: http://<SERVER_IP>:9999
```

**Install on Windows (PowerShell as Admin):**
```
.\windows-client.exe service install
```
![Windows Client](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/b3951eb68771a5a5cf7b88cb69b668d7f7190fd7/screenshots/Screenshot%202025-11-28%20144010.png)

---
### 📊 Validation & GUI

**Open the GUI:**
```
https://<SERVER_IP>:8889
```
**•	Login with admin credentials**

**•	Verify client check-ins and host details**

![GUI dashboard](https://github.com/omkarbabar1234/Velociraptor-Deployment-Endpoint-Forensics-Lab-/blob/b3951eb68771a5a5cf7b88cb69b668d7f7190fd7/screenshots/Screenshot%202025-11-28%20144025.png)

---

### ✅ Conclusion

*This lab provides an end-to-end Velociraptor deployment for DFIR: server installation, client packaging (Linux & Windows), secure communication via WSS, VQL-based collection, and GUI validation. It’s perfect for SOC, DFIR, and Threat Hunting practice.*

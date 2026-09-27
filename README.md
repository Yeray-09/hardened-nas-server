# 📄 Secure Self-Hosted Infrastructure: Nextcloud & MariaDB

> _A production-ready, security-hardened Linux server environment hosting Nextcloud and MariaDB via Docker Compose, defended by boundary firewalls, automated SSH protection, and an Nginx Reverse Proxy._
> 
>   

## 🌟 Highlights

Here are the main technical takeaways and implementation highlights of this project:

  

- 🛡️ **Defense-in-depth security model**: Multi-layered protection from host to container level.
    
      
    
- 🐳 **Isolated Containerization**: Nextcloud and MariaDB running in a private Docker network.
    
      
    
- ⚡ **Optimized Reverse Proxy**: Nginx handling SSL termination, WebSockets, timeouts, and rate limits.
    
      
    
- 🧱 **Hardened Network Boundary**: Strict UFW firewall policies with minimal attack surface.
    
      
    
- 🔑 **Passwordless SSH**: Strict key-based authentication with root login disabled and Fail2ban protection.
    
      
    
- 🔒 **Security Headers**: HSTS, X-Frame-Options, and X-Content-Type-Options enforced at the proxy level.
    
      
    

## ℹ️ Overview

This repository contains the architecture, configurations, and deployment steps for a **secure, self-hosted cloud platform**. Running on **Ubuntu Server (via VirtualBox)**, this setup provides a private Nextcloud instance backed by MariaDB, completely insulated behind a hardened Nginx reverse proxy.

  

The goal of this project is to make a secure and easy to use NAS server that runs on your own virtual machine or a real computer

  

### ✍️ Author

Created by Yeray-09

  

## 🚀 Usage instructions

Once deployed, the infrastructure handles incoming web traffic and routes it securely to the underlying application stack.

  

Plaintext

```
[ Internet / LAN Client ]
          │
          ▼
   [ UFW Firewall ] ──► (Ports Allowed: 22, 80, 443)
          │
          ▼
   [ Nginx Reverse Proxy ]
   ├── Rate Limiting & HSTS Header Enforcement
   ├── HTTP to HTTPS Automatic Redirect (301)
   └── Socket, Timeout & Payload Optimization
          │
          ├──────────────────────┐
          ▼                      ▼
  [ Nextcloud Container ] ── [ MariaDB Container ]
          ▲                      ▲
          └───── Docker Private ─┘
                 Internal Net
```

### Quick Verification Commands

Check the running container stack:

  

Bash

```
docker compose ps
```

Verify firewall policy and active rules:

  

Bash

```
sudo ufw status verbose
```

Monitor SSH brute-force attempts caught by Fail2ban:

  

Bash

```
sudo fail2ban-client status sshd
```

## ⬇️ Installation & Deployment

> _Note: This setup is designed for Ubuntu Server LTS running as a standalone node or virtual machine._
> 
>   

### Prerequisites

- Ubuntu Server LTS installed on bare metal or VirtualBox.
    
      
    
- Static IP address assigned (configured via Netplan).
    
      
    
- Docker and Docker Compose installed.
    
      
    

### 1. Network & System Hardening

Apply the network configuration (`netplan`) and enforce host security:

  

Bash

```
# Apply static IP
sudo netplan apply

# Configure UFW Firewall
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

### 2. SSH Security Configuration

Edit `/etc/ssh/sshd_config` to enforce key-based access:

  

Plaintext

```
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
```

Restart the SSH service:

  

Bash

```
sudo systemctl restart sshd
```

### 3. Deploy Application Stack

Clone this repository and launch the containerized environment:

  

Bash

```
git clone https://github.com/Yeray-09/hardened-nas-server.git
cd hardened-nas-server
docker compose up -d
```

## 📂 Repository Structure

Plaintext

```
.
├── docker-compose.yml       # Nextcloud & MariaDB orchestration
├── nginx/
│   ├── nginx.conf           # Global Nginx & rate-limiting rules
│   └── conf.d/
│       └── nextcloud.conf   # Reverse proxy, HSTS headers & WebSockets
├── netplan/
│   └── 01-netplan.yaml      # Static network interface configuration
├── security/
│   ├── jail.local           # Fail2ban jail configurations
│   └── sshd_config          # Hardened SSH configuration snippet
└── README.md
```

## 💭 Feedback & Contributions

Suggestions, bug reports, and architectural improvements are always welcome! Feel free to open an issue or submit a pull request if you see room for optimization.
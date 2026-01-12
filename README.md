# Lancache Docker Setup Guide

This guide will install and configure **Lancache** on an Ubuntu/Debian system with Docker and Docker Compose, using root privileges.  
DNS IP is set to `10.179.156.10` and DNS server name is `starnetworkbd.lan`.

---
✅ How to run:

Save it as setup_lancache.sh

Make it executable:

chmod +x setup_lancache.sh


Run as root (no sudo needed):

./setup_lancache.sh

## 1. System Update & Package Installation

Update system packages and install required tools:

```bash
apt update -y && apt upgrade -y
apt install -y docker.io docker-compose git
2. Enable and Start Docker
Enable Docker and containerd to start at boot and start them immediately:

bash
Copy code
systemctl enable docker containerd
systemctl start docker containerd
3. Clone Lancache Repository
Clone the official Lancache Docker repository:

bash
Copy code
cd /root
git clone https://github.com/lancachenet/docker-compose/ lancache --depth=1
cd lancache
4. Configure .env File
Create a .env file with DNS IP, server name, cache memory, and disk size:

bash
Copy code
cat > .env <<EOL
DNS_BIND_IP=10.179.156.10
DNS_SERVER_NAME=starnetworkbd.lan
CACHE_MEM_SIZE=4G
CACHE_DISK_SIZE=50G
UPSTREAM_DNS=8.8.8.8
EOL
5. Configure docker-compose.yml
Create a docker-compose.yml file for Lancache services:

bash
Copy code
cat > docker-compose.yml <<EOL
version: '2'
x-restart-policy: &restart-policy "unless-stopped"
services:
  dns:
    image: lancachenet/lancache-dns:latest
    container_name: dns
    env_file: .env
    restart: *restart-policy
    ports:
      - \${DNS_BIND_IP}:53:53/udp
      - \${DNS_BIND_IP}:53:53/tcp
  redis:
    image: redis:alpine
    restart: *restart-policy
  memcached:
    image: memcached:alpine
    restart: *restart-policy
  lancache:
    image: lancachenet/monolithic:latest
    container_name: lancache
    env_file: .env
    restart: *restart-policy
EOL
6. Pull Docker Images and Start Containers
bash
Copy code
docker-compose pull
docker-compose up -d
7. Verify Installation
Check running containers and their stats:

bash
Copy code
docker stats --no-stream
8. Updating DNS or Containers
If you change the DNS IP or server name later, run:

bash
Copy code
docker-compose down -v
docker-compose pull
docker-compose up -d
✅ Setup Complete!

DNS IP: 10.179.156.10

DNS Server Name: starnetworkbd.lan

Lancache services: DNS, Redis, Memcached, Monolithic Lancache

yaml
Copy code

---

If you want, I can also make a **ready-to-run `.sh` script version** linked in the markdown so someone can just run one command from this `.md` file — it’ll be fully automated.  

Do you want me to add that?

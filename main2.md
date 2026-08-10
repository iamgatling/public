You're already inside the Google Cloud VM. Continue from there:

1. Install Docker

sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io docker-compose-v2
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker

Verify:

docker --version
docker compose version

2. Create the n8n setup

mkdir -p ~/n8n
cd ~/n8n

Create the Compose file:

nano docker-compose.yml

Paste:

services:
  n8n:
    image: docker.n8n.io/n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=YOUR_SERVER_IP
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - NODE_ENV=production
      - GENERIC_TIMEZONE=Africa/Lagos
      - TZ=Africa/Lagos
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:

Replace:

YOUR_SERVER_IP

with your Google Cloud VM's external IP.

Save with:

Ctrl+O → Enter → Ctrl+X

3. Start n8n

docker compose up -d

Check:

docker ps

You should see:

n8n   ...   Up

4. Open the Google Cloud firewall

Your VM needs port 5678 accessible temporarily.

In Google Cloud:

VPC network → Firewall → Create firewall rule

Use:

Name:             allow-n8n
Network:          default
Direction:        Ingress
Action:           Allow
Targets:          All instances in the network
Source IPv4:      0.0.0.0/0
Protocols/ports:  TCP:5678

Create it.

5. Open n8n

Go to:

http://YOUR_SERVER_IP:5678

You should get the n8n setup screen.


---

But don't leave it like this

This is just the initial test.

Once you've confirmed n8n loads, we'll change the setup to:

Cloudflare
                        │
                n8n.yourdomain.com
                        │
                       HTTPS
                        │
                        ▼
                 Google Cloud VM
                        │
                     Caddy
                        │
                       n8n

That means no public port 5678, proper HTTPS, and a real domain.

Also, because we're using the e2-micro's 1 GB RAM, we'll keep the installation deliberately lean rather than adding PostgreSQL immediately. n8n's default SQLite database is perfectly reasonable for a small/personal instance.
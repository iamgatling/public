 may not be available under that package name. No need to stop.

Let's use the official Docker repository and install the current Docker Engine + Compose plugin. This also avoids mixing Ubuntu's older Docker packages with the Compose plugin.

Run this as-is:

sudo apt remove -y docker.io docker-compose docker-compose-v2 containerd runc

Then:

sudo apt update
sudo apt install -y ca-certificates curl

Add Docker's official GPG key:

sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

Add the Docker repository:

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

Now install the latest Docker packages:

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Enable Docker:

sudo systemctl enable --now docker

Add yourself to Docker:

sudo usermod -aG docker $USER
newgrp docker

Now:

docker --version
docker compose version

Important: the command is:

docker compose

with a space, not:

docker-compose

The latter is the old standalone Compose command.

Once that's installed, continue directly with:

mkdir -p ~/n8n
cd ~/n8n

Then we'll deploy n8n.
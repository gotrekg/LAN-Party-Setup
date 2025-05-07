# 04 - LanCache Prerequisites (Docker Setup)

Before we get into setting up LanCache.NET, we need to install Docker and Docker Compose. This guide covers that, along with a few post-install tweaks to make life easier.

## 🐳 Install Docker on Debian 12

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg

# Add Docker’s official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg |   sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up the Docker repo
echo   "deb [arch=$(dpkg --print-architecture)   signed-by=/etc/apt/keyrings/docker.gpg]   https://download.docker.com/linux/debian   $(lsb_release -cs) stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## ⚙️ Post-Install Steps

Let’s make it so you don’t need `sudo` for every `docker` command.

```bash
sudo groupadd docker  # Only if it doesn't exist
sudo usermod -aG docker $USER
```

Then **log out and log back in** to apply the group change.

You can test that it worked with:

```bash
docker run hello-world
```

If you see a friendly message from Docker, you're good to go.

---

Once that’s done, we’ll jump into actually setting up LanCache.NET in `05-lancache-net.md`.

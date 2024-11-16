# install-ollama-on-a-pi

## Install Docker and Docker Compose on a Raspberry Pi

Connect to the Raspberry Pi via SSH and run the following commands:

**Prerequisites:**
```bash
# Add Docker's official GPG key:

sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

**Install Docker:**
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**Post-installation steps for Docker:**
```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker

# Verify that you can run docker commands without sudo.
docker run hello-world
```

## Create a Docker Compose file

```bash
mkdir pi-genai-stack && cd pi-genai-stack
```

Create a file named `compose.yml` with the following content:

```yaml
services:

  ollama:
    image: ollama/ollama:0.4.1
    volumes:
      - ollama-data:/root/.ollama
    ports:
      - 11434:11434

  download-llm-qwen2.5-05b:
    image: curlimages/curl:8.6.0
    entrypoint: ["curl", "ollama:11434/api/pull", "-d", "{\"name\": \"qwen2.5:0.5b\"}"]
    depends_on:
      ollama:
        condition: service_started

volumes:
  ollama-data:
```

## Test the Ollama API

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "qwen2.5:0.5b",
  "prompt": "What is the best pizza in the world?",
  "stream": false
}'
```

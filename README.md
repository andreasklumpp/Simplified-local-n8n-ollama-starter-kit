# n8n + Ollama Local Stack

A minimal Docker Compose setup to run **n8n with external task runners and a local Ollama LLM**.

This stack is designed for **local AI workflow development/experimentation** with n8n while keeping execution isolated in **task runners** and running a **local LLM via Ollama**.
**It is not meant for any production use!**

---

# Stack Overview

This repository runs the following services:

| Service               | Description                               |
| --------------------- | ----------------------------------------- |
| **n8n**               | Main workflow automation server           |
| **task-runners**      | External execution workers for n8n        |
| **ollama**            | Local LLM server                          |
| **ollama-pull-llama** | Optional container that pre-pulls a model |


---

# Requirements

* Docker
* Docker Compose

Recommended:

* **8–16GB RAM** for local LLMs

---

# Getting Started

Clone the repository:

```bash
git clone https://github.com/your-org/n8n-ollama-local.git
cd n8n-ollama-local
```

Start the stack:

```bash
docker compose up -d
```

Open n8n:

```
http://localhost:5678
```

On first launch you will create an **n8n owner account**.

---

# How to Use

## 1. Verify the Stack

Check that containers are running:

```bash
docker compose ps
```

You should see:

```
n8n-main
n8n-runners
ollama
ollama-pull-llama
```

---

## 2. Verify Ollama

Check installed models:

```bash
docker exec -it ollama ollama list
```

You should see:

```
llama3.2
```

Test the model:

```bash
docker exec -it ollama ollama run llama3.2
```

Example prompt:

```
Why is the sky blue?
```

Exit with:

```
/bye
```

---

## 3. Connect n8n to Ollama

Inside n8n:

1. Create a new workflow
2. Add a node that calls an LLM (HTTP Request or AI node)
3. Use this endpoint:

```
http://ollama:11434
```

Example API request:

```
POST /api/generate
```

Body:

```json
{
  "model": "llama3.2",
  "prompt": "Explain quantum computing simply."
}
```

Because n8n and Ollama run in the same Docker network, the hostname **`ollama` works automatically**.

---

## 4. Pull Additional Models

To install other models:

```bash
docker exec -it ollama ollama pull mistral
```

Examples:

```
ollama pull mistral
ollama pull llama3
ollama pull phi3
```

Check installed models:

```bash
docker exec -it ollama ollama list
```

---

## 5. Using Ollama from Your Host

You can also call Ollama directly:

```
http://localhost:11434
```

Example:

```bash
curl http://localhost:11434/api/generate \
-d '{
  "model": "llama3.2",
  "prompt": "Write a haiku about automation"
}'
```

---

# n8n Configuration

External runners are enabled for improved isolation.

Important environment variables:

| Variable                   | Description                               |
| -------------------------- | ----------------------------------------- |
| `N8N_RUNNERS_ENABLED`      | Enables task runners                      |
| `N8N_RUNNERS_MODE`         | Uses external runners                     |
| `N8N_RUNNERS_AUTH_TOKEN`   | Auth token shared between n8n and runners |
| `N8N_NATIVE_PYTHON_RUNNER` | Enables Python runner                     |
| `OLLAMA_HOST`              | Ollama API endpoint                       |

If you change the token, update it in **both services**.

---

# Volumes

Persistent data is stored in Docker volumes:

| Volume        | Purpose                    |
| ------------- | -------------------------- |
| `n8n_data`    | n8n workflows and settings |
| `ollama_data` | downloaded models          |

---

# Useful Commands
Hint: If you are using Docker Desktop, the command is `docker compose ...`, if you installed Docker-Compose separately it might be `docker-compose ...``

Start services:

```bash
docker compose up -d
```

Stop services:

```bash
docker compose down
```

View logs:

```bash
docker compose logs -f
```

Pull a model:

```bash
docker exec -it ollama ollama pull mistral
```

List models:

```bash
docker exec -it ollama ollama list
```

---

# Security Note

Replace the default runner token:

```
your-secret-here
```

with a secure random value before using this setup outside of local development.

---
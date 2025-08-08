# 🔐 Authentik Docker Compose Stack

[![MIT License](https://img.shields.io/github/license/Vantasin/authentik?style=flat-square)](LICENSE)
[![Docker Compose](https://img.shields.io/badge/Docker-Compose-blue?logo=docker)](https://www.docker.com/)
[![ZFS](https://img.shields.io/badge/ZFS-OpenZFS-blue?style=flat-square)](https://openzfs.org/)

[![Authentik](https://img.shields.io/badge/Docs-Authentik-blue?style=flat-square)](https://goauthentik.io/docs/)

Authentik is an open-source Identity Provider focused on flexibility and security. It supports Single Sign-On (SSO), multi-factor authentication, LDAP, SAML, OAuth2, and forward authentication for securing web apps and services. Built for modern DevOps workflows, Authentik integrates seamlessly with Docker, Kubernetes, and popular reverse proxies like Traefik and Nginx Proxy Manager.

---

## 📁 Directory Structure

```bash
tank/
├── docker/
│   ├── compose/
│   │   └── authentik/              # Git repo lives here
│   │       ├── docker-compose.yml  # Main Docker Compose config
│   │       ├── .env                # Runtime environment variables and secrets (gitignored!)
│   │       ├── env.example         # Example .env file for reference
│   │       └── README.md           # This file
│   └── data/
│       └── authentik/              # Volume mounts and persistent data
```

---

## 🧰 Prerequisites

* Docker Engine
* Docker Compose V2
* Git
* (Optional) ZFS on Linux for dataset management
* (Optional) openssl for secure password generation

> ⚠️ **Note:** These instructions assume your ZFS pool is named `tank`. If your pool has a different name (e.g., `rpool`, `zdata`, etc.), replace `tank` in all paths and commands with your actual pool name.

---

## ⚙️ Setup Instructions

1. **Create the stack directory and clone the repository**

   If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/compose/authentik
   cd /tank/docker/compose/authentik
   sudo git clone https://github.com/Vantasin/Authentik.git .
   ```

   If using standard directories:
   ```bash
   mkdir -p ~/docker/compose/authentik
   cd ~/docker/compose/authentik
   git clone https://github.com/Vantasin/Authentik.git .
   ```

2. **Create the runtime data directory**

   If using ZFS:
   ```bash
   sudo zfs create -p tank/docker/data/authentik
   ```

   If using standard directories:
   ```bash
   mkdir -p ~/docker/data/authentik
   ```

3. **Configure environment variables**

   Copy and modify the `.env` file:

   ```bash
   sudo cp env.example .env
   sudo nano .env
   sudo chmod 600 .env
   ```
   
	> **Note:**
	> Use a secure password generator of your choice such as `openssl`.
	> Run the following commands to generate a password and secret key and write them to your .env file:

	```bash
	sudo echo "PG_PASS=$(openssl rand -base64 36 | tr -d '\n')" >> .env
	sudo echo "AUTHENTIK_SECRET_KEY=$(openssl rand -base64 60 | tr -d '\n')" >> .env
	```


4. **Start Authentik**

   ```bash
   sudo docker compose up -d
   ```

---

## 🌐 Accessing Authentik Web UI

Once deployed, access **Authentik** using:

- **Web Interface:** Enter the URL for **Authentik**. Eg. `https://authentik.example.com`.

   > **Tip:** You must create a URL `https://authentik.example.com` using [Nginx Proxy Manager](https://github.com/Vantasin/Nginx-Proxy-Manager.git) as a reverse proxy for HTTPS certificates via Let's Encrypt.
   >
   > **Proxy Host:**
   >  - **Domain Name:** `https://authentik.example.com`
   >  - **Scheme:** `http`
   >  - **Forward Hostname/IP:** `authentik_server`
   >  - **Forward Port:** `9000`
   >
   > **SSL:**
   >  - Check **Enable SSL**
   >  - From the **Certificate** dropdown select your `*.example.com` certificate
   >  - Enable **Force SSL** to redirect all HTTP → HTTPS

- **Initial Setup:** When you first access the web interface, you will be prompted to create an admin account.

---

## 🙏 Acknowledgments

- [ChatGPT](https://openai.com/chatgpt) — for assistance in generating setup scripts and templates.
- [Docker](https://www.docker.com/) — for container orchestration and runtime.
- [OpenZFS](https://openzfs.org/) — for advanced local filesystem features, dataset organization, and snapshotting.
- [Authentik](https://goauthentik.io) — an open-source Identity Provider that offers secure authentication and authorization services, including SSO, MFA, LDAP, SAML, OAuth2, and forward auth for web apps.

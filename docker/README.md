# Cloud-1 — Docker Setup

## 1. Overview

This project uses Docker Compose to run four services in separate containers.

| Service | Function |
|---|---|
| Nginx | Handles HTTP requests |
| WordPress (PHP-FPM) | Runs WordPress |
| MariaDB | Stores website data |
| phpMyAdmin | Manages the database |

## 2. Requirements

- Docker
- Docker Compose

Make sure Docker is running:

```bash
docker info
```

## 3. Installation

**Step 1 — Configure environment variables**

From the `docker/` directory, run:

```bash
cp .env.example .env
```

Set two different strong passwords in `.env`:

```dotenv
DB_ROOT_PASSWORD=replace_with_root_password
DB_PASSWORD=replace_with_database_password
```

**Step 2 — Check and start the services**

```bash
docker compose config --quiet
docker compose up -d
docker compose ps
```

Expected result: All four containers are running.

## 4. Access the Services

| Service | Local URL |
|---|---|
| WordPress | http://localhost:8080 |
| phpMyAdmin | http://localhost:8081 |

For phpMyAdmin, use username `wordpress` and the password from `DB_PASSWORD`.

For a new WordPress installation, follow the instructions in the browser.

## 5. Verification

**Step 3 — Check the network and volumes**

```bash
docker network ls
docker volume ls
```

Verify that all containers use the same Compose network.

Verify that the database and WordPress volumes exist.

**Step 4 — Test data persistence**

Create and publish a WordPress post. Then run:

```bash
docker compose down
docker compose up -d
```

Expected result: The post and user account still exist.

**WARNING:** Do not use `docker compose down -v`. This deletes the named volumes and their data.

**Step 5 — Test container restart**

```bash
docker compose restart phpmyadmin
docker compose ps
```

Expected result: All four containers are running. WordPress remains accessible.

## 6. Security

- MariaDB port `3306` is not published to the host.
- PHP-FPM port `9000` is not published to the host.
- Local web ports are bound to `127.0.0.1`.
- Passwords are stored in `.env`, not in the Compose file.
- `.env` and `backups/` must not be committed to Git.

## 7. Cloud Deployment

The local Docker configuration has been tested on macOS.

The final deployment is managed through Ansible on Ubuntu. It must provide:

- Automated installation and deployment
- Nginx routing and HTTPS
- Secure access and secrets management
- Persistent data after server restart
- Idempotent deployment
- Deployment to multiple servers

The local ports `8080` and `8081` are for development only. The final server uses ports `80` and `443` for public web access.
# E‑Commerce Application – Docker Compose Setup

This document explains the Docker Compose configuration for running an **e‑commerce backend** with **Node.js**, **PostgreSQL**, and **Redis** using Docker.

---

## 📦 Services Overview

| Service | Description                   |
| ------- | ----------------------------- |
| backend | Node.js API server            |
| db      | PostgreSQL 16 database        |
| redis   | Redis 7 cache / session store |

---

## 🗂 docker-compose.yml

```yaml
name: e-commerce

services:
  backend:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: backend
    ports:
      - "8000:8000"
    depends_on:
      - db
      - redis
    networks:
      - backend
    environment:
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: postgres
      DB_PASSWORD: postgres
      DB_NAME: postgres
      REDIS_HOST: redis
      REDIS_PORT: 6379

  db:
    image: postgres:16
    container_name: postgres
    environment:
      POSTGRES_PASSWORD: postgres
      POSTGRES_USER: postgres
      POSTGRES_DB: postgres
    ports:
      - "5431:5432"
    networks:
      - backend
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    container_name: redis
    depends_on:
      - db
    ports:
      - "6379:6379"
    networks:
      - backend
    volumes:
      - redis_data:/data

networks:
  backend:
    driver: bridge

volumes:
  postgres_data:
  redis_data:
```

---

## 🧠 Architecture Explanation

```
Client → Backend (Node.js)
             │
             ├── PostgreSQL (db)
             └── Redis (cache)
```

* All containers run in a **private bridge network** (`backend`).
* Backend communicates with services using **service names as hostnames** (`db`, `redis`).
* Only the backend service is publicly exposed.

---

## 🌐 Networking

* Docker creates a custom bridge network called `backend`.
* Containers resolve each other using DNS automatically.

Example hostnames inside containers:

| Service    | Hostname  |
| ---------- | --------- |
| PostgreSQL | `db`      |
| Redis      | `redis`   |
| Backend    | `backend` |

---

## 💾 Data Persistence (Volumes)

Two named volumes are used:

| Volume        | Purpose                          |
| ------------- | -------------------------------- |
| postgres_data | Stores PostgreSQL database files |
| redis_data    | Stores Redis data                |

Data remains safe even if containers are stopped or recreated.

---

## ⚙ Environment Variables (Backend)

These variables are injected into the backend container:

```env
DB_HOST=db
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=postgres
DB_NAME=postgres
REDIS_HOST=redis
REDIS_PORT=6379
```

Use them in Node.js:

```js
process.env.DB_HOST
process.env.REDIS_HOST
```

---

## ▶ How to Run

Build and start all services:

```bash
docker compose up -d --build
```

Stop everything:

```bash
docker compose down
```

View running containers:

```bash
docker compose ps
```

---

## 🧪 Testing Connectivity

Enter backend container:

```bash
docker exec -it backend sh
```

Test:

```bash
ping db
ping redis
```

---

## 🔒 Security Notes

* Database and Redis are **not exposed publicly** (recommended for production).
* Only backend port `8000` is accessible from host.
* Use `.env` file or Docker secrets in production.

---

## 🏗 Recommended Project Structure

```
e-commerce/
│
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│
├── docker-compose.yml
└── .env
```

If Dockerfile is inside `backend/`, update:

```yaml
build:
  context: ./backend
```

---

## 🚀 Optional Improvements

* Add MongoDB service
* Add Nginx reverse proxy
* Add healthchecks
* Use multi-stage Dockerfile
* Add monitoring (Prometheus + Grafana)
* Deploy to Kubernetes / AWS ECS

---

## ✅ Summary

This setup provides:

* Multi-container architecture
* Isolated networking
* Persistent storage
* Production-ready structure
* Easy onboarding for developers

---

Happy shipping 🚀

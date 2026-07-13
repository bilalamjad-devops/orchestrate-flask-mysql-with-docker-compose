# Orchestrate a Flask + MySQL Application with Docker Compose

## Objective

In the previous project, we started the Flask and MySQL containers manually using multiple `docker run` commands.

In this project, we simplify the deployment by using **Docker Compose**. Instead of creating networks, running containers, and configuring environment variables manually, the entire application stack is defined in a single `docker-compose.yml` file.

---

## Business Problem

Managing multiple containers manually is repetitive and error-prone.

Every developer has to remember:

- Create a Docker network
- Start the MySQL container
- Start the Flask container
- Configure environment variables
- Connect both containers to the same network

As the application grows, this process becomes difficult to maintain.

---

## Solution

Docker Compose allows us to define the complete application stack in a single YAML file.

With one command, Docker Compose automatically:

- Creates a network
- Starts the MySQL container
- Starts the Flask container
- Connects both containers
- Passes environment variables
- Manages container lifecycle

---

# Project Flow

```text
Clone Repository
        │
        ▼
docker compose up
        │
        ▼
Create Docker Network
        │
        ▼
Start MySQL Container
        │
        ▼
Start Flask Container
        │
        ▼
Application Running
        │
        ▼
Verify Database
```

---

# Project Structure

```text
flask-mysql-docker-compose/

├── app.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── .env
├── templates/
│   └── index.html
└── README.md
```

---

# Steps

## Step 1: Clone the Repository

## Step 2: Configure Environment Variables

## Step 3: Build and Start the Application Stack

## Step 4: Verify Running Containers

## Step 5: Access the Application

## Step 6: Verify Data Inside MySQL

## Step 7: Stop the Application Stack

---

# Commands

Clone the repository.

```bash
git clone <repo-url>
cd flask-mysql-docker-compose
```

---

Configure environment variables.

```bash
cp .env.example .env
```

---

Build and start everything.

```bash
docker compose up -d --build
```

---

Verify running containers.

```bash
docker ps
```

---

Open the application.

```
http://localhost:5000
```

---

Verify data.

```bash
docker exec -it mysql-db mysql -u root -p
```

```sql
SHOW DATABASES;

USE web_db;

SHOW TABLES;

SELECT * FROM users;
```

---

Stop the application.

```bash
docker compose down
```

---

# Key Learning

- Docker Compose
- Multi-container applications
- Services
- Networks
- Environment variables
- Container communication
- Simplified application deployment

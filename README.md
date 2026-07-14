

# Docker Compose for a Flask & MySQL Application

## Objective

In this project, you will learn how to orchestrate a multi-container application using **Docker Compose**. Instead of manually creating containers and networks, Docker Compose defines the entire application stack in a single `docker-compose.yml` file and starts all services with one command.

I am using Docker Desktop and Git Bash.


---

## Project Flow

```text
Developer
    │
    ▼
docker compose up -d --build
    │
    ▼
Docker Compose
    │
    ├───────────────┐
    │               │
    ▼               ▼
MySQL Container   Flask Container
       │               │
       └──────┬────────┘
              │
      Docker Network
              │
              ▼
     Flask stores data in MySQL
              │
              ▼
      http://localhost:5000
```

---

## Project Structure

```text
orchestrate-flask-mysql-with-docker-compose/
│
├── app.py
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── .env.example
├── templates/
│   └── index.html
└── README.md
```

---

## Steps

Step 1: Clone the Repository

Step 2: Configure Environment Variables

Step 3: Build and Start the Application

Step 4: Verify the Application

Step 5: Verify Data Inside MySQL

Step 6: Stop and Remove the Environment


### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd orchestrate-flask-mysql-with-docker-compose
```

---

### Step 2: Configure Environment Variables

Create a local environment file.

```bash
cp .env.example .env
```

Open the file.

```bash
vim .env
```

Update the database host.

```env
DB_HOST=mysql-db
```

---

### Step 3: Build and Start the Application

Build the Flask image and start all containers.

```bash
docker compose up -d --build
```

Verify the containers are running.

```bash
docker ps
```

---

### Step 4: Verify the Application

Open your browser.

```text
http://localhost:5000
```

Submit sample data through the web application.

<img width="1600" height="900" alt="compose 1" src="https://github.com/user-attachments/assets/b6d1ff5b-62d4-4ca8-b3dc-565c992ecbf2" />


---

### Step 5: Verify Data Inside MySQL

Enter the MySQL container.

```bash
docker exec -it mysql-db bash
```

Connect to MySQL.

```bash
mysql -u root -p
```

Enter the root password.

---

### Verify Database

Show databases.

```sql
SHOW DATABASES;
```

Select the application database.

```sql
USE web_db;
```

Show available tables.

```sql
SHOW TABLES;
```

View stored records.

```sql
SELECT * FROM users;
```

Describe the table structure.

```sql
DESCRIBE users;
```

Exit MySQL.

```sql
exit;
```

Exit the container.

```bash
exit
```

---

### Step 6: Stop and Remove the Environment

Stop all running containers and remove the associated volume.

```bash
docker compose down -v
```

---

## Key Learning

After completing this project, you will understand:

* Creating and managing multiple containers with Docker Compose.
* Building a custom Docker image using a Dockerfile.
* Starting an entire application stack with a single command.
* Automatic Docker network creation for inter-container communication.
* Using service names (e.g., `mysql-db`) instead of IP addresses.
* Managing application configuration with `.env` files.
* Persisting MySQL data using Docker volumes.
* Verifying data stored inside a running MySQL container.
* Cleaning up containers, networks, and volumes.

---

### Why Use Docker Volumes?

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

```yaml
volumes:
  mysql_data:
```

A Docker volume stores database files outside the container.

Without a volume:

```text
Delete Container
      │
      ▼
Database is deleted ❌
```

With a volume:

```text
Delete Container
      │
      ▼
Database is preserved ✅
```

This allows MySQL data to survive container recreation, making volumes essential for stateful applications.

---

14-July-2026

# MongoDB Local Development Environment

This repository provides a containerized MongoDB setup using **Docker Compose**. It includes the official MongoDB Community Edition image and **Mongo Express** for a lightweight web-based administration interface.

## 📋 Prerequisites

Ensure the following are installed on your system:

* **Docker Desktop** (or Docker Engine + Docker Compose)
* **Make** (standard on Linux/macOS; use WSL or Git Bash on Windows)

## 🚀 Quick Start

To start the database and the admin UI immediately:

```bash
make up

```

Once running:

* **MongoDB Port:** `27017`
* **Admin UI:** [http://localhost:8081](https://www.google.com/search?q=http://localhost:8081)

## 🛠 Command Reference

We use a `Makefile` to simplify common Docker operations.

| Command | Description |
| --- | --- |
| `make up` | Starts MongoDB and Mongo Express in the background. |
| `make down` | Stops and removes the running containers. |
| `make restart` | Restarts the containers (useful for reloading config). |
| `make logs` | Tails the logs for the MongoDB container only. |
| `make shell` | Connects directly to the `mongosh` CLI inside the container. |
| `make clean` | **WARNING:** Stops containers and **deletes** the persistent data volume. |

## ⚙️ Configuration Details

The stack is defined in `docker-compose.yml`.

### Credentials

* **Root Username:** `root`
* **Root Password:** `passw0rd`
* **Auth Database:** `admin`

### Data Persistence

Data is persisted using a named Docker volume (`mongodb_data`).

* Running `make down` **preserves** your data.
* Running `make clean` **deletes** your data.

## 🔌 Connection Strings

### From Host Machine (Local Development)

Use this connection string for your local **Golang** or **Node.js** applications:

```text
mongodb://root:passw0rd@localhost:27017/?authSource=admin

```

### From Inside Docker Network

If your application is running inside another Docker container on the same network (`app-network`), use the service name:

```text
mongodb://root:passw0rd@mongodb:27017/?authSource=admin

```

## 📁 Project Structure

```text
.
├── docker-compose.yml  # Defines MongoDB and Mongo Express services
├── Makefile            # Shortcuts for Docker commands
└── README.md           # Documentation

```

## 📝 Integration Notes for Golang

Example `mongo-driver` configuration:

```go
ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
defer cancel()

// DSN for local development
uri := "mongodb://root:passw0rd@localhost:27017/?authSource=admin"

client, err := mongo.Connect(ctx, options.Client().ApplyURI(uri))
if err != nil {
    log.Fatal(err)
}
```
 
## 🔐 Environment and pushing

To avoid committing secrets, create a local `.env` from the provided `.env.example` and fill in your values. The `.env` file is ignored by Git.

```bash
cp .env.example .env
# then edit .env and fill values (GIT_REMOTE_URL, GIT_USER_NAME, GIT_USER_EMAIL, GITHUB_TOKEN)
```

- If you want to push to GitHub over SSH, set `GIT_REMOTE_URL` to the SSH URL (e.g. `git@github.com:username/repo.git`).
- If you prefer HTTPS and use a personal access token, keep the token in `.env` and configure your remote accordingly (do not commit it).

To add a remote and push (replace with your remote URL if you prefer):

```bash
# Add remote (if not already set)
git remote add origin "$GIT_REMOTE_URL"
# Push main branch
git push -u origin main
```

Note: If `GIT_USER_NAME` and `GIT_USER_EMAIL` are not set globally, you can apply them locally for this repository:

```bash
git config user.name "$GIT_USER_NAME"
git config user.email "$GIT_USER_EMAIL"
```

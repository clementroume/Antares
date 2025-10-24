# Antares Project

Antares is a modern, basic full-stack web application featuring a Spring Boot backend (`antares-api`) and an Angular frontend (`antares-app`), fully containerized with Docker.

## Tech Stack

* **Backend**: Java 21, Spring Boot 3.5, Spring Security, JWT, Flyway, MapStruct
* **Frontend**: Angular 20, TypeScript, Tailwind CSS 4, DaisyUI 5
* **Database**: PostgreSQL
* **Cache & Sessions**: Redis
* **Deployment**: Docker & Docker Compose, Nginx

## Prerequisites

* [JDK 21](https://adoptium.net/) or newer
* [Node.js 22](https://nodejs.org/) or newer
* [Docker](https://www.docker.com/products/docker-desktop/) & Docker Compose

## Getting Started

The application is designed to be launched using Docker Compose.

### Environment Configuration

Create an `.env` file in the project's root directory based on the following structure. This file contains all the sensitive variables required to run the application.

```txt
# Environment variables

# === Database Credentials ===
POSTGRES_DB=
POSTGRES_USER=
POSTGRES_PASSWORD=

# === JWT Security Configuration ===
# Generate with 'openssl rand -base64 64'
JWT_SECRET=

# === Default Admin User Configuration ===
ADMIN_FIRSTNAME=
ADMIN_LASTNAME=
ADMIN_EMAIL=
ADMIN_PASSWORD=
```

### Generate local certificates in the project's root directory (MacOs)

```bash
# Generate certificate and key
mkcert -cert-file nginx/certs/cert.crt -key-file nginx/certs/cert.key "antares.local" "localhost" "127.0.0.1" "::1"
# Install certificates
mkcert -install            
```

### Add antares.local to your known hosts

```txt
127.0.0.1       antares.local
::1             antares.local
````

### Build and Run Containers

This command will build the API and app images, then start all services (api, app, db, redis).

```bash
docker-compose up --build -d
```

The application is now accessible at `https://antares.local` (assuming your `nginx.conf` handles SSL or you have an upstream reverse proxy).

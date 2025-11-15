# StellarAtlas Project

StellarAtlas is a modern, full-stack web application featuring a Spring Boot backend (
`antares-auth`), an Angular frontend (`sirius-app`), and a Spring Boot Admin server (`vega-admin`).
The entire stack is containerized with Docker and served securely via a Traefik reverse proxy.

## Tech Stack

- **Backend (Auth)**: Java 21, Spring Boot 3.5, Spring Security, JWT (`antares-auth`)
- **Backend (Admin)**: Spring Boot Admin (`vega-admin`)
- **Frontend**: Angular 20, TypeScript, Tailwind CSS 4, DaisyUI 5 (`sirius-app`)
- **Database**: PostgreSQL (`castor-db`)
- **Cache & Sessions**: Redis (`pollux-cache`)
- **Reverse Proxy**: Traefik (handles HTTPS, local domains, rate limiting)
- **Deployment**: Docker & Docker Compose

## Architecture Overview

All local traffic is managed by Traefik, which provides a single secure entry point (`https://...`)
and routes requests to the appropriate service.

``` 
(Your Mac)
        |
        | Accesses:
        | - https://stellar.atlas            -> [Traefik] -> [sirius-app (Nginx)]
        | - https://stellar.atlas/api        -> [Traefik] -> [antares-auth (Spring)]
        | - https://api.stellar.atlas        -> [Traefik] -> [antares-auth (Spring)]
        | - https://admin.stellar.atlas      -> [Traefik] -> [vega-admin (Spring)]
        | - https://dashboard.stellar.atlas  -> [Traefik] (Internal Dashboard)
        |
        | Direct access via localhost (for development):
        | - localhost:5432  -> [castor-db]
        | - localhost:6379  -> [pollux-cache]
```

## Prerequisites

- [JDK 21](https://adoptium.net/ "null")or newer
- [Node.js 22](https://nodejs.org/ "null")or newer
- [Docker](https://www.docker.com/products/docker-desktop/ "null")& Docker Compose
- `openssl`(usually pre-installed on macOS/Linux)
- `htpasswd`(comes with`apache2-utils`on Linux, or use an online generator)

## Local Setup

### 1. Environment Configuration

Create an`.env`file in the project's root directory. This file contains all the sensitive variables
required to run the application.

```txt
# === Database Credentials ===
POSTGRES_DB=antares
POSTGRES_USER=antares
POSTGRES_PASSWORD=your_strong_postgres_password

# === Cache Credentials ===
REDIS_PASSWORD=your_strong_redis_password

# === JWT Security Configuration ===
# Generate with 'openssl rand -base64 64'
JWT_SECRET=your_long_and_random_jwt_secret_key

# === Default Admin User Configuration ===
# Used by 'antares-auth' and 'vega-admin'
ADMIN_FIRSTNAME=Admin
ADMIN_LASTNAME=User
ADMIN_EMAIL=admin@stellar.atlas
ADMIN_PASSWORD=your_strong_admin_password

# === Traefik Dashboard Authentication ===
# Generate with 'htpasswd -nb user password', escaping '$' as '$$'
TRAEFIK_DASHBOARD_AUTH=your_htpasswd_hash_with_escaped_dollars 
```

### 2. Generate Local Certificates

We need self-signed certificates for Traefik to serve HTTPS locally.

```bash
# Create the certs directory
mkdir -p certs

# Generate the certificate for *.stellar.atlas
openssl req -x509 -nodes -days 365 -newkey rsa:2048
-keyout certs/local.key -out certs/local.crt
-subj "/CN=*.stellar.atlas"
```

### 3. Update Your Host File

Your computer needs to know that these domains point to your local machine.

- **macOS/Linux**: Edit`/etc/hosts`
- **Windows**: Edit`C:\Windows\System32\drivers\etc\hosts`

Add the following lines:

``` 
127.0.0.1 stellar.atlas api.stellar.atlas admin.stellar.atlas dashboard.stellar.atlas 
::1 stellar.atlas api.stellar.atlas admin.stellar.atlas dashboard.stellar.atlas 
```

### 4. Build and Run Containers

This command will build the service images and start all services.

``` bash
docker compose up --build -d
```

## Accessing the Application

Your stack is now running. Your browser will show a security warning, which is normal (self-signed
certificate). You can safely "proceed" or "accept the risk".

- **Main Application**:`https://stellar.atlas`
- **Spring Boot Admin**:`https://admin.stellar.atlas`
- **Traefik Dashboard**:`https://dashboard.stellar.atlas`
- **API (Swagger UI)**:`https://stellar.atlas/swagger-ui.html`
- **API (Dedicated)**:`https://api.stellar.atlas`

**Database & Cache Access (Local Development)**

The databases are securely exposed_only_to`localhost`on your host machine.

- **PostgreSQL (`castor-db`)**:
    - **Host**:`127.0.0.1`
    - **Port**:`5432`
    - **User/Password**: (from your`.env`)

- **Redis (`pollux-cache`)**:
    - **Host**:`127.0.0.1`
    - **Port**:`6379`
    - **Password**: (from your`.env`)
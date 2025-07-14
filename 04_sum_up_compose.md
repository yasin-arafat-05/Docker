

### Key Points
- Docker Compose simplifies managing multi-container applications using a YAML file.
- The provided universal `docker-compose.yml` includes key features like project naming, lifecycle hooks, profiles, environment variables, networking, secrets, and more.
- Each feature is configured to work together, with comments explaining their purpose for beginners.
- Some features, like lifecycle hooks and watch mode, require Docker Compose CLI version 2.30.0 or later.
- The setup assumes basic files (e.g., Dockerfiles, secret files) exist in the project directory.

### What is a Universal Docker Compose File?
A Docker Compose file is a YAML file that defines how multiple containers work together to form an application. The universal file below includes all major features from the [Docker Compose documentation](https://docs.docker.com/compose/), such as project naming, service dependencies, and networking, making it a great starting point for learning or building your own app.

### How to Use It
- **Setup**: Ensure directories (`./base`, `./web`) and files (`.env`, `db_password.txt`, `web_config.txt`) exist in your project folder.
- **Run**: Use `docker compose up --build` to start all services, or `docker compose up --profile web` to start specific services.
- **Development**: Use `docker compose up --watch` for automatic code updates during development.
- **Verify**: Check containers with `docker ps`, networks with `docker network ls`, or resolved config with `docker compose config`.

### What’s Included
- **Services**: A base image, a web app, and a database, covering builds, dependencies, and profiles.
- **Networking**: Custom and external networks for flexible communication.
- **Secrets and Configs**: Secure handling of sensitive data and configurations.
- **Development Features**: Watch mode and lifecycle hooks for easier coding.

---

```yaml
# Universal Docker Compose File
# This file demonstrates key features from the Docker Compose documentation:
# - Project naming for organizing containers
# - Service builds with dependencies for efficient image creation
# - Environment variables and .env files for configuration
# - Custom and external networks for service communication
# - Secrets and configs for secure data management
# - Volumes for persistent data
# - Dependencies with healthchecks for startup order
# - Profiles for selective service activation
# - Lifecycle hooks for startup/shutdown tasks (requires Compose CLI 2.30.0+)
# - Watch mode for development (requires Compose CLI 2.22.0+)
# - Swarm mode deployment (optional)

version: '3.8'  # Latest Compose file format
name: myuniversalapp  # Project name, prefixes container/network names

# Services definition
services:
  # Base service: builds a base image for other services
  base:
    build: ./base  # Build context is ./base, creates myuniversalapp_base image

  # Web service: main application, builds on base, includes most features
  web:
    build:
      context: ./web
      dockerfile: Dockerfile-web
      additional_contexts:
        base_image: "service:base"  # Depends on base service image
    ports:
      - "80:80"  # Maps host port 80 to container port 80
    environment:
      - DEBUG=true  # Static environment variable
    env_file:
      - .env  # Loads variables from .env file
    volumes:
      - ./web/data:/data  # Mounts host directory to container
    networks:
      - frontend
      - backend  # Connects to both networks to reach db
    depends_on:
      db:
        condition: service_healthy  # Waits for db to be healthy
    profiles:
      - web  # Starts only with --profile web
    secrets:
      - db_password  # Mounts secret for secure access
    configs:
      - source: web_config
        target: /app/config.txt  # Mounts config file
    develop:
      watch:
        - action: sync
          path: ./web/src
          target: /app/src
          ignore:
            - node_modules/  # Ignores node_modules for performance
        - action: rebuild
          path: ./web/package.json  # Rebuilds on dependency changes
    # Lifecycle hooks for startup/shutdown tasks
    post_start:
      - command: echo "Web service started"
    pre_stop:
      - command: echo "Web service stopping"
    # Optional: Swarm mode settings
    deploy:
      replicas: 2
      resources:
        limits:
          cpus: '0.50'
          memory: 512M

  # Database service: PostgreSQL with healthcheck and persistent storage
  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password  # Uses secret
    volumes:
      - db_data:/var/lib/postgresql/data  # Persistent storage
    networks:
      - backend  # Isolated on backend network
    secrets:
      - db_password
    profiles:
      - db  # Starts only with --profile db
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

# Networks definition
networks:
  frontend:
    driver: bridge  # For external-facing services
  backend:
    driver: bridge  # For internal services like db
  external_net:
    external: true
    name: my_external_network  # Connects to existing network

# Volumes definition
volumes:
  db_data:  # Named volume for database persistence

# Secrets definition
secrets:
  db_password:
    file: ./db_password.txt  # Secret file for password

# Configs definition
configs:
  web_config:
    file: ./web_config.txt  # Non-sensitive config file
```

### Comprehensive Guide to Docker Compose Features

This section provides a detailed overview of the Docker Compose features included in the universal `docker-compose.yml` file, based on the [Docker Compose documentation](https://docs.docker.com/compose/). Each feature is explained with its purpose, configuration, and usage, ensuring a complete understanding for beginners.

#### 1. Project Name
**Purpose**: The project name groups containers and networks, prefixing their names (e.g., `myuniversalapp_web_1`). It defaults to the project directory name but can be customized.

**Configuration**:
- Set explicitly with `name: myuniversalapp` in the Compose file.
- Alternatives:
  - CLI: `docker compose -p myproject up`
  - Environment: `COMPOSE_PROJECT_NAME=myproject`
- Rules: Must use lowercase letters, numbers, dashes, or underscores, starting with a letter or number.

**Usage**:
- Run `docker compose up` to create containers named `myuniversalapp_web_1` and networks like `myuniversalapp_frontend`.

#### 2. Services
**Purpose**: Services define containers, their images, and configurations. The file includes three services: `base` (builds a base image), `web` (main application), and `db` (database).

**Configuration**:
- **base**: Builds a base image for dependency sharing.
- **web**: Includes build, ports, environment variables, volumes, networks, dependencies, profiles, secrets, configs, watch mode, lifecycle hooks, and swarm settings.
- **db**: Uses a pre-built Postgres image with environment variables, volumes, secrets, and healthchecks.

**Usage**:
- Start all services: `docker compose up`.
- Start specific services: `docker compose up --profile web`.

#### 3. Building Dependent Images
**Purpose**: Share common image layers to reduce build time and image size.

**Configuration**:
- **base**: Builds from `./base`, creating an image tagged `myuniversalapp_base`.
- **web**: Uses `additional_contexts` to build on `base`’s image, with `Dockerfile-web` containing `FROM base_image`.

**Example Files**:
- `./base/Dockerfile`:
  ```dockerfile
  FROM alpine
  RUN apk add --update --no-cache openssl
  ```
- `./web/Dockerfile-web`:
  ```dockerfile
  FROM base_image
  COPY . /app
  ```

**Usage**:
- Run `docker compose build` to build `base` first, then `web`.

#### 4. Environment Variables
**Purpose**: Configure services dynamically without hardcoding values.

**Configuration**:
- **Static**: `environment: - DEBUG=true` in `web`.
- **File**: `env_file: - .env` loads variables from `.env`.
- **Interpolation**: Variables like `${DEBUG}` can be used (see below).

**Example `.env`**:
```plaintext
DEBUG=true
API_KEY=xyz123
```

**Precedence** (highest to lowest):
1. CLI: `docker compose run -e DEBUG=false web`
2. `environment: - DEBUG=${DEBUG}` (from shell or `.env`)
3. `environment: - DEBUG=true`
4. `env_file`
5. Dockerfile `ENV`

**Usage**:
- Check resolved config: `docker compose config`.

#### 5. Interpolation
**Purpose**: Dynamically insert variable values into the Compose file.

**Configuration**:
- Used implicitly in `environment` or `image` (e.g., `image: webapp:${TAG}`).
- Syntax:
  - `${VAR}`: Uses `VAR`’s value.
  - `${VAR:-default}`: Uses `default` if `VAR` is unset or empty.
  - `${VAR:?error}`: Exits with `error` if `VAR` is unset.

**Sources** (highest to lowest):
1. Shell environment.
2. `.env` in working directory.
3. `.env` via `--env-file`.

**Usage**:
- Create `.env` with `TAG=v1.5`, then `docker compose config` shows resolved values.

#### 6. Networking
**Purpose**: Enable communication between services using service names.

**Configuration**:
- **Default**: Compose creates `myuniversalapp_default` (not used here due to custom networks).
- **Custom Networks**:
  - `frontend`: For external-facing services (`web`).
  - `backend`: For internal services (`db`, `web`).
- **External Network**: `external_net` connects to `my_external_network` (created via `docker network create my_external_network`).
- **Service Connections**: `web` joins both `frontend` and `backend` to communicate with `db`.

**Usage**:
- `web` connects to `db` using `postgres://db:5432`.
- Check networks: `docker network ls`.

#### 7. Volumes
**Purpose**: Persist data or share files between host and container.

**Configuration**:
- **Named Volume**: `db_data` for `db`’s persistent storage.
- **Bind Mount**: `./web/data:/data` in `web` for file sharing.

**Usage**:
- Data persists in `db_data` even after containers stop.
- Check volumes: `docker volume ls`.

#### 8. Secrets
**Purpose**: Securely manage sensitive data like passwords.

**Configuration**:
- **Definition**: `db_password` sourced from `./db_password.txt`.
- **Usage**: Mounted at `/run/secrets/db_password` in `web` and `db`.
- **Environment**: `POSTGRES_PASSWORD_FILE` in `db` reads the secret.

**Example `db_password.txt`**:
```plaintext
mypassword
```

**Usage**:
- Safer than environment variables, as secrets are mounted as files with restricted access.

#### 9. Configs
**Purpose**: Manage non-sensitive configuration files.

**Configuration**:
- **Definition**: `web_config` sourced from `./web_config.txt`.
- **Usage**: Mounted at `/app/config.txt` in `web`.

**Example `web_config.txt`**:
```plaintext
server_name=example.com
```

**Usage**:
- Similar to secrets but for non-sensitive data.

#### 10. Depends On and Healthchecks
**Purpose**: Control service startup order and ensure dependencies are ready.

**Configuration**:
- **web**: Uses `depends_on` with `condition: service_healthy` to wait for `db`.
- **db**: Includes a healthcheck to verify PostgreSQL readiness.

**Usage**:
- Ensures `web` starts only after `db` is healthy.
- Check health: `docker inspect <db_container>`.

#### 11. Profiles
**Purpose**: Selectively start services based on use case (e.g., development, production).

**Configuration**:
- **web**: Assigned `profiles: [web]`.
- **db**: Assigned `profiles: [db]`.

**Usage**:
- Start only `web`: `docker compose up --profile web`.
- Start both: `docker compose up --profile web --profile db`.

#### 12. Lifecycle Hooks
**Purpose**: Run commands at specific container lifecycle stages (requires Compose CLI 2.30.0+).

**Configuration**:
- **web**:
  - `post_start`: Runs `echo "Web service started"` after startup.
  - `pre_stop`: Runs `echo "Web service stopping"` before stopping.

**Usage**:
- Useful for initialization or cleanup tasks.

#### 13. Compose Watch
**Purpose**: Automatically update services during development (requires Compose CLI 2.22.0+).

**Configuration**:
- **web**:
  - `sync`: Syncs `./web/src` to `/app/src`, ignoring `node_modules`.
  - `rebuild`: Rebuilds on `package.json` changes.

**Usage**:
- Run `docker compose up --watch` to enable live updates.

#### 14. Deploy (Swarm Mode)
**Purpose**: Configure deployment settings for Docker Swarm.

**Configuration**:
- **web**: Sets 2 replicas and resource limits (CPUs, memory).

**Usage**:
- Enable with `docker compose up` in a Swarm cluster.
- Optional for non-Swarm users.

#### Required Files
To use this file, ensure the following exist:
- `./base/Dockerfile`: Base image Dockerfile.
- `./web/Dockerfile-web`: Web service Dockerfile with `FROM base_image`.
- `./web/src/`: Source code directory.
- `./web/data/`: Data directory for bind mount.
- `./.env`: Environment variables file.
- `./db_password.txt`: Secret file.
- `./web_config.txt`: Config file.

#### Example Commands
| Command | Description |
|---------|-------------|
| `docker compose up --build` | Builds and starts all services. |
| `docker compose up --profile web` | Starts only the `web` service. |
| `docker compose up --watch` | Starts services with watch mode for development. |
| `docker compose config` | Shows resolved Compose file with interpolated variables. |
| `docker network ls` | Lists networks created by Compose. |
| `docker volume ls` | Lists volumes like `db_data`. |

#### Best Practices
- **Use Service Names**: Connect services using names (e.g., `postgres://db:5432`) for reliability.
- **Secure Secrets**: Use secrets for sensitive data instead of environment variables.
- **Leverage Profiles**: Use profiles to manage development vs. production services.
- **Optimize Builds**: Use dependent images to reduce build time and image size.
- **Enable Watch**: Use watch mode for faster development cycles.


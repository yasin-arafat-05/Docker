
<br>
<br>
<br>

## Advance docker compose file:

- [Layring_Docker_file_must_need_for_production](https://www.youtube.com/watch?v=hX2UAHhX8E8)
- [Docker_Volume_VS_bind_mount](https://www.youtube.com/watch?v=r_LgmqejAkA)
- 

<br>
<br>
<br>



```yaml
name: ecommerce

services: 
  #<-------------------1. database Container-------------------->
  db:
    image: postgres:16-alpine
    restart: unless-stopped
    env_file:
      - .env
    environment:
      POSTGRES_DB: ${DATABASE}
      POSTGRES_USER: ${DB_ROLE_NAME}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_INITDB_ARGS: "--encoding=UTF8 --locale=C"
    volumes:
      - type: volume
        source: postgres_data
        target: /var/lib/postgresql/data
        volume:
          nocopy: true
          size: 10G  # Database storage limit
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER -d $$POSTGRES_DB"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    networks:
      - private
    deploy:
      resources:
        limits:
          memory: 1G
          cpus: '1.0'
        reservations:
          memory: 512M
          cpus: '0.5'

  #<-------------------2.user server-------------------->
  user:
    build:
      context: backend
      dockerfile: user/DockerFile
      args:
        - ENVIRONMENT=production
    restart: unless-stopped
    env_file:
      - .env
    environment:
      DB_HOST: ${DB_HOST}
      DB_PORT: ${DB_PORT}
      IMAGE_STORAGE_PATH: /app/images
      MAX_STORAGE_SIZE: 1G
      LOG_LEVEL: INFO
    volumes:
      - type: volume
        source: img_data
        target: /app/images
        volume:
          nocopy: true
          size: 1G  # Exactly 1GB image storage
      - type: volume
        source: user_logs
        target: /app/logs
        volume:
          nocopy: true
          size: 100M
      - type: bind
        source: /etc/ssl/certs
        target: /etc/ssl/certs
        read_only: true
    depends_on:
      db:
        condition: service_healthy
    networks:
      - private
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 15s
      timeout: 10s
      retries: 3
      start_period: 40s
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '0.5'
        reservations:
          memory: 256M
          cpus: '0.25'

  #<-------------------3.product server-------------------->
  product:
    build:
      context: backend
      dockerfile: product/DockerFile
      args:
        - ENVIRONMENT=production
    restart: unless-stopped
    env_file:
      - .env
    environment:
      DB_HOST: ${DB_HOST}
      DB_PORT: ${DB_PORT}
      IMAGE_STORAGE_PATH: /app/images
      MAX_STORAGE_SIZE: 1G
      LOG_LEVEL: INFO
    volumes:
      - type: volume
        source: img_data
        target: /app/images  # Same volume shared with user service
        volume:
          nocopy: true
          size: 1G
      - type: volume
        source: product_logs
        target: /app/logs
        volume:
          nocopy: true
          size: 100M
      - type: bind
        source: /etc/ssl/certs
        target: /etc/ssl/certs
        read_only: true
    depends_on:
      db:
        condition: service_healthy
    networks:
      - private
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8001/health"]
      interval: 15s
      timeout: 10s
      retries: 3
      start_period: 40s
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '0.5'
        reservations:
          memory: 256M
          cpus: '0.25'

  #<-------------------4. Nginx: proxy----------------------->
  nginx:
    image: nginx:alpine 
    restart: unless-stopped
    ports:
      - "443:443"  # HTTPS
      - "80:80"    # HTTP redirect
    volumes:
      - type: bind
        source: ./nginx/nginx.conf
        target: /etc/nginx/nginx.conf
        read_only: true
      - type: bind
        source: ./nginx/conf.d
        target: /etc/nginx/conf.d
        read_only: true
      - type: volume
        source: img_data
        target: /usr/share/nginx/images
        read_only: true  # Nginx only reads images
      - type: bind
        source: ./ssl
        target: /etc/nginx/ssl
        read_only: true
      - type: volume
        source: nginx_logs
        target: /var/log/nginx
        volume:
          nocopy: true
          size: 500M
    depends_on:
      - user
      - product
    networks:
      - public
      - private
    deploy:
      resources:
        limits:
          memory: 256M
          cpus: '0.25'
        reservations:
          memory: 128M
          cpus: '0.1'

  #<-------------------5. Backup Service ------------------->
  backup:
    image: postgres:16-alpine
    restart: on-failure
    env_file:
      - .env
    volumes:
      - type: volume
        source: postgres_data
        target: /var/lib/postgresql/data
        read_only: true
      - type: volume
        source: backup_data
        target: /backups
        volume:
          nocopy: true
          size: 20G
      - type: bind
        source: ./backup/scripts
        target: /scripts
        read_only: true
    networks:
      - private
    command: >
      sh -c "
        echo 'Backup service initialized' &&
        tail -f /dev/null
      "
    profiles:
      - backup

  #<-------------------6. Monitoring ------------------->
  monitor:
    image: prom/prometheus:latest
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - type: bind
        source: ./monitoring/prometheus.yml
        target: /etc/prometheus/prometheus.yml
        read_only: true
      - type: volume
        source: prometheus_data
        target: /prometheus
        volume:
          nocopy: true
          size: 5G
    networks:
      - private
      - public
    profiles:
      - monitoring

#<----Volumes and networks------>
volumes:
  postgres_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/ecommerce/postgres  # Specific host path for control
  img_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/ecommerce/images
      size: 1G  # Exactly 1GB limit
  user_logs:
    driver: local
  product_logs:
    driver: local
  nginx_logs:
    driver: local
  backup_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/ecommerce/backups
  prometheus_data:
    driver: local

networks:
  public:
    driver: bridge
    driver_opts:
      com.docker.network.driver.mtu: 1500
  private:
    driver: bridge
    internal: false  # Allow controlled external access
    driver_opts:
      com.docker.network.driver.mtu: 1500

#<----Additional Production Configs------>
configs:
  nginx_config:
    file: ./nginx/nginx.conf
  prometheus_config:
    file: ./monitoring/prometheus.yml, 
```
<br>
<br>

### **1. 🔧 Extended Volume Syntax**
```yaml
volumes:
  - type: volume
    source: postgres_data
    target: /var/lib/postgresql/data
    volume:
      nocopy: true
      size: 10G  
```

**ব্যাখ্যা:** 
- `type: volume` - Docker managed volume use করছে
- `nocopy: true` - Host থেকে container এ data copy হবে না (performance)
```yaml
  product:
    build:
      context: .  
      dockerfile: backend/product/DockerFile
    restart: unless-stopped
    env_file:
      - .env
    environment:
      DB_HOST: db  
      DB_PORT: 5432
      LOG_LEVEL: INFO
    volumes:
      - img_data:/app/images
      - product_logs:/app/logs
    depends_on:
      db:
        condition: service_healthy
    #suppose এইটার জন্য, আমার docker container একটা volume থাকবে, আর, আমি নিচে যে config file এ লিখেছি,
 
volumes:
  postgres_data:
  img_data:
  user_logs:
  product_logs:
  nginx_logs:
  # এর জন্যও docker volume থাকবে এখন কথা হচ্ছে, এখন, আমার image গুলো direct docker volume থেকে map হবে তাই না ????
```
<br>

```txt
┌────────────────────────────────────────────────────────┐
│                  DOCKER VOLUMES                        │
│                                                        │
│  ┌─────────────┐  ┌───────────────┐ ┌─────────────┐    │
│  │postgres_data│  │   img_data    │ │ product_logs│    │
│  │             │  │               │ │             │    │
│  │ /var/lib/   │  │ /app/images/  │ │ /app/logs/  │    │
│  │ postgresql/ │  │               │ │             │    │
│  │ data        │  │ • product1.jpg│ │ • app.log   │    │
│  └─────────────┘  │ • product2.jpg│ │ • error.log │    │
│                   │ • user_avatar │ └─────────────┘    │
│                   └───────────────┘                    │
│                         ▲                              │
│                         │                              │
│                  Multiple Services                     │
│                 Share Same Volume                      │
│                         │                              │
└─────────────────────────┼──────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          │               │               │
          ▼               ▼               ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   user          │ │   product       │ │   nginx         │
│   Container     │ │   Container     │ │   Container     │
│                 │ │                 │ │                 │
│ /app/images/ ───┼─┼──► /app/images/ │ │ /usr/share/     │
│                 │ │                 │ │ nginx/images/   │
│ • Read/Write    │ │ • Read/Write    │ │ • Read Only     │
│ • Upload images │ │ • Access images │ │ • Serve images  │
└─────────────────┘ └─────────────────┘ └─────────────────┘

```
- `size: 10G` - Exactly 10GB storage limit

<br>
<br>

### **2. 🚀 Resource Limits & Reservations**
```yaml
deploy:
  resources:
    limits:
      memory: 1G
      cpus: '1.0'
    reservations:
      memory: 512M
      cpus: '0.5'
```

**ব্যাখ্যা:**
- `limits` - Maximum resource usage.(Ensures that a service does not consume more resources than specified, preventing it from monopolizing the host's resources.)

- `reservations` - Guaranteed minimum resources.(Ensures that the service gets at least the reserved resources when it starts, assuming the host has enough capacity.)

- **Prevents** one service সব resources consume করে ফেলা

### **3. 🏥 Advanced Health Checks**
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
  interval: 15s
  timeout: 10s
  retries: 3
  start_period: 40s  # ✅ Service startup time consider করে
```

### **4. 🔒 Security Hardening**
```yaml
volumes:
  - type: bind
    source: /etc/ssl/certs
    target: /etc/ssl/certs
    read_only: true  # ✅ Container can't modify host SSL certs
```

### **5. 📊 Monitoring Service**
```yaml
monitor:
  image: prom/prometheus:latest
  profiles:
    - monitoring  # ✅ Only runs when needed
```

### **6. 💾 Backup Service**
```yaml
backup:
  profiles:
    - backup  # ✅ Separate profile, always running না
  volumes:
    - type: volume
      source: postgres_data
      target: /var/lib/postgresql/data
      read_only: true  # ✅ Backup can only read, not modify
```

## ⚠️ **Problems in Your Current File:**

### **1. Volume Size Limit Actually Work করছে না**
```yaml
# ❌ এই line কাজ করবে না
size: 1G  # Docker Compose directly size limit support করে না

# ✅ Solution: Host level এ quota set করতে হবে
volumes:
  img_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/ecommerce/images
      # size: 1G  # Remove this line
```

### **2. Build Context Problem**
```yaml
# ❌ এই structure এ problem হবে
build:
  context: backend
  dockerfile: user/DockerFile

# ✅ Correction: Dockerfile path ঠিক করুন
build:
  context: .
  dockerfile: backend/user/DockerFile
```

## 🔧 **Corrected docker-compose.yml:**

```yaml
name: ecommerce

services: 
  #<-------------------1. database Container-------------------->
  db:
    image: postgres:16-alpine
    restart: unless-stopped
    env_file:
      - .env
    environment:
      POSTGRES_DB: ${DATABASE}
      POSTGRES_USER: ${DB_ROLE_NAME}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_INITDB_ARGS: "--encoding=UTF8 --locale=C"
    volumes:
      - postgres_data:/var/lib/postgresql/data  # ✅ Simplified
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_ROLE_NAME} -d ${DATABASE}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    networks:
      - private

  #<-------------------2.user server-------------------->
  user:
    build:
      context: .  # ✅ Corrected context
      dockerfile: backend/user/DockerFile
    restart: unless-stopped
    env_file:
      - .env
    environment:
      DB_HOST: db  # ✅ Direct service name
      DB_PORT: 5432
      LOG_LEVEL: INFO
    volumes:
      - img_data:/app/images
      - user_logs:/app/logs
    depends_on:
      db:
        condition: service_healthy
    networks:
      - private
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 15s
      timeout: 10s
      retries: 3
      start_period: 40s

  #<-------------------3.product server-------------------->
  product:
    build:
      context: .  # ✅ Corrected context
      dockerfile: backend/product/DockerFile
    restart: unless-stopped
    env_file:
      - .env
    environment:
      DB_HOST: db  # ✅ Direct service name
      DB_PORT: 5432
      LOG_LEVEL: INFO
    volumes:
      - img_data:/app/images
      - product_logs:/app/logs
    depends_on:
      db:
        condition: service_healthy
    networks:
      - private
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8001/health"]
      interval: 15s
      timeout: 10s
      retries: 3
      start_period: 40s

  #<-------------------4. Nginx: proxy----------------------->
  nginx:
    image: nginx:alpine 
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - img_data:/usr/share/nginx/images:ro  # ✅ Read-only
      - nginx_logs:/var/log/nginx
    depends_on:
      - user
      - product
    networks:
      - public
      - private

#<----Volumes and networks------>
volumes:
  postgres_data:
  img_data:
  user_logs:
  product_logs:
  nginx_logs:

networks:
  public:
    driver: bridge
  private:
    driver: bridge
```

## 📁 **Required Directory Structure:**

```
ecommerce/
├── docker-compose.yml
├── .env
├── nginx.conf
├── shared/                    # ✅ Common utilities
│   ├── database/
│   │   ├── models.py
│   │   └── connection.py
│   ├── utils/
│   │   ├── auth.py
│   │   └── s3_client.py
│   └── requirements/
│       ├── base.txt
│       └── prod.txt
├── backend/
│   ├── user/
│   │   ├── DockerFile
│   │   ├── requirements.txt
│   │   └── app/
│   │       ├── main.py
│   │       └── routers/
│   ├── product/
│   │   ├── DockerFile
│   │   ├── requirements.txt
│   │   └── app/
│   ├── inventory/            # ✅ Future service
│   ├── payment/              # ✅ Future service  
│   └── analytics/            # ✅ Future service
├── frontend/                 # ✅ React/Vue app
├── monitoring/
│   └── prometheus.yml
└── backup/
    └── scripts/
```

## DockerFile Example:

```DockerFile
# Stage 1: Builder (for installing dependencies)
FROM python:3.10-slim AS builder

# Set working directory
WORKDIR /app

# Set environment variables (no layer created, just metadata)
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app:/shared

# Install system dependencies (single layer for system packages)
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy shared and service-specific requirements (separate layer for requirements)
COPY ./shared/requirements/base.txt ./shared/requirements/prod.txt /shared/requirements/
COPY ./backend/user/requirements.txt /app/requirements.txt

# Install dependencies (single layer for pip installs)
RUN pip install --user --no-cache-dir -r /shared/requirements/base.txt -r /shared/requirements/prod.txt -r /app/requirements.txt

# Stage 2: Runtime (final image)
FROM python:3.10-slim

# Set working directory
WORKDIR /app

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app:/shared \
    PATH=/home/appuser/.local/bin:$PATH

# Create non-root user (single layer for user setup)
RUN useradd -m appuser

# Copy installed dependencies from builder
COPY --from=builder /root/.local /home/appuser/.local

# Copy shared utilities (separate layer for shared code)
COPY ./shared /shared

# Copy service code (separate layer for app code)
COPY ./backend/user/app /app

# Set ownership for security
RUN chown -R appuser:appuser /app /shared

# Switch to non-root user
USER appuser

# Expose port (metadata, no layer)
EXPOSE 8000

# Run the application (single layer for command)
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]

```



## 🚀 **Deployment Commands:**

```bash
# Start only main services
docker compose up -d db user product nginx

# Start with backup
docker compose --profile backup up -d

# Start with monitoring
docker compose --profile monitoring up -d

# Check status
docker compose ps

# View logs
docker compose logs -f user
```

## 🎯 **Key Benefits of This Setup:**

1. **✅ Production Ready** - Health checks, resource limits
2. **✅ Scalable** - Easy to add more services
3. **✅ Maintainable** - Clean structure
4. **✅ Secure** - Read-only volumes, network isolation
5. **✅ Monitorable** - Logs, metrics collection


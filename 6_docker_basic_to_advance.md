
<br>
<br>

# 🚀 Day-1: Docker Installation & Getting Started

<br>
<br>

## 🔧 Step 1: Install Docker

👉 Official Docs:

- 🌐 Docker Engine (Linux): [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)
- 🖥️ Docker Desktop (Windows): [https://docs.docker.com/desktop/setup/install/windows-install/](https://docs.docker.com/desktop/setup/install/windows-install/)
- 📋 Docker Release Notes: [https://docs.docker.com/desktop/release-notes/](https://docs.docker.com/desktop/release-notes/)

> 💡 After installation, make sure Docker Desktop is running (Windows/macOS), or the Docker daemon is active (Linux).

---

## ✅ Step 2: Verify Installation

Run the following commands in your terminal to ensure Docker is properly installed:

```bash
docker --version                   # ✅ Docker version
docker-compose --version          # ✅ Docker Compose version
docker info                       # ℹ️  General system info
docker info --format '{{.ServerVersion}}'   # 🎯 Server version only
docker info --format '{{.ClientInfo}}'      # 🎯 Client info only
docker info | grep "Docker Root Dir:" # 📁 Find the Docker root directory path
sudo ls -alsh  /var/lib/docker # 📂 List contents of the Docker root directory
total 52K
4.0K drwx--x--- 12 root root 4.0K May 24 23:13 .                # This directory itself
4.0K drwxr-xr-x 74 root root 4.0K May 15 21:42 ..               # Parent directory
4.0K drwx--x--x  4 root root 4.0K Aug 25  2024 buildkit         # 💡 Used for efficient image builds with BuildKit
4.0K drwx--x---  2 root root 4.0K May 24 23:13 containers       # 📦 Stores container metadata and logs
4.0K -rw-------  1 root root   36 Aug 25  2024 engine-id        # 🆔 Unique ID for this Docker engine instance
4.0K drwx------  3 root root 4.0K Aug 25  2024 image            # 🖼️ Stores image metadata and layers
4.0K drwxr-x---  3 root root 4.0K Aug 25  2024 network          # 🌐 Docker network configuration and data
4.0K drwx--x--- 32 root root 4.0K May 24 23:13 overlay2         # 📂 Default storage driver directory (overlay2)
4.0K drwx------  4 root root 4.0K Aug 25  2024 plugins          # 🔌 Installed Docker plugins (e.g. volume or network)
4.0K drwx------  2 root root 4.0K May 24 23:13 runtimes         # ⏱️ Runtime binaries (like runc or containerd)
4.0K drwx------  2 root root 4.0K Aug 25  2024 swarm            # 🐝 Docker Swarm cluster data (if used)
4.0K drwx------  2 root root 4.0K May 24 23:13 tmp              # 🧪 Temporary files used by Docker daemon
4.0K drwx-----x  3 root root 4.0K May 24 23:13 volumes          # 📦 Volume data used by containers

````

````bash
🧠 Summary
| ------------- | --------------------------------------------------------- |
| Folder        | Description                                               |
| ------------- | --------------------------------------------------------- |
| `buildkit/`   | Docker's modern build engine files                        |
| `containers/` | Metadata and runtime logs for each container              |
| `engine-id`   | Unique ID for your Docker engine installation             |
| `image/`      | Metadata and layers for container images                  |
| `network/`    | Network config (bridge, host, etc.)                       |
| `overlay2/`   | Union file system used by Docker to store container files |
| `plugins/`    | Installed plugins                                         |
| `runtimes/`   | Container runtime definitions (e.g., `runc`)              |
| `swarm/`      | Swarm mode cluster information                            |
| `tmp/`        | Temporary working data                                    |
| `volumes/`    | Persistent volumes mounted into containers                |
| ------------- | --------------------------------------------------------- |
````

### 🐧 For Linux users only:

```bash
sudo systemctl status docker      # 🔍 Check Docker daemon status
sudo journalctl -u docker         # 📜 Docker logs
```

---

## 🧪 Step 3: Test Docker

Run a test container to verify everything works:

```bash
docker run hello-world            # 🚀 Runs a simple test container
docker ps                         # 📋 List running containers
docker ps -a                      # 📋 List all containers (including stopped)
docker images                     # 🗂️  List downloaded images
```

---

## 🎉 Step 4: Fun Example – Run NGINX Web Server

```bash
docker pull nginx                 # ⬇️ Pull the latest NGINX image
docker run -d -p 8080:80 nginx    # 🌐 Start web server on localhost:8080
```

🖥️ Open your browser and go to: [http://localhost:8080](http://localhost:8080) or use IP, in my case [http://192.168.1.22:8080](http://192.168.1.22:8080)

> You’ll see the default NGINX welcome page served from inside a Docker container!

---

## 🧹 Step 5: Clean Up Docker Resources

```bash
docker system df                  # 📊 View disk usage
docker system prune               # 🧼 Clean up unused containers, networks, images
```

> ⚠️ `docker system prune` will prompt you before deletion. Make sure you don’t need any data it will remove!

---

## 📝 Summary

| Command                          | Description              |
| -------------------------------- | ------------------------ |
| `docker --version`               | Show Docker version      |
| `docker run hello-world`         | Test Docker setup        |
| `docker pull nginx`              | Download the NGINX image |
| `docker run -d -p 8080:80 nginx` | Start NGINX container    |
| `docker system prune`            | Remove unused data       |

---

<br>
<br>

# 🚀 Day-2: Docker Images and search images.

<br>
<br>

## 🐳 What is a Docker Image?

A **Docker image** is a lightweight, standalone, and executable package that includes everything needed to run a piece of software, including the code, runtime, libraries, and dependencies. Think of it as a snapshot or blueprint of an application! 📸

- **Immutable**: Docker images are read-only templates.
- **Layered**: Built in layers for efficiency (e.g., base OS, libraries, app code).
- **Portable**: Run consistently across different environments (local, cloud, etc.).

Docker images are stored in registries like **Docker Hub**, which is like a "GitHub for Docker images." � registry

## 🔍 Why Search for Docker Images?

Searching for Docker images allows you to:
- Find pre-built images for popular software (e.g., `nginx`, `python`, `mysql`).
- Save time by reusing community-maintained or official images.
- Ensure you're using trusted and optimized images for your projects.

Docker Hub hosts millions of images, and searching efficiently helps you find the right one for your needs! 🌐

## 🚀 Steps to Work with Docker Images

1. **Install Docker**: Ensure Docker is installed and running.
2. **Search for Images**: Use the `docker search` command to find images on Docker Hub.
3. **Pull an Image**: Download the desired image to your local machine using `docker pull`.
4. **Run a Container**: Create a container from the image using `docker run`.
5. **List Images**: View all downloaded images with `docker images`.

## 📝 Example Commands

Here are some practical commands to get you started:

### 1. Search for a Docker Image

```bash
# Search for images with the specified name in Docker Hub
docker search image-name

# Search for images and limit the number of results
docker search --limit 5 image-name

# Search for images with automated builds
docker search --filter is-automated=true image-name

# Search for official images only
docker search --filter is-official=true image-name

# Search for images with a minimum number of stars
docker search --filter stars=10 image-name

# Search for images and format the output
docker search --format "{{.Name}}: {{.Description}}" image-name

# Search for images and include truncated descriptions
docker search --no-trunc image-name

# Search quietly, outputting only image names
docker search -q image-name
```
This lists available `nginx` images on Docker Hub, showing details like name, description, and stars. ⭐

### 2. Pull a Docker Image
To download the official `nginx` image:
```bash
docker pull nginx
```

### 3. List Local Images
To see all images on your machine:
```bash
docker images
```

### 4. Run a Container
To start a container from the `nginx` image:
```bash
docker run -d -p 8080:80 nginx
```
This runs `nginx` in the background (`-d`) and maps port `8080` on your machine to port `80` in the container.

### 5. Remove an Image
To delete an image (replace `<image_id>` with the actual ID from `docker images`):
```bash
docker rmi <image_id>
```
> [!NOTE]
> In this series of docker zero to zero I am going to use these images as well.

```bash
ibraransaridocker/network-debug-tools   latest    
ibraransaridocker/whoami                latest    
ibraransaridocker/coming-soon           latest    
ibraransaridocker/under-maintenance     latest    
ibraransaridocker/nginx-demo            latest    
ibraransaridocker/dotfiles              latest   
```

<br>
<br>

# 🐳 **Day 3: Mastering `docker run` Command - With examlpes!**

<br>
<br>

## 🔹 Basic Usage

```bash
# 🏃 Run a container & remove it after execution
docker run --rm ubuntu echo "Hello from container"

# 🖥️ Run and ping a public DNS (Google) from busybox container
docker run --rm busybox ping 8.8.8.8
```

---

## 🔹 Interactive & Detached Modes

```bash
# 🌐 Run nginx demo container (foreground by default)
docker run nginx

# 🧪 Run with interactive terminal
docker run -it ubuntu

# 🔄 Detached (background) + interactive
docker run -itd --name=dia ubuntu:latest /bin/bash

## -i: Keep STDIN open even if not attached.
## -t: Allocate a pseudo-TTY (terminal).
# Runs the container in the foreground, attaching your terminal to the container's shell. You can interact with it directly (e.g., run commands like bash).
# The container stops when you exit the shell (e.g., by typing exit).

## -d: Run container in detached mode (i.e., in the background).
# Includes the -d (detached) flag, which runs the container in the **background**.
# The container starts and runs independently of your terminal. You won't get an interactive shell unless you explicitly attach to it later (e.g., using docker attach or docker exec).

## Key Difference:
# -it: Runs the container in the foreground with an interactive terminal.
# -itd: Runs the container in the background, detached from your terminal.

```

---

## 🔹 Custom Images and Tags

```bash
# 🛠️ network-debug-toolss with specific tag
docker run -it --rm --name=dia ibraransaridocker/network-debug-tools:latest /bin/bash

```

---

## 🔹 Networking Options

```bash
# 🌐 Use a custom network
docker network create dia_network
docker run -it --rm --network=dia_network ubuntu

# 🧷 Custom DNS
docker run -it --name=dia --dns=8.8.8.8 ubuntu:latest /bin/bash
#cat /etc/resolv.conf

# 🧭 Network alias
docker run -it --rm --name=dia --network=dia_network --network-alias=web ibraransaridocker/network-debug-tools /bin/bash
# Inspect the Container alias: docker inspect dia
docker run -it --rm --name=dia1 --network=dia_network --network-alias=db ibraransaridocker/network-debug-tools /bin/bash 
# From the second container, ping the alias: ping web or db

## The --network-alias is helpful when:
#- You're using service discovery among microservices (e.g., web, db, cache) without needing fixed container names or IPs.
#- You want to refer to a container by a specific hostname within a custom Docker network.
```

---

## 🔹 Volume Mounts (Persistent Storage)

```bash
# 💽 Mount volume from host to container
docker run -it --rm --name=dia -p 8080:80 ibraransaridocker/nginx-demo 
docker run -it --rm --name=dia -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html/ nginx 

# 🔒 Read-only volume mount
docker run -itd --name=dia -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html:ro nginx 

# docker exec -it dia bash
# cd /usr/share/nginx/html
# echo "Test" >> index.html

```

---

## 🔹 Environment Variables & User Settings

```bash
# 🌿 Set environment variable
docker run -it --name=dia -e user=devopsinaction ubuntu env
docker run -itd --name=dia -e user=devopsinaction ubuntu /bin/bash
# docker exec -it dia bash
# printenv

# 👤 Run container with specific UID & GID
docker run -it --name=dia -u 1000:1000 ubuntu /bin/bash
# id
```

---

## 🔹 Port Mapping

```bash
docker run -it --rm --name=dia -p 8080:80 ibraransaridocker/nginx-demo 
docker run -it --rm --name=dia -p 8080:80 -p 8081:80 ibraransaridocker/nginx-demo 
docker run -it --rm --name=dia -p 8080:80 -p 8000-8005:8000-8005 ibraransaridocker/nginx-demo 
```

---

## 🔹 Working Directory & Entrypoint

```bash
# 📁 Set working directory to /app
docker run -it --name=dia -w /app node:alpine sh

# 🚪 Override the default entrypoint
docker run -it --name=dia --entrypoint=/bin/sh ubuntu:latest
```

---

## 🔹 Resource Limits

```bash
# 🧠 Limit memory and CPU usage
docker run -it --name=dia --memory="256m" --cpus="1.0" ibraransaridocker/network-debug-tools
docker stats dia
#load test inside container
# apt update
# apt install stress
# stress --cpu 1 --vm 1 --vm-bytes 300M --timeout 30s

```

---

## 🔹 Restart Policies

```bash
# 🔁 Auto-restart container on failure
docker run -dit --name=dia --restart=always nginx

```
## 🔹 Advanced Use Cases

```bash
# ❤️ Set custom hostname
docker run -itd --name=dia --hostname=webserver ubuntu:latest /bin/bash
# docker exec -it dia bash
# hostname
# cat /etc/hostname

# 💉 Add health check
docker run -itd --name=dia --health-cmd="curl --fail http://localhost || exit 1" --health-interval=30s nginx

# 🔍 Share host’s PID namespace
docker run -itd --name=dia --pid=host ubuntu:latest /bin/bash
```

---

## 🔹 Helpful Tips 💡

* Use `--rm` for disposable containers.
* Combine `-itd` for interactive **and** background usage.
* Bind mount volumes (`-v`) for local development.
* Use `--env` or `--env-file` to pass environment variables in bulk.
* Health checks help in container orchestration.

---

## ✅ Most Common Flags Reference (Cheat Sheet)

| Flag                  | Description                  |
| --------------------- | ---------------------------- |
| `-it`                 | Interactive terminal         |
| `--rm`                | Remove after exit            |
| `-d`                  | Run in background (detached) |
| `-p`                  | Port mapping                 |
| `-v`                  | Mount volume                 |
| `--name`              | Set container name           |
| `--env`, `-e`         | Set environment variable     |
| `--network`           | Connect to a custom network  |
| `--cpus` / `--memory` | Set resource limits          |
| `--restart`           | Set restart policy           |
| `--entrypoint`        | Override entry command       |

<br>
<br>

# 🐳 **Day 4: Mastering docker ps Command - With examlpes!**

<br>
<br>


This guide provides a deep dive of the `docker ps` command, including how to list, filter, format, and count containers and images.
Docker ps command lists containers on your system. It displays useful information such as container IDs, image names, uptime, exposed ports and many more.. It's an essential tool to working with the containers. 🐳📋


## 📋 Basic Usage


### ❔ Get Help using help command
```
docker ps --help
```
```
Usage:  docker ps [OPTIONS]

List containers

Aliases:
  docker container ls, docker container list, docker container ps, docker ps

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Format output using a custom template:
                        'table':            Print output in table format with column headers (default)
                        'table TEMPLATE':   Print output in table format using the given Go template
                        'json':             Print in JSON format
                        'TEMPLATE':         Print output using the given Go template.
                        Refer to https://docs.docker.com/go/formatting/ for more information about formatting output with templates
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display container IDs
  -s, --size            Display total file sizes

```


### 1. Show Running Containers

```
docker ps
```

### 2. Show All Containers (Running + Stopped)

```
docker ps -a
```

### 3. Show Disk Usage per Container

```
docker ps -s
docker ps -as

```

### 4. Show Last N Created Containers

```
docker ps --last 4
# or
docker ps -n 4
```

### 5. Show the Most Recently Created Container

```
docker ps -l
# or
docker ps --latest
```

### 6. Show Only container IDs

```
docker ps -q
docker ps -aq
# or
docker ps --quiet
docker ps -a --quiet
```

---

## 🔍 Filtering Containers

### Filter by Name

```
docker ps -f "name=test-container"
or
docker ps --filter "name=test-container"
```

### Filter by Container ID

```
docker ps -a -f "id=aca09d8707d8"
or
docker ps -a --filter "id=aca09d8707d8"
```

### Filter by Status

```
docker ps --filter status=running
docker ps --filter status=exited
```

### Filter by Ancestor (Image)

```
docker ps --filter ancestor=nginx
```

### Filter by Creation Time before/after container

```
docker ps -f before=whoami
docker ps -f since=nd
```
**whoami and nd is container name.**

### Sort containers by creation time (most recent first)

```
docker ps -a --format '{{.CreatedAt}}\t{{.Names}}' | sort -r
```
### Filter by Network, Volume, Port, Label, Health

```
docker ps --filter publish=8080
docker ps --filter "label=com.example.app=web"
docker ps --filter health=healthy
docker ps --filter network=bridge
docker ps --filter volume=myvol
docker ps -a --filter exited=0
```

### Multiple Filters

```
docker ps --filter status=running --filter ancestor=nginx
```

## 🔢 Counting Containers

### Count Running Containers

```
docker ps -q | wc -l
```

### Get Count via Docker Info

```
docker info --format '{{json .ContainersRunning}}'
```

### Count All Containers (Running + Stopped)

```
docker ps -a -q | wc -l
```

### Count Matching Containers by Image Name

```
docker ps | grep ibraransaridocker/coming-soon | wc -l
```

## 🛠️ Custom Formatting

### Simple Name Format

```
docker ps --format '{{.Names}}'
or
docker ps -a --format "{{.Names}}"
or
docker ps -a --format "table {{.Names}}"
```

### Detailed Format with Variables

```
FORMAT="\nID\t{{.ID}}\nIMAGE\t{{.Image}}\nCOMMAND\t{{.Command}}\nCREATED\t{{.RunningFor}}\nSTATUS\t{{.Status}}\nPORTS\t{{.Ports}}\nNAMES\t{{.Names}}\n"
docker ps -a --format="$FORMAT"
```

### Tabular Format Output

```
docker ps -a --format "table {{.Names}}\t{{.ID}}\t{{.Status}}\t{{.Ports}}\t{{.Image}}\t{{.Command}}\t{{.RunningFor}}"
```
---

## 🔍 Inspect Available Format Fields

### View Raw Output in JSON

```
docker ps --format '{{json .}}' | jq
```

### List All Available Format Keys

```
docker ps --format '{{json .}}' | jq -r 'keys_unsorted[]' | sort -u
```

### List All Format Keys from All Containers

```
# Create sample container to provide all keys
docker run -itd \
  --name test-container \
  --hostname custom-hostname \
  --env MY_ENV=example \
  --label app=demo \
  --label com.example.app=web \
  --mount type=bind,source=/tmp,target=/app/tmp,readonly \
  --mount type=volume,source=myvol,target=/app/data \
  --network bridge \
  --publish 8080:80 \
  --restart unless-stopped \
  --memory 512m \
  --cpu-shares 512 \
  --health-cmd="curl -f http://localhost || exit 1" \
  --health-interval=30s \
  --health-retries=3 \
  --health-start-period=5s \
  nginx:latest

# Run to get key
docker inspect $(docker ps -q) | jq '.[0] | keys_unsorted' | sort -u
# or
docker inspect $(docker ps -q) | jq -r '.[0] | keys_unsorted[]' | sort -u

```

<!--
This line will be hidden and not rendered.
So you can hide notes or stuff here.
### Alternative using grep and sed (less accurate)

```
docker ps --format '{{json .}}' | jq -s 'map(keys) | add | unique | sort'
or
docker ps --format '{{json .}}' | grep -o '"[^"]*":' | sed 's/"//g' | sed 's/://g' | sort -u
```

<details>
  <summary>Click to expand</summary>

  Here is some hidden content.
  You can put multiple lines here.

</details>



docker inspect $(docker ps -q) | jq -r '.[0].State | keys_unsorted[]'

docker inspect $(docker ps -q) | jq -r '.[0].State.Health.Status'

docker inspect $(docker ps -q) | jq -r '.[0].State.Health'


-->



---

## 🧩 All Available `--format` Fields

Here are all the fields you can use with `--format`:

| Field         | Description                               |
| ------------- | ----------------------------------------- |
| `.ID`         | Container ID                              |
| `.Names`      | Container name(s)                         |
| `.Image`      | Image name                                |
| `.ImageID`    | Image ID                                  |
| `.Command`    | Startup command                           |
| `.CreatedAt`  | Creation time                             |
| `.RunningFor` | Time running                              |
| `.Ports`      | Port mappings                             |
| `.Status`     | Status string                             |
| `.Size`       | Disk usage size (needs `-s`)              |
| `.Labels`     | All labels                                |
| `.Label`      | Specific label (e.g., `{{.Label "key"}}`) |
| `.Mounts`     | Mount points                              |
| `.Networks`   | Networks attached                         |
| `.State`      | Running, exited, paused, etc.             |
| `.Health`     | Healthcheck status                        |

---
## 📚 More Info

For full details, check the [official Docker formatting docs](https://docs.docker.com/engine/cli/formatting/) 📘

<br>
<br>

# day:10: 🐳 Docker Resource Management Guide

<br>
<br>

Managing system resources like CPU, memory, disk, and I/O is crucial in production environments to ensure containers behave predictably and don't starve the host or other containers.

## 📌 Why Resource Management Matters?

- Prevent 🛑 one container from eating up all system resources.
- Ensure fair use and better system **stability** and **performance**.
- Protect the host and other containers from **resource exhaustion**.
- Simulate production-like limits in development.
- Efficient Use of Infrastructure



# Docker Resource Limits: Key Components

Docker resource limits allow developers to control how much CPU, memory, and other system resources a container can use. Below are the primary components for configuring these limits.
## 1. Memory Limits

- **`--memory` (`-m`)**  
  Sets a **hard limit** on memory. The container cannot exceed this amount.  
  **Example:** `-m 512m` (limits memory usage to 512 MB)

- **`--memory-reservation`**  
  Sets a **soft limit** (baseline memory usage). The container can exceed it if the host has spare memory, but will be throttled during high usage.

- **`--memory-swap`**  
  Controls the total memory (RAM + swap).  
  - Set it **equal to** `--memory` to **disable swap**.  
  - Set it **greater than** `--memory` to **allow swapping**.  
## 2. CPU Limits

- **`--cpus`**  
  Limits the container to a specific number of CPUs or fractions.  
  **Example:** `--cpus=1.5` (limits to 1.5 CPU cores)

- **`--cpu-shares`**  
  Sets a **relative CPU weight**.  
  - Default: `1024`  
  - Higher values mean higher CPU priority during contention.  
  **Example:** `--cpu-shares=2048`

- **`--cpu-quota`** and **`--cpu-period`**  
  Enforce **CPU throttling** based on time quotas.  
  **Example:**  
\--cpu-period=100000
\--cpu-quota=50000

> Limits the container to **50% of a CPU every 100 ms**.
## 3. I/O Limits

- **`--device-read-bps` / `--device-write-bps`**  
Throttle read/write speeds to devices (in bytes per second).  
**Example:** `--device-read-bps=/dev/sda:10mb`

- **`--device-read-iops` / `--device-write-iops`**  
Limits the number of **I/O operations per second (IOPS)**. Helps manage disk bandwidth in I/O-constrained environments.

> [!attention] 
> Be mindful when configuring swap on your Docker hosts. Swap is slower than memory but can provide a buffer against running out of system memory 

---
# 🚀 Deploy Portainer to View Docker Resource Limits via GUI

Easily monitor and manage Docker resources with **Portainer**'s user-friendly interface.

```bash
docker run -d \
  -p 9000:9000 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  portainer/portainer-ce:latest
```

🔹 **Port**: Access the GUI at `http://localhost:9000`
🔹 **Volume**: Mounts Docker socket for management access
🔹 **Restart Policy**: Ensures Portainer starts on system reboot

🧭 Once running, open your browser and head to `http://localhost:9000` in my case it is `http://192.168.192.221:9000` to complete setup and start managing containers with resource insights!

---
## 🧠 Memory Limits 

### ➕ Set Memory Limit (Test Cases)

```bash
# Get help
docker run --help | grep memory

# Run container with memory
docker run -itd --name=mem -m 512m ibraransaridocker/network-debug-tools:latest
# 📌 This restricts the container to **512MB of RAM**. If the container tries to use more, it will be **killed** (OOM - Out of Memory).

# Check assigned memory
docker inspect --format='{{.HostConfig.Memory}}' mem | awk '{print $1/1024/1024 " MB"}'

# See assigned memory
docker stats mem

# 🧪 Stress test inside your container
docker exec -it mem /bin/bash
apt update && apt install -y stress
stress --vm 2 --vm-bytes 900M --timeout 30s

# Watch live OOM
sudo dmesg -w | grep -i 'killed process'

# Check results
sudo dmesg | grep -i 'killed process'
sudo journalctl -k | grep -i 'killed process'
````

### ➕ Memory + Swap (Test Cases)

```bash
# Get help
docker run --help | grep swap

# Syntax
docker run -dit --memory="[ram]" --memory-swap="[ram+swap]" [image]

# For example, to run a container with a 512 MB RAM limit and 1GB of swap memory, type:
# Here swap memory from our system memory for linux type btop
# by giving swap memoery we can use swap memory if containter exit the memory that we assign 
docker run -itd --name=swap -m 512m --memory-swap=1g ibraransaridocker/network-debug-tools:latest

# Check assigned memory
docker inspect --format='{{.HostConfig.Memory}}' swap | awk '{print $1/1024/1024 " MB"}'
docker inspect --format='{{.HostConfig.MemorySwap}}' swap | awk '{print $1/1024/1024 " MB"}'

# Connect container and install stress tool
docker exec -it swap bash
apt update && apt install -y stress stress-ng 

# 🧪 Test Case 1: Soft Memory Load (within 512MB)
# 🔹 Goal: Should NOT crash
stress-ng --vm 1 --vm-bytes 400M --vm-keep -t 20s

# 🧪 Test Case 2: Exceed RAM, Stay Under Swap Limit (700MB)
# 🔹 Goal: Use swap, no crash
stress-ng --vm 1 --vm-bytes 700M --vm-keep --vm-method all -t 30s

# 🧪 Test Case 3: Exceed Total Limit (>1GB) → Trigger OOM
# 🔹 Goal: Container crashes due to memory overuse
stress-ng --vm 1 --vm-bytes 2G --vm-keep --vm-method all -t 60s

# Watch live
docker stats swap

# Watch live OOM
sudo dmesg -w | grep -i 'killed process'
# Check results
sudo dmesg | grep -i 'killed process'
sudo journalctl -k | grep -i 'killed process'
```

---
## 🧮 CPU Limits

### 🎚️ Set CPU Quota (Absolute )Limit

```bash
# Get help
docker run --help | grep cpu

# Check available cpu on host
docker run --rm busybox nproc

# Run container with 512 CPU
docker run -itd --name=cpu --cpus=1.5 ibraransaridocker/network-debug-tools:latest
Example: `--cpus=1.5` lets container use 1.5 CPUs.

# Inspect CPU
docker inspect --format='{{.HostConfig.NanoCpus}}' cpu | awk '{printf "%.1f\n", $1 / 1000000000}'

# Stress test
docker exec -it cpu apt update
docker exec -it cpu apt install -y stress
docker exec -it cpu stress --cpu 2 --timeout 30s
- This uses 2 CPU workers (threads) but the container is still limited to 1.5 CPUs.   
- This lets you observe throttling or how Docker enforces CPU limits.

#🧮 How CPU % works in `docker stats`
- The **CPU %** shown is **per host logical CPU core**.
- If you see `150%`, it means the container is using **1.5 full logical CPUs worth of processing power**.  
- If you had 4 logical cores and ran a container with `--cpus=2`, the container could hit `200%`.

# Stats
docker stats cpu
```

---
## 🔢 Limit Number of Processes (PIDs)  📦

```bash
# 🔐 Restrict container to max **5 processes**. Prevents fork bombs 🧨.
docker run -itd --name=pid --pids-limit=5 ubuntu

# Test pid
sleep 500 & sleep 500 & sleep 500 & sleep 500 & sleep 500
or 
for i in {1..10}; do sleep 500 & done

# Check pid
docker stats pid
```

---
## 🎮 GPU Access (NVIDIA)

```
# Run an Ubuntu container with access to all GPUs, then execute 'nvidia-smi' to check NVIDIA GPU status
docker run -it --rm --gpus all ubuntu nvidia-smi

# Use the device option to specify GPUs. For example:
docker run -it --rm --gpus device=GPU-3a23c669-1f69-c64e-cf85-44e9b07e7a2a ubuntu nvidia-smi

# Exposes that specific GPU.
docker run -it --rm --gpus '"device=0,2"' ubuntu nvidia-smi

# Set NVIDIA capabilities
docker run --gpus 'all,capabilities=utility' --rm ubuntu nvidia-smi
```
## 💽 Block I/O Limits (Disk)

```bash
💽 Bandwidth Limit
docker run --device-read-bps /dev/sda:1mb nginx
docker run --device-write-bps /dev/sda:2mb nginx

🔄 IOPS Limit
docker run --device-read-iops /dev/sda:100 nginx
docker run --device-write-iops /dev/sda:100 nginx

🎚 Set Priority Weight
docker run --blkio-weight-device "/dev/sdb:600" nginx
```

---
# 🧪 Cleanup Lab
To remove **all(Running+Stopped) Docker containers** forcefully, you can use the following command:
```bash
docker rm -f $(docker ps -aq)
````
🧼 This will:
* Stop any running containers
* Remove **all** containers (both running and exited)
> ⚠️ **Warning:** This action is irreversible. Make sure you don't need any of the containers before running it!

---
## 🧪 Best Practices

- ✅ Always **set memory and CPU limits** in production.
- ✅ Use `docker stats` to monitor containers.
- ✅ Use `docker inspect` for detailed container config.
- ✅ Don’t forget about **disk I/O** and **PID** limits.

---
## 📚 Resources
* [Docker Official Docs - Resource Limits](https://docs.docker.com/config/containers/resource_constraints/)
* [Cgroups Explained](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/resource_management_guide/ch01)
* [Linux Performance Tools](https://brendangregg.com/linuxperf.html)

## 🧪 Practical Time (Try from yourself)

``` Bash
# No Swap Allowed:
docker run --memory="300m" --memory-swap="300m" ubuntu
- The container is restricted to using only 300 MB of RAM, with no swap.

# Double the Memory if Host Has Swap:
docker run --memory="300m" ubuntu
- If `--memory-swap` is not set, the container can use double the specified memory (600 MB) if the host has swap configured.

# Unlimited Swap:
docker run --memory="300m" --memory-swap=-1 ubuntu
- The container can use unlimited swap, limited only by what’s available on the host.

# --memory-swappiness`: Controlling Swap Usage
The `--memory-swappiness` option controls the tendency of the kernel to move processes out of physical memory and onto swap space.

## Low Swappiness (0):
docker run --memory-swappiness=0 ubuntu
- Minimizes swapping, keeping processes in RAM for better performance.

## High Swappiness (100):
docker run --memory-swappiness=100 ubuntu
- Allows processes to be moved to swap space more aggressively.

## --memory-reservation: Setting a Soft Limit
The --memory-reservation option sets a soft limit on the physical memory a container should ideally use. It must be set lower than the --memory limit to take precedence.
docker run --memory="500m" --memory-reservation="300m" ubuntu
- In this example, the container tries to use up to 300 MB of RAM under normal conditions but can use up to 500 MB if needed.

## Unlimited Memory, Limited Kernel Memory:
docker run --kernel-memory="200m" ubuntu
- The container has no limit on overall memory but limits the kernel memory to 200 MB.

## Limited Memory, Unlimited Kernel Memory:
docker run --memory="500m" ubuntu
- The container’s total memory is limited to 500 MB, but the kernel memory is unlimited within this limit.

## Limited Memory, Limited Kernel Memory:
docker run --memory="500m" --kernel-memory="200m" ubuntu
- Both the total memory and kernel memory are limited to ensure balanced resource usage and help debug memory issues.
```

## 🔍Inspect CPU + Memory + SWAP
```
# Inspect CPU + Memory + SWAP
docker inspect --format='Memory={{.HostConfig.Memory}} Swap={{.HostConfig.MemorySwap}} CPU={{.HostConfig.CPUShares}}' CONTAINER_NAME | awk '{printf "Memory: %.0f MB\nSwap: %.0f MB\nCPU Shares: %s\n", $1/1024/1024, $2/1024/1024, $3}'
```

<br>
<br>


# 🚀 Deep Dive into Docker Containers Logging: From Basic to Advanced 🐳

<br>
<br>



## 📚 Introduction to Docker Logging

Docker containers generate logs to track their activities, errors, and runtime behavior. These logs are essential for debugging, monitoring, and ensuring the health of applications in development and production environments. Docker provides a flexible logging system that can be tailored to various use cases. Let's dive into the fundamentals and scale up to advanced techniques! 🔍

## 🤔 Why Logging Matters? 

- **Debugging**: Identify and fix issues in containerized applications.
- **Monitoring**: Track performance and system health.
- **Auditing**: Maintain records for compliance and security.
- **Troubleshooting**: Pinpoint failures in production systems.

## 🛠️ Basics of Docker Container Logs

Docker captures logs from a container’s `stdout` (standard output) and `stderr` (standard error) streams. By default, Docker uses the `json-file` logging driver to store logs.
## `stdout` vs `stderr`

| Stream   | Purpose                           |
| -------- | --------------------------------- |
| `stdout` | Standard output (normal messages) |
| `stderr` | Standard error (error messages)   |
* **`stdout`** is for successful output.
* **`stderr`** is for errors and warnings.
> [!attention] 
> In Docker logs both `stdout` vs `stderr` is combined, to see separately see below examples 

## 🔍 Docker help command

Get options using help command.

```bash
docker container logs --help
Usage:  docker container logs [OPTIONS] CONTAINER
Fetch the logs of a container
Aliases:
  docker container logs, docker logs
Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. "2013-01-02T13:23:37Z") or relative
                       (e.g. "42m" for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. "2013-01-02T13:23:37Z") or relative
                       (e.g. "42m" for 42 minutes)
```
## ✅ Syntax Examples
```bash
docker logs --follow <container_name_or_id>
docker logs --details <container_name_or_id>
docker logs --timestamps <container_name_or_id>
docker logs --tail <number> <container_name_or_id>
docker logs --tail 5 <container_name_or_id>
docker logs --tail 5 --follow <container_name_or_id>
docker logs <container_id> --since YYYY-MM-DD
docker logs <container_id> --until YYYY-MM-DD
docker logs --since <YYYY-MM-DDTHH:MM:SS> --until <YYYY-MM-DDTHH:MM:SS> <container_name_or_id>
docker logs <container_id> | grep pattern
docker logs <container_id> | grep -i error
```
## 🧪 LAB Activity

```bash
# Create containers to monitor log
docker run --name log -d busybox sh -c "while true; do echo \$(date) '==> Welcome to DevOps in Action!'; sleep 1; done"
docker run -d --name=grafana -p 3000:3000 grafana/grafana
docker run -d  --name=logger chentex/random-logger:latest 100 400

# Set container name
container_name_or_id=logger

# Check logs with all poissble way
docker ps --size
docker logs --follow $container_name_or_id
docker logs --details $container_name_or_id
docker logs --timestamps $container_name_or_id
docker logs --tail 10 $container_name_or_id
docker logs --tail 5 --follow $container_name_or_id
docker logs $container_name_or_id --since 2025-06-10
docker logs $container_name_or_id --until 2025-06-11
docker logs --since 2025-06-10 --until 2025-06-11 $container_name_or_id
docker logs $container_name_or_id | grep -i error
docker logs $container_name_or_id | grep -i failed
docker logs $container_name_or_id | grep -i -E 'error|warning|stop|fail'
docker logs --since 2025-06-10 $container_name_or_id | grep "error"

# Stdout and Stderr log export
docker run --name log-test --rm -d busybox sh -c "echo 'This is stdout'; echo 'This is stderr' >&2; sleep 100"
docker logs log-test > /tmp/stdout.log 2>/tmp/stderr.log
cat /tmp/stdout.log
cat /tmp/stderr.log

# Check log path
docker inspect --format='{{.LogPath}}' $container_name_or_id

# View logs for a container
sudo cat /var/lib/docker/containers/<CONTAINER ID>/<CONTAINER ID>-json.log

# Tail logs for a container 
sudo tail -f /var/lib/docker/containers/<CONTAINER ID>/<CONTAINER ID>-json.log

# Check size of log file of selected containers
sudo du -sh $(docker inspect --format='{{.LogPath}}' $container_name_or_id)

# Check log size of all containers at once
sudo find /var/lib/docker/containers -type f -name "*.log" -print0 | sudo du -shc --files0-from - | tail -n1

# Check log size of all containers at seprately
docker ps -aq | while read cid; do 
  name=$(docker inspect --format='{{.Name}}' "$cid" | sed 's/^\/\?//')
  log_path=$(docker inspect --format='{{.LogPath}}' "$cid")
  size=$(sudo du -sh "$log_path" 2>/dev/null | cut -f1)
  echo -e "$name\t$size"
done

# Export/Backup logs way
docker logs --since=1h $container_name_or_id > /tmp/save.txt
docker logs --since="2024-12-01T00:00:00" --until="2024-12-10T23:59:59" my-container > /tmp/exported.log
sudo cp -a $(docker inspect --format='{{.LogPath}}' $container_name_or_id) /tmp/api.log

# To delete the single container logs:
# Clear log file
sudo truncate -s 0 $(docker inspect --format='{{.LogPath}}' $container_name_or_id)
#sudo echo "" > $(sudo docker inspect --format='{{.LogPath}}' $container_name_or_id)
#: > $(sudo docker inspect --format='{{.LogPath}}' $container_name_or_id)

# Delete the log files of all docker containers in your system.
sudo find /var/lib/docker/containers -type f -name "*json.log" -exec truncate -s 0 {} \;
#sudo find /var/lib/docker/containers/ -name '*-json.log' -exec truncate -s 0 {} \;
#find /var/lib/docker/containers/ -type f -name "*.log" -delete
#sudo truncate -s 0 /var/lib/docker/containers/*/*-json.log 
```
## 🐳 Docker Logging Lab: Inspecting Container Logs with container name, image name and id in logs 📝

Explore how to configure Docker logging options and inspect container logs directly from the file system with tag in log. This hands-on lab demonstrates tagging logs with metadata and analyzing structured JSON log output.

```bash
# Set container Name in logs
docker run -itd --name=nginx -p 8090:80 --log-opt tag="{{.Name}}"  nginx
curl http://localhost:8090
docker logs nginx
CONTAINER_ID=$(docker inspect -f '{{.Id}}' nginx)
sudo cat /var/lib/docker/containers/$CONTAINER_ID/$CONTAINER_ID-json.log | jq

# Set container ImageName, Name and ID in logs
docker rm -f nginx
docker run -itd --name=nginx -p 8081:80 --log-opt tag="{{.ImageName}}/{{.Name}}/{{.ID}}"  nginx
curl http://localhost:8081
docker logs nginx
CONTAINER_ID=$(docker inspect -f '{{.Id}}' nginx)
sudo cat /var/lib/docker/containers/$CONTAINER_ID/$CONTAINER_ID-json.log | jq

# Alternative use below command
sudo cat /var/lib/docker/containers/$(docker inspect --format='{{.Id}}' nginx)/$(docker inspect --format='{{.Id}}' nginx)-json.log | jq
sudo jq . /var/lib/docker/containers/$(docker inspect -f '{{.Id}}' nginx)/$(docker inspect -f '{{.Id}}' nginx)-json.log
```
## 📝Default Logging Driver: `json-file` 

The `json-file` driver stores logs as JSON objects on the host filesystem, typically in `/var/lib/docker/containers/<container_id>/<container_id>-json.log`.
**Pros**:
- Simple to use.
- Logs are persistent on the host.
- Supports `docker logs` command.
**Cons**:
- Logs can consume significant disk space.
- Not ideal for large-scale production.
## ⚙️ Setting/Changing Logging Drivers Globally 🌍

```bash 
# Check global logging driver
docker info --format '{{json .}}' | jq
docker info --format '{{.LoggingDriver}}' 

# Supported plugins
docker info --format '{{json .Plugins.Log}}'
docker info --format '{{json .Plugins}}' | jq '{Log: .Log}'
```

Edit `/etc/docker/daemon.json` to set a default logging driver for all containers:
```json
sudo nano /etc/docker/daemon.json

# -----------------------
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
# -----------------------
## Run syslog server
docker run -d --name syslog-server \
  -p 514:514/tcp -p 514:514/udp \
  rsyslog/syslog_appliance_alpine
#### check logs
docker exec -it syslog-server cat /var/log/syslog
# -----------------------
{
  "log-driver": "syslog",
  "log-opts": {
    "syslog-address": "udp://localhost:514"
  }
}
# -----------------------
{
  "log-driver": "syslog",
  "log-opts": {
    "mode": "non-blocking"
  }
}
# -----------------------
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "25m",
    "max-file": "10",
    "labels": "production_bind",
    "env": "os,customer"
  }
}
dockerd --validate --config-file=/etc/docker/daemon.json
sudo systemctl restart docker
sudo systemctl status docker
```

- `max-size`: Limits the size of each log file.
- `max-file`: Specifies the number of rotated log files.

> [!info] 
> Ref:  [Daemon configuration file](https://docs.docker.com/reference/cli/dockerd/#daemon-configuration-file)
##  🚦 Set directly log drivers to specific containers to override global config
```bash
# Run nginx override global config 
docker run -d \
--name nginx \
--log-driver json-file \
--log-opt max-size=100m \
--log-opt max-file=3 \
nginx

# Check Container logging driver
docker inspect -f '{{.HostConfig.LogConfig.Type}}' nginx
```

✅ This keeps logs limited to 100MB in total (3 files × 100MB each).

## 🐳 Docker Logging Drivers Overview: Customizing Logging Drivers

Docker supports multiple **logging drivers** to handle container logs differently based on your needs. These drivers redirect logs to various destinations such as syslog, AWS CloudWatch, or Fluentd.
## 🔌 Supported Logging Drivers

| Driver     | Description                                                                                  |
|------------|----------------------------------------------------------------------------------------------|
| 🚫 **none**      | No logs are available for the container and `docker logs` returns no output.                |
| 📦 **local**     | Logs are stored in a custom format designed for minimal overhead.                           |
| 🗃️ **json-file** | Logs are formatted as JSON. This is the **default** logging driver for Docker.             |
| 🖧 **syslog**    | Writes log messages to the syslog facility. Requires syslog daemon running on host.        |
| 📝 **journald**  | Writes log messages to journald. Requires journald daemon running on host.                  |
| 🐊 **gelf**      | Sends logs to a Graylog Extended Log Format (GELF) endpoint like Graylog or Logstash.      |
| 🐳 **fluentd**   | Sends logs to Fluentd (forward input). Requires Fluentd daemon running on host.             |
| ☁️ **awslogs**   | Sends logs to Amazon CloudWatch Logs.                                                     |
| 🔍 **splunk**    | Sends logs to Splunk using the HTTP Event Collector.                                      |
| 🪟 **etwlogs**   | Sends logs as Event Tracing for Windows (ETW) events. Available only on Windows platforms. |
| 🌐 **gcplogs**   | Sends logs to Google Cloud Platform (GCP) Logging.                                        |

> For each driver, you can find configurable options in the [official Docker logging documentation](https://docs.docker.com/config/containers/logging/configure/).

### ⚠️ Limitations of Logging Drivers

- Reading log information may require **decompressing rotated log files**, causing:
  - Temporary increase in disk usage (until logs are read)
  - Increased CPU usage while decompressing
- The **maximum size** of log files depends on the capacity of the host storage where the Docker data directory resides.

## 🛠 Set driver at runtime:
1. **`none`**: Disables logging entirely (useful for minimal setups).
    docker run --log-driver=none --name my-app nginx 
2. **`local`**: Stores logs in a custom binary format, optimized for performance.
    docker run --log-driver=local --log-opt max-size=10m --name my-app nginx
3. **`syslog`**: Sends logs to a syslog server (useful for centralized logging).
    docker run --log-driver=syslog --log-opt syslog-address=udp://localhost:514 --name my-app nginx
4. **`journald`**: Integrates with `systemd` journal for Linux systems.
    docker run --log-driver=journald --name my-app nginx

## 📊 GUI Logging APP 1
```bash
docker run --name dozzle -d --volume=/var/run/docker.sock:/var/run/docker.sock -p 8080:8080 amir20/dozzle:latest
# Dozzle will be available at http://localhost:8080/.
# https://github.com/amir20/dozzle
```

## 📊 GUI Logging APP 2
```
docker run --restart=always --name c-docker-web-ui -d -p 9000:9000  -v /var/run/docker.sock:/var/run/docker.sock pottava/docker-webui
# Dozzle will be available at http://localhost:9000/.
# https://github.com/pottava/docker-webui
```

## 📊 GUI Logging APP 3
```
docker run -d \
  -p 9000:9000 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  portainer/portainer-ce:latest
```
## 👀 How to Properly Log (Best Practices)
- Log to the Standard Output Stream
- Limit the log size
- Rotate Logs

## 🎯 Summary

| Concept             | Dev Environment | Production Guidance           |
| ------------------- | --------------- | ----------------------------- |
| Default Driver      | `json-file`     | Use `fluentd`, `gelf`, etc.   |
| Log Rotation        | Optional        | Mandatory                     |
| Centralized Logging | Optional        | Highly Recommended            |
| Log Structure       | Text logs       | Structured JSON logs          |
| Log Forwarding      | Not needed      | Critical for monitoring/alert |


---
## 🐳 Docker Logging Resources

- 🏷️ [Log Tags Overview](https://docs.docker.com/engine/logging/log_tags/)  
  Learn about available log tags and how to use them with logging drivers.
- ⚙️ [Daemon Configuration File](https://docs.docker.com/reference/cli/dockerd/#daemon-configuration-file)  
  Configure the Docker daemon using a JSON file, including logging settings.
- 📄 [Container Logs Command](https://docs.docker.com/reference/cli/docker/container/logs/)  
  Usage guide for the `docker container logs` CLI command.
- 🛠️ [Configure Logging Drivers](https://docs.docker.com/engine/logging/configure/)  
  Instructions on how to configure different logging drivers in Docker.
- 🔖 [Log Tags in Container Logging](https://docs.docker.com/config/containers/logging/log_tags/)  
  Additional info on log tags for container logging configuration.



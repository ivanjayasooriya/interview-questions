# Docker Interview Questions & Answers

A comprehensive guide covering Docker fundamentals through advanced concepts, images, containers, networking, volumes, Docker Compose, orchestration basics, security, and production best practices.

---

## Table of Contents

1. [Docker & Containerization Fundamentals](#1-docker--containerization-fundamentals)
2. [Docker Architecture](#2-docker-architecture)
3. [Images](#3-images)
4. [Dockerfile](#4-dockerfile)
5. [Containers](#5-containers)
6. [Volumes & Data Persistence](#6-volumes--data-persistence)
7. [Networking](#7-networking)
8. [Docker Compose](#8-docker-compose)
9. [Multi-Stage Builds & Image Optimization](#9-multi-stage-builds--image-optimization)
10. [Registries & Image Distribution](#10-registries--image-distribution)
11. [Security](#11-security)
12. [Orchestration: Docker Swarm & Kubernetes Basics](#12-orchestration-docker-swarm--kubernetes-basics)
13. [Logging, Monitoring & Debugging](#13-logging-monitoring--debugging)
14. [Performance & Resource Management](#14-performance--resource-management)
15. [CI/CD with Docker](#15-cicd-with-docker)
16. [Miscellaneous & Best Practices](#16-miscellaneous--best-practices)

---

## 1. Docker & Containerization Fundamentals

### Q1. What is Docker?
Docker is an open-source platform for developing, shipping, and running applications inside **containers** — lightweight, isolated, portable units that package an application together with all its dependencies (libraries, runtime, configuration), ensuring it runs consistently across different environments (a developer's laptop, a test server, production).

### Q2. What is a container?
A container is a lightweight, standalone, executable unit of software that packages an application's code along with everything it needs to run (system tools, libraries, settings). Containers run as **isolated processes** on the host machine's operating system kernel, sharing that kernel rather than requiring their own full OS — which is what makes them significantly lighter weight and faster to start than virtual machines.

### Q3. What problem does containerization solve, i.e., what is "dependency hell" / "works on my machine"?
Before containerization, applications often ran differently across environments due to differing installed library versions, OS configurations, or missing dependencies between a developer's machine, staging, and production — the classic "works on my machine" problem. Containers solve this by bundling the exact runtime environment an application needs directly into a portable image, guaranteeing the same environment runs identically anywhere Docker (or an OCI-compatible runtime) is installed.

### Q4. What is the difference between a container and a virtual machine (VM)?
| | Container | Virtual Machine |
|---|---|---|
| Isolation level | Process-level, shares the host OS kernel | Full OS-level, includes its own kernel |
| Size | Lightweight (MBs) | Heavy (GBs, includes a full guest OS) |
| Startup time | Seconds or less | Minutes (booting a full OS) |
| Resource overhead | Low — no duplicated OS/kernel | Higher — each VM runs a complete OS instance |
| Isolation strength | Weaker (shared kernel — a kernel-level exploit could theoretically affect the host/other containers) | Stronger (hardware-level virtualization, more isolated) |

Containers achieve isolation using OS-level features (namespaces, cgroups — see Q9), while VMs use a **hypervisor** to virtualize entire hardware/OS instances.

### Q5. What is the difference between Docker and a hypervisor-based virtualization solution (VMware, VirtualBox)?
A hypervisor virtualizes hardware, allowing multiple independent guest operating systems (each with their own full kernel) to run on a single physical machine. Docker doesn't virtualize hardware at all — it uses the **host OS's own kernel** and isolates processes via kernel namespaces/cgroups, making containers fundamentally lighter and faster than full VMs, though with a comparatively narrower isolation boundary (see Q4).

### Q6. What is the difference between an image and a container?
An **image** is a read-only, immutable template/blueprint containing an application's filesystem and metadata (layers, configuration, entry point) — analogous to a class in object-oriented programming, or a snapshot. A **container** is a running (or stopped) **instance** of an image, with its own writable layer on top and its own isolated process/network/filesystem state — analogous to an object instantiated from that class. Multiple containers can be created from the same single image.

### Q7. What is OCI (Open Container Initiative), and why does it matter?
The OCI is an open governance structure that defines industry-standard specifications for container **image formats** and **container runtimes**, ensuring interoperability between different tools (Docker, containerd, Podman, CRI-O) — meaning an image built with Docker conforms to a standard that other OCI-compliant tools can also run, avoiding vendor lock-in to Docker specifically as the only way to build/run containers.

### Q8. What are the main use cases/benefits of Docker in a development and production workflow?
- **Consistency** across development, testing, and production environments.
- **Isolation** — each application/service runs independently, without conflicting dependencies.
- **Portability** — images run identically on any machine with a compatible container runtime.
- **Efficient resource usage** — many containers can run on a single host with far less overhead than equivalent VMs.
- **Fast startup/teardown** — enabling rapid scaling, testing, and CI/CD pipelines.
- **Microservices enablement** — natural fit for packaging and independently deploying/scaling individual services.

---

## 2. Docker Architecture

### Q9. What underlying Linux kernel features does Docker rely on for isolation?
- **Namespaces** — provide isolated views of system resources per container: PID namespace (process IDs), network namespace (network interfaces/IPs), mount namespace (filesystem mounts), UTS namespace (hostname), IPC namespace (inter-process communication), and user namespace (user/group IDs) — collectively making a container "believe" it has its own isolated system view.
- **Control groups (cgroups)** — limit and account for a container's resource usage (CPU, memory, disk I/O, network bandwidth), preventing one container from starving others of host resources.
- **Union/layered filesystems** (e.g., OverlayFS) — enable Docker's efficient, layered image system (Q22).

### Q10. What is the Docker Engine, and what are its main components?
The Docker Engine is the core client-server application that builds and runs containers. Its main components:
- **`dockerd`** (the Docker daemon) — a background process managing images, containers, networks, and volumes.
- **Docker CLI (`docker`)** — the command-line client that communicates with the daemon (via a REST API) to issue commands.
- **REST API** — the interface `dockerd` exposes, which the CLI (or other tools) uses to communicate with it.

### Q11. What is `containerd`, and how does it relate to Docker?
`containerd` is a lightweight, industry-standard **container runtime** (an OCI-compliant, CNCF graduated project) that handles the low-level tasks of actually running containers — managing container lifecycle, image transfer/storage, and execution. Docker Engine uses `containerd` internally as its runtime layer (Docker's daemon delegates the actual container execution work to `containerd`, which in turn uses a lower-level runtime like `runc` to create the containers themselves) — `containerd` can also be used independently of Docker (e.g., it's the runtime Kubernetes commonly uses directly, without needing the rest of the Docker Engine stack).

### Q12. What is `runc`?
`runc` is a low-level, OCI-compliant **command-line tool/library** that actually creates and runs containers according to the OCI runtime specification — it's the component that directly interfaces with the Linux kernel's namespaces/cgroups to instantiate a container process. `containerd` invokes `runc` (or an alternative OCI runtime) to perform this lowest-level container creation step.

### Q13. What is the client-server architecture of Docker, and how does the Docker CLI communicate with the daemon?
The Docker CLI (client) sends commands to the Docker daemon (`dockerd`, the server) via a REST API — typically over a local Unix socket by default, but configurable to communicate over a network (TCP) as well, allowing a client to control a remote Docker daemon (relevant for tools like Docker Machine or remote build setups).

### Q14. What is Docker Desktop, and how does it differ from the Docker Engine itself?
Docker Desktop is an application (for macOS, Windows, and Linux) that bundles the Docker Engine, CLI, Docker Compose, and a GUI, along with the necessary virtualization layer (since Docker's Linux-kernel-based isolation requires an actual Linux kernel — Docker Desktop runs a lightweight Linux VM under the hood on macOS/Windows to provide this) — making it possible to run Docker on non-Linux operating systems, whereas the Docker Engine alone runs natively only on Linux.

---

## 3. Images

### Q15. What is a Docker image, in more technical terms?
An image is a read-only template composed of a stack of **layers**, each representing a filesystem change (from a `Dockerfile` instruction), plus metadata (default command, exposed ports, environment variables, etc.) — layers are combined into a single unified view via a union filesystem (typically OverlayFS) when a container is created from the image.

### Q16. What are image layers, and why does Docker use a layered structure?
Each instruction in a `Dockerfile` (`RUN`, `COPY`, `ADD`, etc.) typically creates a new, immutable **layer** on top of the previous one, capturing only the filesystem changes made by that instruction. This layered approach enables **caching** (unchanged layers can be reused across builds, speeding up rebuilds — see Q35) and **sharing** (multiple images built from a common base share the same underlying layers on disk, saving storage space, since Docker only stores each unique layer once).

### Q17. What is an image tag, and what does the `latest` tag actually mean?
A tag is a human-readable label (typically a version identifier) attached to a specific image, in the format `repository:tag` (e.g., `node:20-alpine`). `latest` is simply a **conventional default tag name** — not a special, automatically-updating pointer to the newest build unless a maintainer explicitly pushes an image tagged `latest` themselves. Relying on `latest` in production is generally discouraged, since it provides no guarantee about which actual image version is running or reproducibility across pulls.

### Q18. What is the difference between `docker pull`, `docker push`, and `docker build`?
- **`docker build`** — creates a new image from a `Dockerfile` and build context.
- **`docker pull`** — downloads an image (or a specific tag) from a registry to the local machine.
- **`docker push`** — uploads a locally built/tagged image to a registry, making it available for others (or other environments) to pull.

### Q19. What is the difference between `docker images`/`docker image ls` output fields — Repository, Tag, Image ID, and Size?
`docker images` lists locally stored images, showing the repository name, tag, a unique image ID (a content-addressable hash — technically derived from the image's configuration), creation date, and the image's total size (the combined size of all its layers).

### Q20. What is an image digest, and how does it differ from a tag?
A digest is a **cryptographic hash (SHA256)** of an image's content, providing an immutable, content-addressable identifier — unlike a tag (which is a mutable, human-assigned label that can be reassigned to point to a different image later), a digest always refers to the **exact same** image content. Pulling/referencing an image by digest (`image@sha256:...`) guarantees you get precisely the intended image, useful for strict reproducibility.

### Q21. How do you remove unused images, and what is `docker image prune`?
```bash
docker rmi <image>              # remove a specific image
docker image prune              # remove dangling (untagged) images
docker image prune -a           # remove all images not used by any existing container
```
Regularly pruning unused images/layers helps reclaim disk space, particularly relevant in CI environments or long-running development machines that accumulate many intermediate/unused images over time.

### Q22. What is a union/overlay filesystem, and how does it enable Docker's layered image model?
A union filesystem (Docker's default on Linux is **OverlayFS**) allows multiple separate filesystem layers to be transparently merged into a single, unified view. Docker uses this to stack an image's read-only layers together with a container's own thin, writable layer (Q30) on top, presenting the combination as if it were one coherent filesystem to the running container, while physically keeping the underlying layers separate and shareable on disk.

### Q23. What is a "base image," and what is a "scratch" image?
A **base image** is the starting point layer(s) a `Dockerfile` builds upon (declared via the `FROM` instruction) — e.g., `ubuntu`, `node`, `alpine`. **`scratch`** is a special, entirely **empty** base image (`FROM scratch`), used to build minimal images containing literally nothing but the application binary itself (typically a statically-compiled binary, e.g., from Go or Rust) — producing the smallest possible image size, at the cost of having no shell, package manager, or standard OS utilities available inside the container.

---

## 4. Dockerfile

### Q24. What is a Dockerfile?
A text file containing a sequence of instructions that Docker uses to automatically build an image — specifying the base image, files to copy in, commands to run, environment variables, exposed ports, and the default command/entry point for containers created from it.

### Q25. What are the most commonly used Dockerfile instructions, and what does each do?
- **`FROM`** — specifies the base image to build from (must be the first instruction, aside from optional `ARG` before it).
- **`WORKDIR`** — sets the working directory for subsequent instructions (and the container's default directory at runtime).
- **`COPY`** — copies files/directories from the build context into the image.
- **`ADD`** — similar to `COPY`, but with extra capabilities (can fetch remote URLs, auto-extracts local tar archives) — generally `COPY` is preferred unless `ADD`'s specific extra behavior is genuinely needed, since `ADD`'s "magic" behavior can be a source of surprises.
- **`RUN`** — executes a command **during the image build**, creating a new layer with the resulting filesystem changes (e.g., installing packages).
- **`CMD`** — specifies the **default command** to run when a container starts (can be overridden at `docker run` time).
- **`ENTRYPOINT`** — specifies the main executable for the container, less easily overridden than `CMD` (see Q28).
- **`EXPOSE`** — documents which port(s) the container listens on (purely informational/metadata — does not actually publish the port; see Q26).
- **`ENV`** — sets environment variables available both during the build and at container runtime.
- **`ARG`** — defines a build-time-only variable, passed via `--build-arg`, not available inside the running container unless also explicitly passed to `ENV`.
- **`VOLUME`** — declares a mount point intended for persistent/external storage (see Section 6).
- **`USER`** — sets the user (and optionally group) that subsequent instructions, and the container process itself, run as.

### Q26. What is the difference between `EXPOSE` in a Dockerfile and actually publishing a port with `docker run -p`?
`EXPOSE` is purely **documentation/metadata** embedded in the image, indicating which port(s) the containerized application is designed to listen on — it does **not** actually make the port accessible from outside the container. Actually publishing/mapping a port to the host (making it reachable from outside Docker's internal network) requires the `-p hostPort:containerPort` flag at `docker run` time (or the equivalent `ports:` configuration in Docker Compose).

### Q27. What is the difference between the "shell form" and "exec form" of `RUN`/`CMD`/`ENTRYPOINT`?
```dockerfile
RUN apt-get update && apt-get install -y curl   # shell form — runs via /bin/sh -c "..."
CMD ["node", "server.js"]                        # exec form — runs the command directly, no shell
```
**Shell form** runs the command through a shell (`/bin/sh -c`), which allows shell features (variable expansion, chaining with `&&`, pipes) but means the process runs as a child of the shell (which can interfere with proper signal handling — see Q28) and the shell itself becomes PID 1 rather than your actual application. **Exec form** runs the specified executable directly, without an intermediate shell, making the application itself PID 1 and ensuring it receives OS signals (like `SIGTERM`) directly and correctly — generally the recommended form for `CMD`/`ENTRYPOINT`.

### Q28. What is the difference between `CMD` and `ENTRYPOINT`, and how are they typically used together?
`CMD` specifies default arguments/command that can be **entirely overridden** by arguments passed to `docker run`. `ENTRYPOINT` specifies the container's **fixed main executable**, which is much harder to override (requires the explicit `--entrypoint` flag rather than just appending arguments to `docker run`). A common pattern combines both: `ENTRYPOINT` defines the fixed executable, while `CMD` supplies **default arguments** to that executable, which the user can still easily override by passing different arguments to `docker run` without needing to override the entrypoint itself.
```dockerfile
ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8080"]  # default args; docker run image --port 9000 overrides just this part
```

### Q29. What is Docker's build cache, and how do Dockerfile instruction ordering choices affect it?
Docker caches each layer produced by a `Dockerfile` instruction, and on subsequent builds, reuses a cached layer instead of re-executing that instruction — **as long as** the instruction and its inputs (including files copied via `COPY`/`ADD`, or the preceding layer's cache state) haven't changed. Because a cache miss on any layer invalidates the cache for **all subsequent** layers, it's best practice to order instructions from **least frequently changing** (e.g., installing dependencies from a lockfile) to **most frequently changing** (e.g., copying application source code) — so that routine source code changes don't force an unnecessary, slow re-installation of unchanged dependencies on every build.
```dockerfile
COPY package*.json ./
RUN npm install          # cached unless package*.json changes
COPY . .                 # source changes don't invalidate the npm install layer above
```

---

## 5. Containers

### Q30. What is a container's writable layer, and how does it relate to the underlying image?
When a container is created from an image, Docker adds a thin, **writable layer** on top of the image's read-only layers — any filesystem changes made while the container runs (new files, modifications, deletions) are captured in this writable layer, leaving the underlying image's layers completely untouched and shareable with other containers based on the same image. This writable layer is deleted when the container is removed, which is why data meant to persist beyond a container's lifecycle needs a volume or bind mount (Section 6) instead.

### Q31. What is the difference between `docker run`, `docker start`, and `docker exec`?
- **`docker run`** — creates a **new** container from an image and starts it (combining `docker create` + `docker start` conceptually).
- **`docker start`** — starts an **existing, stopped** container (previously created, not a new one).
- **`docker exec`** — runs an **additional command inside an already-running** container (commonly used to open an interactive shell into a running container for debugging: `docker exec -it <container> /bin/sh`).

### Q32. What is the difference between running a container in detached mode (`-d`) vs. attached/foreground mode?
By default (foreground/attached mode), `docker run` attaches your terminal to the container's standard input/output, blocking until the container exits. `-d` (detached mode) starts the container in the background, immediately returning control of the terminal, with the container continuing to run independently — the typical mode for long-running services.

### Q33. What is the difference between stopping, killing, pausing, and removing a container?
- **`docker stop`** — sends `SIGTERM` (allowing graceful shutdown), then `SIGKILL` after a grace period if the process hasn't exited.
- **`docker kill`** — sends `SIGKILL` (or another specified signal) immediately, without a graceful shutdown period.
- **`docker pause`** — freezes all processes within the container (via cgroups) without stopping/killing them, resumable via `docker unpause`.
- **`docker rm`** — permanently **removes** a (stopped) container and its writable layer; requires the container to be stopped first (or use `-f` to force-stop and remove in one step).

### Q34. What happens to a container's data when it's removed (`docker rm`)?
Any data written to the container's writable layer (i.e., anything not stored in a volume or bind-mounted from the host) is **permanently lost** when the container is removed — this is a key reason persistent application data (databases, uploaded files) should always be stored in a volume rather than the container's own writable layer.

### Q35. What is the container lifecycle, from creation to removal?
**Created** (container exists but hasn't started) → **Running** (actively executing) → **Paused** (optionally, frozen) → **Stopped/Exited** (process has terminated, but the container and its writable layer still exist, can be restarted) → **Removed** (container and its writable layer are permanently deleted).

### Q36. How do you view logs from a running (or stopped) container?
```bash
docker logs <container>
docker logs -f <container>   # follow/stream logs in real time, like `tail -f`
```
By default, this shows output captured from the container's `STDOUT`/`STDERR` streams (assuming the default `json-file` or similar logging driver is used — see Section 13).

### Q37. What is a container's restart policy, and what options are available?
Configures whether/how Docker automatically restarts a container if it stops. Options: `no` (default — never restart automatically), `on-failure[:max-retries]` (restart only if it exits with a non-zero status, optionally capped at a maximum retry count), `always` (always restart, regardless of exit status, including after a Docker daemon restart), and `unless-stopped` (like `always`, but won't restart if the container was manually stopped by the user before a daemon restart).
```bash
docker run --restart unless-stopped my-service
```

### Q38. What are `docker inspect` and `docker stats` used for?
`docker inspect <container/image>` returns detailed, low-level JSON metadata about a container or image (network settings, mounts, environment variables, configuration) — useful for debugging configuration issues. `docker stats` provides a live, streaming view of resource usage (CPU, memory, network I/O) for running containers — useful for spotting resource bottlenecks/leaks.

---

## 6. Volumes & Data Persistence

### Q39. Why is data persistence a special concern for containers?
Because a container's writable layer is ephemeral — deleted along with the container itself — any data an application needs to survive container restarts, removal, or being replaced by a newer version (a very common event, especially during deployments/updates) must be stored **outside** that ephemeral writable layer, in a mechanism designed to persist independently of any single container's lifecycle.

### Q40. What are the three main types of data storage/mounting options in Docker?
1. **Volumes** — storage managed entirely by Docker itself, stored in a dedicated area of the host filesystem (`/var/lib/docker/volumes/` on Linux by default), independent of any specific container.
2. **Bind mounts** — map a specific file/directory on the **host machine** directly into the container at a specified path, giving the host filesystem's structure direct visibility inside the container.
3. **tmpfs mounts** — store data only in the host machine's **memory** (RAM), never persisted to disk at all, and lost when the container stops — useful for sensitive temporary data or performance-sensitive scratch space.

### Q41. What is the difference between a volume and a bind mount, and when should you use each?
**Volumes** are fully managed by Docker (created, named, and located by Docker itself), portable across different host environments, and the generally **recommended** approach for persisting application data (databases, uploaded content) since they're decoupled from the host's specific filesystem structure. **Bind mounts** directly reference an existing path on the host, giving more direct control and visibility (e.g., mounting your local source code directory into a container for live development), but tightly coupling the container to that specific host machine's filesystem layout — commonly used in **development** environments (for live code reloading) rather than production data persistence.
```bash
docker run -v my-named-volume:/app/data my-image        # volume
docker run -v $(pwd)/src:/app/src my-image                # bind mount
```

### Q42. How do you create and manage Docker volumes explicitly?
```bash
docker volume create my-volume
docker volume ls
docker volume inspect my-volume
docker volume rm my-volume
docker volume prune   # remove all unused (unreferenced by any container) volumes
```
(Volumes can also be created implicitly, simply by referencing a name that doesn't yet exist in a `-v`/`--mount` flag.)

### Q43. What is the difference between the `-v` (short) syntax and the `--mount` (long/verbose) syntax for specifying mounts?
Both achieve the same underlying result, but `--mount` uses explicit, comma-separated key-value pairs (`--mount type=volume,source=my-vol,target=/app/data`), which is more verbose but less ambiguous/error-prone than `-v`'s more compact, positional colon-separated syntax (`-v my-vol:/app/data`) — `--mount` is generally recommended for clarity, particularly for more advanced configurations, while `-v` remains common for quick/simple use due to its brevity.

### Q44. What is a named volume vs. an anonymous volume?
A **named volume** is explicitly given a specific name (either via `docker volume create` or by specifying a name in the `-v`/`--mount` syntax), making it easy to reference and reuse across container runs. An **anonymous volume** is created without a name (Docker generates a random hash identifier) when a container specifies a volume mount without a named source — anonymous volumes are harder to intentionally reuse/reference later and are more likely to accumulate as orphaned, forgotten volumes over time if not tracked carefully.

### Q45. How do you share data between multiple containers?
By mounting the **same named volume** into multiple containers simultaneously — each container can read/write to the shared volume's contents, useful for scenarios like a sidecar container that processes files written by a main application container, or sharing configuration/data between related services.

### Q46. Why shouldn't application data (like a database's actual data files) typically be stored inside the container's own image/writable layer?
Beyond simply being lost on container removal (Q34), storing large or frequently-changing data directly in a container's writable layer also tends to be **less performant** than volumes for write-heavy workloads on some storage drivers, and conflates ephemeral application code with persistent state, making backups, migrations, and container replacement (a routine, expected event in container-based deployments) needlessly risky and complicated.

---

## 7. Networking

### Q47. What are the built-in Docker network drivers, and what does each provide?
- **`bridge`** (default) — creates a private, isolated internal network on the host; containers on the same bridge network can communicate with each other, and can reach the outside world via NAT, but aren't directly reachable from outside without explicit port publishing.
- **`host`** — removes network isolation entirely, sharing the host machine's network namespace directly with the container (no port mapping needed/possible — the container uses the host's network stack directly); improves network performance slightly, at the cost of isolation.
- **`none`** — completely disables networking for the container (no network interfaces at all beyond loopback).
- **`overlay`** — enables communication between containers running on **different Docker hosts** (used in Docker Swarm/multi-host setups) via a virtual overlay network spanning the hosts.
- **`macvlan`** — assigns a container its own MAC address, making it appear as a physical device directly on the host's physical network, useful for legacy applications expecting direct network-level presence.

### Q42. What is the default `bridge` network, and why is it generally recommended to create a custom bridge network instead?
Docker automatically creates a default `bridge` network that all containers join unless configured otherwise. However, containers on the **default** bridge network can only communicate with each other via IP address (not by container name — no built-in DNS resolution), whereas a **user-defined/custom** bridge network provides automatic **DNS-based service discovery** — containers can reach each other simply by container/service name — plus better isolation (only containers explicitly attached to that custom network can communicate), making custom bridge networks the generally recommended default for multi-container applications.

### Q49. How do containers on the same custom bridge network discover/communicate with each other?
Docker's embedded DNS server automatically resolves each container's **name** (or, in Compose, its service name) to its internal IP address on that network — allowing application code to simply connect to `http://other-service-name:port` rather than needing to know or hardcode specific container IP addresses (which can change across restarts).

### Q50. What does the `-p` (publish) flag do, and what is the difference between `-p 8080:80` and `-P` (capital)?
`-p hostPort:containerPort` explicitly maps a specific host port to a specific container port, making the containerized service accessible from outside Docker's internal network via that host port. `-P` (capital, "publish all") automatically publishes **all** ports the image declares via `EXPOSE`, each mapped to a **randomly chosen** available host port — less predictable/controllable than explicit `-p` mappings, generally more useful for quick testing than production use.

### Q51. What is the difference between a container's internal port and the host-published port?
The container's internal port is whatever port the application inside the container actually listens on (fixed, defined by the application itself). The host-published port is the port on the **host machine** that Docker forwards incoming traffic from, to that internal container port — these can be different numbers (`-p 8080:3000` exposes a container's internal port 3000 externally as host port 8080), giving flexibility to run multiple containers whose internal ports might collide, each mapped to distinct host ports.

### Q52. How do you inspect a Docker network, and how do you connect/disconnect a running container to/from a network?
```bash
docker network ls
docker network inspect my-network
docker network connect my-network my-container
docker network disconnect my-network my-container
```
A running container can be connected to multiple networks simultaneously, and networks can be joined/left dynamically without needing to recreate the container.

### Q53. What is service discovery, and how does it typically work in a multi-container Docker application?
Service discovery is the mechanism by which one service/container locates and connects to another dynamically (rather than relying on hardcoded, static IP addresses that could change). In Docker, this is primarily achieved via the embedded DNS resolution on custom bridge/overlay networks (Q49), where a service can simply be addressed by its container/service name — Docker Compose and Swarm both build directly on this underlying mechanism.

---

## 8. Docker Compose

### Q54. What is Docker Compose, and what problem does it solve?
Docker Compose is a tool for defining and running **multi-container** Docker applications using a single, declarative YAML configuration file (`compose.yaml`/`docker-compose.yml`) — instead of manually running multiple `docker run` commands with matching flags for networking, volumes, and dependencies, Compose lets you define the entire application stack (services, networks, volumes) in one file and bring it all up/down with a single command.

### Q55. What is the basic structure of a `docker-compose.yml` file?
```yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
  db:
    image: postgres:16
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secret

volumes:
  db-data:
```
The top-level `services` key defines each container/application component, `volumes` and `networks` (not shown here) define shared resources, each usable across services.

### Q56. What are the most commonly used Docker Compose commands?
```bash
docker compose up            # create and start all services (add -d for detached mode)
docker compose down          # stop and remove containers, networks (add -v to also remove volumes)
docker compose build         # build/rebuild images defined with a `build` key
docker compose logs -f       # stream logs from all (or a specific) service
docker compose ps            # list running services/containers in this project
docker compose exec web sh   # run a command inside a running service's container
```
(Note: modern Docker ships Compose as a plugin — `docker compose` (with a space) — superseding the older, separately-installed `docker-compose` binary/hyphenated command, though both remain broadly compatible in usage.)

### Q57. What does `depends_on` control in Docker Compose, and what are its limitations?
`depends_on` controls the **startup order** of services — ensuring a dependent service's container is started only after the services it depends on have started. However, by default, it only waits for the dependency's container to **start**, not for the actual application/service inside it to be genuinely **ready** to accept connections (e.g., a database container might be "started" but still initializing before it can accept queries) — for genuine readiness-waiting, `depends_on` needs to be combined with a `condition: service_healthy` clause referencing a defined `healthcheck`, or the application itself should implement its own connection retry logic.

### Q58. How do you define and use a health check in Docker Compose (or a Dockerfile)?
```yaml
services:
  db:
    image: postgres:16
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
  web:
    depends_on:
      db:
        condition: service_healthy
```
A health check periodically runs a specified command inside the container to determine whether the application is genuinely healthy/ready — Docker tracks and exposes this status (`healthy`/`unhealthy`/`starting`), and other services can wait on it via `depends_on`'s `condition` option, achieving true readiness-based startup ordering.

### Q59. How do environment variables work in Docker Compose, and how does an `.env` file factor in?
Environment variables can be set directly under a service's `environment` key, referenced from the host shell's environment, or loaded from a `.env` file placed alongside the `compose.yml` (automatically picked up by Compose to substitute `${VARIABLE}` placeholders within the compose file itself, and optionally passed through to containers) — a common pattern for keeping environment-specific configuration (and secrets, for local development) out of the Compose file itself and out of source control.

### Q60. What is the difference between `build` and `image` in a Compose service definition?
`image` specifies a pre-built image to pull/use directly (from a registry). `build` (specifying a Dockerfile/context path) tells Compose to **build** an image locally from source before running it — a single service can specify both (build a new image and simultaneously tag it with a specific name via `image`), a common pattern in development.

### Q61. How does Docker Compose handle networking between services by default?
Compose automatically creates a dedicated custom bridge network for each project (all services defined in the same Compose file join this shared network by default), and each service is reachable by other services using its **service name** as a hostname — no manual network configuration needed for typical multi-service communication within the same Compose project.

### Q62. What is the difference between using Docker Compose for local development vs. production deployment?
While Compose is fully capable of running a production stack on a **single host**, it doesn't provide multi-host orchestration, automated failover, rolling updates across a cluster, or the broader operational tooling that a dedicated orchestrator (Docker Swarm, Kubernetes) provides — Compose is most commonly used for local development environments and simpler, single-host production deployments, with orchestrators taking over for larger-scale, highly-available, multi-node production needs.

---

## 9. Multi-Stage Builds & Image Optimization

### Q63. What is a multi-stage build, and what problem does it solve?
A `Dockerfile` technique using **multiple `FROM` instructions**, each starting a new build "stage," where later stages can selectively copy specific artifacts (like a compiled binary or built frontend assets) from earlier stages via `COPY --from=<stage>` — without carrying forward the earlier stage's full toolchain (compilers, build dependencies, source code) into the final image. This solves the problem of needing heavyweight build tools (a full compiler toolchain, `node_modules` used only for building) during the build process, while keeping the final production image lean and containing only what's actually needed at runtime.
```dockerfile
# Build stage
FROM node:20 AS build
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Production stage
FROM node:20-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY package*.json ./
RUN npm install --omit=dev
CMD ["node", "dist/server.js"]
```

### Q64. What are the main benefits of multi-stage builds?
- **Smaller final images** — build-only dependencies (compilers, dev dependencies, intermediate build artifacts) never appear in the final image.
- **Improved security** — a smaller final image has a smaller attack surface (fewer installed packages/tools that could harbor vulnerabilities).
- **Simpler CI/CD** — a single `Dockerfile` can handle both building and packaging the application, without needing a separate external build step/pipeline stage just to produce build artifacts before a second, separate `docker build`.

### Q65. What are common techniques for reducing Docker image size?
- Using a **minimal base image** (`alpine`, `slim` variants, or `distroless` images) instead of a full general-purpose OS image.
- Using **multi-stage builds** to exclude build-time-only dependencies from the final image.
- Combining related `RUN` commands into a single layer where sensible (e.g., `apt-get update && apt-get install -y ... && rm -rf /var/lib/apt/lists/*` in one `RUN` instruction) to avoid leaving cached package manager metadata/intermediate state in a separate layer.
- Using a `.dockerignore` file (Q66) to avoid copying unnecessary files into the build context/image.
- Removing unnecessary files (cache directories, documentation, temporary build artifacts) within the same layer they were created, since deleting a file in a **later** layer doesn't reduce the image's total size — the file still physically exists in the earlier layer.

### Q66. What is a `.dockerignore` file, and why is it important?
Similar in concept to `.gitignore`, it specifies files/directories that should be **excluded** from the build context sent to the Docker daemon when building an image (e.g., `node_modules`, `.git`, local `.env` files, build artifacts) — improving build performance (less data to transfer/process) and preventing accidental inclusion of unnecessary, large, or sensitive files into the resulting image.

### Q67. What are "distroless" images, and what are their trade-offs?
Minimal images (popularized by Google's `distroless` project) that contain **only** an application and its direct runtime dependencies — deliberately excluding a package manager, shell, or other typical OS utilities entirely. This significantly reduces image size and attack surface, but makes interactive debugging (`docker exec ... sh`) impossible directly within the container itself, since there's no shell available — a trade-off some teams accept for production images while using more full-featured images during development/debugging.

### Q68. Why does combining multiple `RUN apt-get install` commands into a single `RUN` instruction (with `&&`) matter for image size, given layer caching?
Because each separate `RUN` instruction creates its own distinct layer, and once a layer is created, files added or removed within a **later**, separate layer don't shrink the image — the content still exists in the earlier layer underneath. Combining install-and-cleanup steps into a **single** `RUN` instruction ensures the cleanup (e.g., removing package manager cache files) happens within the same layer as the installation, actually reducing that layer's (and thus the image's) final size, rather than just appearing to clean up while the earlier layer's bloat remains present underneath.

---

## 10. Registries & Image Distribution

### Q69. What is a container registry?
A storage and distribution service for container images, allowing images to be pushed (uploaded) and pulled (downloaded) by name and tag — the standard way images move between where they're built (a developer machine or CI pipeline) and where they're actually run (staging/production servers, orchestrators).

### Q70. What is Docker Hub, and what are alternative registries?
**Docker Hub** is Docker's own default, publicly-accessible registry, hosting a huge number of official and community-published images (base images like `node`, `postgres`, `nginx` are typically pulled from here by default). Alternatives/complements include cloud-provider-specific registries (Amazon ECR, Google Artifact Registry, Azure Container Registry), self-hosted options (Harbor, a private Docker Registry instance), and GitHub Container Registry (`ghcr.io`) — organizations often use a private registry for proprietary application images while still pulling common public base images from Docker Hub.

### Q71. How do you tag and push a custom-built image to a registry?
```bash
docker build -t my-app:1.0 .
docker tag my-app:1.0 myregistry.example.com/my-app:1.0
docker push myregistry.example.com/my-app:1.0
```
The image's full reference name (including the registry hostname, unless it's Docker Hub, which is the implicit default) determines where `docker push`/`docker pull` will send/retrieve it.

### Q72. What is Docker Hub rate limiting, and why does it matter for CI/CD pipelines?
Docker Hub imposes **pull rate limits** on anonymous and free-tier authenticated accounts (a certain number of pulls per IP/account within a rolling time window) — high-volume CI/CD pipelines that frequently pull base images can hit these limits, causing build failures. Mitigations: authenticating pulls (raising the limit compared to anonymous pulls), using a **pull-through cache/mirror**, or using an alternative registry/cloud-provider-hosted mirror for common base images.

### Q73. What is image signing/verification (e.g., Docker Content Trust), and why does it matter for supply chain security?
Docker Content Trust (built on **Notary**/TUF — The Update Framework) allows publishers to **cryptographically sign** images, and consumers to verify that a pulled image genuinely comes from the expected publisher and hasn't been tampered with — an important supply-chain security measure, since blindly pulling and running arbitrary, unverified images from a registry carries meaningful risk (a compromised or malicious image could execute arbitrary code with the container's privileges).

---

## 11. Security

### Q74. What are the main security considerations specific to running containers?
- **Shared kernel** — a serious container-escape vulnerability could theoretically allow a compromised container to affect the host or other containers, since containers share the host's kernel (unlike VMs' stronger hardware-level isolation).
- **Running as root by default** — unless explicitly configured otherwise, container processes run as root by default, which is a broader privilege than necessary for most applications.
- **Untrusted/vulnerable images** — pulling and running images from unverified sources, or images with unpatched known vulnerabilities in their dependencies.
- **Excessive privileges/capabilities** — granting a container more Linux capabilities or host access than it actually needs for its function.
- **Secrets management** — accidentally baking credentials/secrets directly into an image layer (even if later "deleted" in a subsequent layer — Q68's caching/layering issue applies to secret leakage too, not just size).

### Q75. Why is it best practice to avoid running container processes as root, and how do you configure a non-root user?
Running as root **inside** a container, while not automatically equivalent to root on the host (due to namespace isolation), still increases the potential impact of a container-escape vulnerability or a compromised application process — following the principle of least privilege, applications should run as an unprivileged user whenever possible.
```dockerfile
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

### Q76. What is the `--privileged` flag, and why should it be avoided except when genuinely necessary?
`--privileged` grants a container **almost all** of the host's Linux capabilities and disables most of the standard container isolation restrictions, effectively giving the container near-complete access to the host system (device access, kernel module loading, and more) — occasionally necessary for very specific use cases (e.g., running Docker-in-Docker, certain low-level system tools), but a significant security risk for typical application workloads and should be avoided unless there's a specific, well-understood need for it.

### Q77. What are Linux capabilities, and how does Docker's default capability set relate to security?
Linux capabilities break down the traditionally all-or-nothing root privilege into a set of discrete, individually grantable privileges (e.g., `NET_BIND_SERVICE` for binding to privileged ports, `CHOWN` for changing file ownership). Docker containers run with a **restricted default set** of capabilities (not the full root capability set, even when the container's internal user is root) — and following least-privilege practice, this default set can be further reduced (`--cap-drop`) to only what a specific application genuinely needs, or specific additional capabilities explicitly added (`--cap-add`) only when required.

### Q78. How do you scan Docker images for known vulnerabilities?
Using image vulnerability scanning tools — **Docker Scout** (Docker's own integrated scanning), **Trivy** (a popular open-source scanner), **Snyk**, or cloud-registry-integrated scanners (ECR/GCR image scanning) — which analyze an image's installed packages/dependencies against known vulnerability databases (CVEs), typically integrated into a CI/CD pipeline to catch vulnerable images before they're deployed.

### Q79. How should secrets (API keys, database passwords) be handled in Docker, and why shouldn't they be baked into an image?
Secrets should **never** be included directly in a `Dockerfile` (via `ENV`, `ARG`, or `COPY`ing a secrets file) — even if seemingly "removed" in a later layer, the secret typically remains recoverable from the earlier image layer's history. Instead, secrets should be injected at **runtime**: via environment variables passed at `docker run`/Compose time (though these are visible via `docker inspect`, so care is still needed), Docker's native `--secret` mechanism (Swarm-integrated, mounting secrets as in-memory files rather than environment variables), or an external secrets manager (Vault, AWS Secrets Manager) that the application fetches credentials from at startup.

### Q80. What is Docker's `--read-only` flag, and how does it improve container security?
Runs a container's root filesystem in **read-only** mode, preventing any writes to the container's filesystem at all (aside from explicitly mounted writable volumes/tmpfs mounts for directories that genuinely need write access) — reducing the potential impact of a compromised application attempting to write malicious files or modify the container's filesystem.

### Q81. What is the principle of least privilege as applied broadly to Docker container configuration?
Configuring a container with only the **minimum** access/capabilities/resources it genuinely needs to function: a non-root user, a minimal capability set, no unnecessary volume/host mounts, resource limits (Section 14) to prevent a single container from monopolizing host resources, and network restrictions limiting which other services it can actually reach — collectively reducing the potential blast radius of any single compromised container.

---

## 12. Orchestration: Docker Swarm & Kubernetes Basics

### Q82. What is container orchestration, and why is it needed beyond single-host Docker/Compose?
Orchestration refers to automated management of containerized applications across a **cluster of multiple machines** — handling scheduling (deciding which node runs which container), scaling, load balancing, self-healing (restarting/rescheduling failed containers), rolling updates, and service discovery across the cluster — capabilities that plain Docker/Compose (designed around a single host) don't provide at cluster scale.

### Q83. What is Docker Swarm?
Docker's own **built-in, native orchestration** solution, turning a group of Docker hosts into a single logical cluster (a "swarm") that can be managed collectively — using largely the same Docker CLI/Compose-like syntax developers are already familiar with, making it comparatively simple to adopt, though it has seen far less industry adoption than Kubernetes for large-scale production use in recent years.

### Q84. What are the basic building blocks of Docker Swarm (nodes, services, tasks)?
- **Node** — an individual Docker Engine instance participating in the swarm, either a **manager** node (participates in cluster management/scheduling decisions, maintains cluster state) or a **worker** node (executes containers as instructed by managers).
- **Service** — a declarative definition of a desired containerized workload (which image, how many replicas, networking/volume configuration) — the swarm's job is to ensure the actual running state matches this desired state.
- **Task** — a single running container instance, representing one replica of a service, scheduled onto a specific node by the swarm's scheduler.

### Q85. What is the difference between Docker Swarm and Kubernetes, at a high level?
Both orchestrate containers across a cluster, but **Kubernetes** has become the dominant, much more feature-rich and widely-adopted standard — offering a vastly larger ecosystem, more sophisticated scheduling/networking/storage abstractions, and broader cloud-provider/tooling support, at the cost of significantly greater complexity to learn and operate. **Docker Swarm** is simpler to set up and operate, integrates natively with the Docker CLI/Compose file format, but has a much smaller ecosystem and has largely fallen out of favor for new large-scale production deployments in the broader industry, in favor of Kubernetes.

### Q86. How does a Kubernetes Pod relate to a Docker container?
A **Pod** is Kubernetes' smallest deployable unit — typically wrapping **one** (or occasionally several tightly-coupled) container(s) that share the same network namespace and storage volumes, scheduled and managed together as a single unit by Kubernetes. Kubernetes itself doesn't require Docker specifically as its container runtime (it uses the standardized **Container Runtime Interface, CRI**, commonly backed by `containerd` or CRI-O directly, without Docker Engine as an intermediary in modern Kubernetes versions) — but the container images Kubernetes runs are typically still built using Docker (or another OCI-compatible builder) and remain fully OCI-compliant, standard Docker images.

### Q87. What is the relationship between building images with Docker and deploying them to Kubernetes?
Developers commonly still use `docker build`/Dockerfiles to create OCI-compliant images and push them to a registry, exactly as they would for any other deployment target — Kubernetes then pulls and runs those same images using its own scheduling/orchestration logic (via its configured container runtime), meaning Docker's role in a Kubernetes-based workflow is typically centered on image **building**, with Kubernetes (not Docker Engine/Swarm) handling the actual **orchestration/runtime** management in production.

---

## 13. Logging, Monitoring & Debugging

### Q88. What are Docker logging drivers, and what are common options?
A logging driver determines how/where a container's `STDOUT`/`STDERR` output is captured and stored. Common drivers: `json-file` (the default — writes logs as JSON to a local file on the host, readable via `docker logs`), `syslog` (forwards to the host's syslog service), `journald`, `gelf`/`fluentd` (forwards to a centralized log aggregation system), and `awslogs`/other cloud-specific drivers — the choice matters significantly for production observability, since the default `json-file` driver alone doesn't provide centralized, cross-host log aggregation.

### Q89. Why is centralized logging important for containerized applications, beyond `docker logs`?
Because containers are ephemeral (routinely created, destroyed, and rescheduled, especially under an orchestrator), relying solely on `docker logs` (which only shows logs for containers that currently exist, and are lost once a container is removed) is insufficient for production observability — centralized logging solutions (the ELK/EFK stack, Loki, cloud-provider logging services) aggregate logs from all containers across all hosts into a persistent, searchable system, independent of any individual container's lifecycle.

### Q90. How do you debug a container that's crashing immediately on startup?
- Check `docker logs <container>` for error output from the failed startup attempt.
- Run `docker inspect <container>` to check the exit code and any relevant configuration issues.
- Try running the image interactively with an overridden entrypoint to get a shell instead: `docker run -it --entrypoint sh <image>`, to manually inspect the filesystem/environment and diagnose the issue.
- Check resource limits — a container killed due to hitting a memory limit (OOMKilled) shows a specific exit status/flag in `docker inspect`, distinguishing it from an application-level crash.

### Q91. What does an exit code of `137` typically indicate?
Exit code `137` (128 + 9, where 9 is `SIGKILL`'s signal number) commonly indicates the container was forcibly killed — most often due to being **OOM-killed** (the Linux kernel's out-of-memory killer terminating it for exceeding its memory limit), though it can also result from an explicit `docker kill` or `SIGKILL` sent for another reason.

### Q92. What monitoring tools/approaches are commonly used for Docker containers in production?
`docker stats` for quick, ad hoc live resource checks; **cAdvisor** (Container Advisor, a Google-originated tool) for detailed per-container resource usage metrics; **Prometheus** (commonly paired with cAdvisor or Docker's built-in metrics endpoint) for time-series metrics collection and alerting; and **Grafana** for visualization dashboards built on top of that collected metric data — together forming a common open-source container monitoring stack.

---

## 14. Performance & Resource Management

### Q93. How do you limit a container's CPU and memory usage, and why is this important?
```bash
docker run --memory="512m" --cpus="1.5" my-app
```
Without explicit limits, a single container could consume unbounded host resources, potentially starving other containers (or the host itself) — especially important in shared, multi-tenant environments. Memory limits also protect against runaway memory leaks within a single misbehaving container from destabilizing the entire host.

### Q94. What happens when a container exceeds its configured memory limit?
The Linux kernel's **OOM killer** terminates the offending process within the container (typically the main containerized process itself), causing the container to exit (commonly with exit code 137 — see Q91) — the container does **not** simply have its memory usage capped/throttled; exceeding a hard memory limit results in forced termination.

### Q95. What is the difference between CPU limits (`--cpus`) and CPU shares (`--cpu-shares`)?
`--cpus` sets a **hard cap** on the maximum CPU resources a container can use (e.g., `--cpus="1.5"` limits it to at most 1.5 CPU cores' worth of processing time, even if more is available and idle). `--cpu-shares` sets a **relative weighting** for CPU access **only when there's contention** — it doesn't cap usage at all when the host has spare CPU capacity; it only affects how CPU time is proportionally divided among competing containers when the host is under CPU pressure.

### Q96. What are common causes of poor Docker performance, and how are they diagnosed/mitigated?
- **Bloated images/slow builds** — mitigated via multi-stage builds, better caching, minimal base images (Section 9).
- **Excessive I/O from bind mounts** (particularly noticeable on Docker Desktop's macOS/Windows virtualization layer, where cross-filesystem-boundary I/O can be notably slower than native Linux) — mitigated by using volumes instead of bind mounts where possible, or platform-specific performance-tuning options.
- **Resource contention** from unbounded/unconfigured resource limits — mitigated by setting appropriate `--memory`/`--cpus` limits.
- **Inefficient logging** (excessive log volume without log rotation configured) — mitigated by configuring log rotation options (`max-size`, `max-file`) on the logging driver.

### Q97. What is log rotation, and how do you configure it for Docker's default logging driver?
Without configuration, the default `json-file` logging driver can allow log files to grow **unbounded**, potentially consuming significant disk space over a long-running container's lifetime. Log rotation limits this by capping the maximum size/number of retained log files:
```bash
docker run --log-opt max-size=10m --log-opt max-file=3 my-app
```

---

## 15. CI/CD with Docker

### Q98. How is Docker typically used within a CI/CD pipeline?
A typical pipeline: checkout code → run tests (often inside a container matching the production runtime environment, ensuring consistency) → build a Docker image (often leveraging multi-stage builds) → tag the image (commonly with the Git commit SHA and/or a semantic version) → scan the image for vulnerabilities → push the image to a registry → deploy the new image to staging/production (via an orchestrator, or a simpler deployment mechanism referencing the new image tag).

### Q99. What is "Docker-in-Docker" (DinD), and what are its use cases/caveats?
Running a Docker daemon **inside** a container (typically needed in CI pipelines that themselves run inside containers but need to build/run other Docker images as part of the pipeline). Caveats: DinD often requires the `--privileged` flag (a security concern, Q76), can have subtle layer-caching/networking quirks compared to a "normal" Docker setup, and an alternative — mounting the **host's** Docker socket into the CI container (`-v /var/run/docker.sock:/var/run/docker.sock`) — is sometimes used instead, letting the CI container use the host's Docker daemon directly rather than running a nested one, though this approach has its own security implications (the CI container effectively gains control over the host's Docker daemon).

### Q100. How do you leverage build caching effectively in a CI/CD pipeline, given that CI runners are often ephemeral (starting with no local cache)?
Since a fresh CI runner typically has no pre-existing Docker build cache, strategies include: using a **registry-based cache** (`--cache-from`/`--cache-to` with BuildKit, pulling a previous image build's layers from the registry itself as a cache source before building), persisting a build cache directory between CI runs if the CI platform supports it, and structuring the `Dockerfile` for optimal cache reuse (Q29) so that even a partial cache hit meaningfully speeds up the build.

### Q101. What is BuildKit, and how does it improve on the legacy Docker builder?
BuildKit is Docker's modern build engine (the default builder since Docker 23.0+), offering significant improvements over the legacy builder: better parallelization of independent build stages, more efficient caching (including remote/registry-based cache import/export), build secrets support (`--secret`, allowing sensitive build-time data to be used without it being persisted into any image layer), and SSH agent forwarding for private dependency access during builds.

---

## 16. Miscellaneous & Best Practices

### Q102. What is the difference between `COPY` and mounting a bind mount for source code during development vs. production?
In **production**, application code should be `COPY`'d directly into the image at build time, producing a self-contained, immutable artifact. In **development**, a bind mount is commonly used instead to map the local source directory directly into the running container, enabling live code changes to be reflected immediately without needing to rebuild the image on every change — a pattern generally reserved for local development, not production deployments.

### Q103. Why is pinning specific image versions/tags (rather than relying on `latest`) considered a best practice?
Using a specific, pinned tag (or, more strongly, a digest — Q20) ensures **reproducibility** — the exact same image is used every time, across every environment and every deployment, rather than potentially pulling a different, unexpected version of `latest` at different points in time (which could introduce breaking changes or vulnerabilities without any explicit, auditable change to your own configuration).

### Q104. What is the difference between `docker system prune` and more targeted prune commands (`docker container prune`, `docker image prune`, `docker volume prune`)?
`docker system prune` removes **all** unused containers, networks, and dangling images together in one command (with `-a` also removing all unused images, not just dangling ones, and `--volumes` additionally removing unused volumes) — a broad, convenient cleanup command, whereas the individual `docker <resource> prune` commands allow more targeted cleanup of just one resource type at a time, useful when you want more granular control over what gets removed.

### Q105. What is the difference between an ENTRYPOINT script pattern and simply specifying the application binary directly?
A common pattern uses a small shell script as the `ENTRYPOINT` (e.g., `docker-entrypoint.sh`) to perform setup/initialization logic (waiting for a dependency to be ready, running database migrations, processing configuration templates) **before** finally executing the actual application (often via `exec "$@"` at the script's end, which is important — using `exec` replaces the shell script process with the application process directly, ensuring the application correctly becomes PID 1 and receives signals properly, rather than remaining a child process of the still-running shell script).

### Q106. What are health checks (`HEALTHCHECK` in a Dockerfile), and how do they differ from a simple "is the process running" check?
```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```
A `HEALTHCHECK` instruction defines a command Docker periodically runs **inside** the container to assess whether the application is genuinely healthy/responsive — going beyond simply checking whether the container's main process is still running (a process can be alive but hung, deadlocked, or otherwise non-functional despite technically "running"), giving orchestration tooling (and `depends_on` conditions in Compose) a more meaningful signal for readiness/liveness decisions.

### Q107. What are common Docker anti-patterns to avoid?
- Running multiple unrelated, loosely-coupled processes/services inside a single container instead of one primary process per container (breaks the general principle of container = single responsibility, complicating logging, scaling, and health checks).
- Storing persistent application data in a container's writable layer instead of a volume.
- Using `latest` tags in production deployments instead of pinned, specific versions.
- Baking secrets directly into image layers via `Dockerfile` instructions.
- Ignoring `.dockerignore`, bloating build context and images with unnecessary files.
- Running containers as root without a specific need to.
- Not setting resource limits, risking one container starving others on a shared host.
- Using shell form `CMD`/`ENTRYPOINT` without considering signal-handling/PID 1 implications.
- Not leveraging multi-stage builds, shipping bulky images containing unnecessary build tooling.

### Q108. What are general Docker best practices for production applications?
- Use minimal, well-maintained base images and multi-stage builds to keep final images small and secure.
- Pin specific image versions/digests rather than relying on `latest`.
- Run containers as a non-root user with a minimal capability set.
- Store persistent data in volumes, never in a container's writable layer.
- Define health checks so orchestration tooling can make informed readiness/liveness decisions.
- Set explicit CPU/memory resource limits appropriate to each service's actual needs.
- Use a `.dockerignore` file and structure Dockerfile instructions to maximize build cache effectiveness.
- Never bake secrets into images; inject them at runtime via a proper secrets mechanism.
- Regularly scan images for known vulnerabilities as part of the CI/CD pipeline.
- Centralize logging and monitoring rather than relying solely on per-container `docker logs`.
- Use `exec` form for `CMD`/`ENTRYPOINT` (and `exec "$@"` in entrypoint scripts) for correct signal handling.

---

*This guide covers the core Docker interview surface area. For role-specific deep dives (e.g., deep Kubernetes orchestration internals, advanced BuildKit features, or specific cloud-provider container service integrations like ECS/Fargate), further topic-specific preparation is recommended.*

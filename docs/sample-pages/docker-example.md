# Docker

**Author:** Me
**Tags:** `docker`, `devops`
**Status:** Enriched

---

## What I Learned

Docker makes it possible to package an application and all its dependencies
into a single container, so it runs the same way on every machine. As an
intern, the biggest aha moment was realizing that "it works on my machine"
is no longer an excuse — if it runs in the container, it runs everywhere.
I also learned that containers are not virtual machines; they share the
host OS kernel, which makes them much lighter and faster to spin up.

## Key Concepts

Images are the blueprints for containers — read-only templates built from
a `Dockerfile`. Containers are running instances of those images, isolated
from the host system. Volumes let you persist data outside the container
lifecycle, so your database doesn't disappear when a container stops.
Docker Compose ties multiple services together (e.g. a web app + a
Postgres database) with a single `docker-compose.yml` file, making local
development dramatically simpler.

## Mistakes I Made

The first mistake was forgetting to add a `.dockerignore` file, which
caused the entire `node_modules` folder to be copied into the image —
making it huge and slow to build. Another early blunder was running
everything as `root` inside the container, which is a security risk that
got flagged immediately in code review. I also confused image layers and
kept invalidating the cache by copying source files before installing
dependencies, which made every build take far longer than necessary.

## References

- Docker Official Documentation
- Play with Docker
- Docker Curriculum
- Docker Deep Dive by Nigel Poulton

---

## 🤖 AI-Generated Enrichment

### TLDR

Docker packages an application and its dependencies into portable
containers that run identically anywhere, eliminating "it works on my
machine" issues. Unlike VMs, containers share the host OS kernel, making
them lightweight and fast. Key building blocks are images (blueprints),
containers (running instances), volumes (persistent data), and Docker
Compose (multi-service orchestration).

### FAQs

**Q: What's the difference between a Docker image and a container?**
A: An image is a read-only blueprint built from a Dockerfile. A container
is a running instance of that image — isolated from the host system.

**Q: Why do containers start faster than virtual machines?**
A: Containers share the host OS kernel instead of running their own full
OS, which makes them much lighter and quicker to spin up than VMs.

**Q: How do you avoid losing data when a container stops?**
A: Use volumes — they persist data outside the container's lifecycle, so
databases and other stateful data survive even after the container is
removed.

**Q: Why is running a container as `root` risky?**
A: If an attacker breaks out of the container, running as root gives them
elevated privileges on the host system too.

**Q: Why does copying source files before installing dependencies slow
down builds?**
A: Docker caches each layer of a build. Copying source files (which
change often) before dependencies (which change rarely) invalidates the
cache on nearly every build.

### Quiz

1. **What's the main difference between a container and a virtual
   machine?**
   *Containers share the host OS kernel, while VMs run their own full
   guest OS — making containers lighter and faster.*

2. **What does a `.dockerignore` file do, and why does forgetting it
   cause problems?**
   *It excludes files/folders (like `node_modules`) from being copied
   into the image. Forgetting it bloats the image size and slows builds.*

3. **What is Docker Compose used for?**
   *Orchestrating multiple related services with a single
   `docker-compose.yml` file, simplifying local development.*

4. **Why should you install dependencies before copying source code in a
   Dockerfile?**
   *To preserve Docker's layer caching — dependencies change less often
   than source code.*

5. **What security risk is associated with running a container as
   root?**
   *It increases the potential damage if the container is compromised.*

### Related Topics

- Git (categorical overlap: both are core dev-workflow tools)

### Suggested Next Learning

- Docker Networking
- Multi-stage Docker builds
- Docker security basics
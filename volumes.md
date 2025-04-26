# Overview

This session covers Docker Volumes and Bind Mounts, focusing on solving persistence challenges in containers. These are critical concepts where many beginners get confused, but this explanation simplifies them through real-world examples.

## Why Persistent Storage?

**Problem 1: Ephemeral Containers**

Containers are ephemeral; once a container stops or crashes, all data inside it (e.g., logs created by applications like Nginx) is lost. Critical information for audits and security logs gets deleted.

**Problem 2: Shared Data Between Containers**

In multi-container setups (e.g., frontend and backend), if backend writes files (JSON, YAML, HTML) and the container crashes without persistent storage, the frontend loses historical data.

**Problem 3: Containers Reading Host Files**

Applications inside containers often need to read files generated externally (like by a cron job on the host). Containers by default cannot access host directories.

**Solutions: Bind Mounts and Volumes**

### 1. Bind Mounts

-Bind a specific host directory to a directory inside the container.

-Example: Bind /app from the host to /app inside the container.

-Useful when you need the container to immediately access/update specific host files.

-Any file written to the bind mount directory persists even if the container goes down.

### 2. Volumes

-Managed by Docker itself; volumes are stored in Docker's storage locations (e.g., /var/lib/docker/volumes/).

-Volumes provide better abstraction, management, and flexibility:

-You can create volumes using Docker CLI (docker volume create).

-Volumes can be shared between multiple containers.

-Volumes can be moved across hosts (e.g., using NFS, S3, EC2 external storage).

-Volumes allow high-performance storage solutions.

-Volumes make it easier to backup, restore, and inspect data.

## Practical Differences: -v vs --mount

-v (short form): Concise but less descriptive.

--mount (verbose form): Recommended in production for clarity and better management.

Example: specifying source, target, and type explicitly.

Always prefer --mount over -v in organizational or collaborative environments for better readability.

### Key Commands

**List Volumes:**

```docker volume ls```

**Inspect a Volume:**

```docker volume inspect <volume-name>```

**Create a Volume:**

```docker volume create <volume-name>```

**Run a Container with a Volume Mounted:**

```docker run -d --name <container-name> --mount source=<volume-name>,target=/app <image-name>```

**Delete a Volume:**

Make sure the container using it is stopped and removed first.

```docker volume rm <volume-name>```

**Delete Multiple Volumes:**

```docker volume rm <volume1> <volume2>```

### Key Points

-Use Volumes over Bind Mounts when possible for better management and flexibility.

-Always stop and remove containers before deleting their associated volumes.

-Volumes can be used for sharing data between containers, backup, and migration across different hosts.

-Choose --mount syntax over -v for clarity.

## Conclusion

Understanding Docker storage (Bind Mounts and Volumes) is crucial for building reliable and persistent containerized applications. This class provides a clear practical understanding, enabling you to manage persistent data effectively.


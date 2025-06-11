## Core Configuration Files

*   **`.devcontainer/devcontainer.json`**: The primary configuration file. It tells the GitHub Actions how to create and
    access the dev container which will run your code
*   **`Dockerfile`** (optional): If you need an advanced container configuration
*   **`docker-compose.yml`** (optionally): A YAML file for defining and running multi-container Docker applications,
    can be combined with `Dockerfile` for advanced configuration of containers

---

## Scenario 1: Plain `devcontainer.json` (Using a Pre-built Image)

This is the simplest approach, suitable when a pre-built Docker image on a registry (like Docker Hub) meets your needs.

**When to use:**
*   You need just a standard environment (e.g., `mcr.microsoft.com/vscode/devcontainers/java`).
*   No custom software installation or complex setup is required beyond
    what the image and devcontainer features and extensions provide


**File Structure:**
```
your-project/
└── .devcontainer/
    └── devcontainer.json
```

**`.devcontainer/devcontainer.json` Example:**
```json
{
  "name": "Java",
  "image": "mcr.microsoft.com/devcontainers/java:1-21",
  "features": {
    "ghcr.io/devcontainers/features/java:1": {
      "version": "none",
      "installMaven": "true",
      "mavenVersion": "3.8.6"
    }
  }
}
```
**Explanation:**
* Github Actions pulls the specified `image` and runs it.
* Your local project folder is mounted into `/workspace` (by default, unless you specify `workspaceFolder` property) inside the container.
* `features` and `customizations` are applied to the container (additional features installed, etc.)

**Sample repository:**

https://github.com/jetbrains-junie/2048
https://github.com/jetbrains-junie/2048/blob/main/.devcontainer/devcontainer.json

---

## Scenario 2: `devcontainer.json` + `Dockerfile` (Custom Single Container)

Use this when you need a customized single-container environment.

**When to use:**
*   You need specific OS packages, tools, or libraries installed.
*   You need a particular user setup or environment variables configured at build time.
*   You only require one container for your development.

**File Structure:**
```
your-project/
├── .devcontainer/
│   └── devcontainer.json
├── Dockerfile          // Your custom Dockerfile
└── (your project files)
```

**`Dockerfile` Example:**
```dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    # Add other dependencies here
    && rm -rf /var/lib/apt/lists/*

# Set working directory (optionally)
# WORKDIR /workspace/${localWorkspaceFolderBasename}

# Keep the container running
CMD ["sleep", "infinity"]
```

**`.devcontainer/devcontainer.json` Example:**
```json
{
  "name": "My Custom Python App (Dockerfile)",
  "build": {
    // path to your Dockerfile (relative to devcontainer.json)
    "dockerfile": "../Dockerfile",
    // build context (usually project root)
    "context": ".."               
  },
  // using the Dockerfile also requires the explicit declaration of workspaceFolder.
  // Junie always expects it to be at the default location
  // which is "/workspace/${localWorkspaceFolderBasename}"
  // for instance, /workspace/kotlin-spring-chat
  "workspaceFolder": "/workspace/${localWorkspaceFolderBasename}",
  
  // declaring a "workspaceFolder" also requires declaring mount point for it
  // see https://containers.dev/implementors/json_reference/ for details
  // here we need to mount a folder in github actions to the devcontainer
  // at the standard location which is "/workspace/${localWorkspaceFolderBasename}"
  "workspaceMount": "source=${localWorkspaceFolder},target=/workspace/${localWorkspaceFolderBasename},type=bind,consistency=cached",

  // we can still use features here, they will be applied to the container
  // after creation
  "features": {
    "ghcr.io/devcontainers/features/java:1": {
      "version": "17",
      "installMaven": "true",
      "mavenVersion": "3.8.6"
    }
  }
}
```
**Explanation:**
* Junie GitHub app uses the `Dockerfile` (specified in `build.dockerfile`) and `build.context` (Docker build context) to build a custom Docker image.
* You can still use devcontainer's `features` and `customizations` when using `Dockerfile`

**Note:**
* **when using `Dockerfile` you need to declare `workspaceFolder` and `workspaceMount`, which will explicitly mount project
  directory into `/workspace/<project-name>` otherwise the started container won't have the access to the sources
  (the same place as devcontainers use by default) in `devcontainer.json`
* Junie GitHub app uses some external tools, so it's recommended to use a devcontainer-ready
  image such as `mcr.microsoft.com/devcontainers/base:ubuntu` instead of simple `ubuntu` image. In the latter case, ensure you
  have `git`, `wget` and `sudo` installed

**Sample repository**:

https://github.com/jetbrains-junie/kotlin-spring-chat
https://github.com/jetbrains-junie/kotlin-spring-chat/blob/main/.devcontainer/devcontainer.json
https://github.com/jetbrains-junie/kotlin-spring-chat/blob/main/Dockerfile

---

## Scenario 3: `devcontainer.json` + `docker-compose.yml` (Multi-Container with Pre-built Images)

Use this when your application requires multiple services (e.g., app + database) and these services can use pre-built images.

**When to use:**
*   You need to orchestrate multiple services (e.g., web app, database, cache).
*   The services can be defined using existing images from a registry.
*   You want to manage service configurations (ports, env vars, networks, volumes) in `docker-compose.yml`.

**File Structure:**
```
your-project/
├── .devcontainer/
│   ├── devcontainer.json
│   └── docker-compose.yml    // Defines your services
└── (your project files)
```

**`docker-compose.yml` Example:**
```yaml
services:
  devcontainer_app:
    image: 'mcr.microsoft.com/devcontainers/java:1-17'
    volumes:
      # mount project's parent folder as /workspace,
      # so the project itself will be available at /workspace/<project-name>
      # local path is relative to docker-compose.yml
      - ../../:/workspace/:cached
    command: 'sleep infinity'
    # network with service_db
    network_mode: 'service:postgres_db'

  postgres_db:
    image: 'postgres:latest'
    environment:
      - 'POSTGRES_DB=mydatabase'
      - 'POSTGRES_PASSWORD=secret'
      - 'POSTGRES_USER=myuser'
    ports:
      - '5432'
```

**`.devcontainer/devcontainer.json` Example:**
```json
{
  "name": "Java",
  // relative path to docker-compose.yaml which will be used to launch the devcontainer
  "dockerComposeFile": "docker-compose.yaml",
  // particular service from "dockerComposeFile" which will be used as a primary container.
  // sources will be mounted into that container at "workspaceFolder"
  "service": "devcontainer_app",
  "workspaceFolder": "/workspace/${localWorkspaceFolderBasename}",
  "features": {
    "ghcr.io/devcontainers/features/java:1": {
      "version": "none",
      "installMaven": "true",
      "mavenVersion": "3.8.6"
    }
  }
}
```
**Explanation:**
*   `dockerComposeFile` points to your `docker-compose.yml`.
*   `service` specifies which service defined in `docker-compose.yml` devcontainer should use (in this case, `devcontainer_app`).
*   `workspaceFolder` is the path *inside the specified service's container*.
*   Docker Compose handles starting all defined services and their dependencies.

**Note:**
* similar to the Dockerfile, you need to explicit mount `workspaceFolder` to `/workspace/${localWorkspaceFolderBasename}`.
  Since, variables are not available inside `docker-compose.yml` we mount project's parent directory to `/workspace/`

**Sample repository**:

https://github.com/jetbrains-junie/workout-logger-demo
https://github.com/jetbrains-junie/workout-logger-demo/blob/main/.devcontainer/devcontainer.json
https://github.com/jetbrains-junie/workout-logger-demo/blob/main/.devcontainer/docker-compose.yaml

---

## Other scenarios

For more advanced scenarious, such as `docker-compose.yml`+`Dockerfile`, multiple `docker compose` array, etc., please
refer to the https://containers.dev/implementors/json_reference/.

The main requirement is still that project sources are mounted into: `/workspace/${localWorkspaceFolderBasename}`
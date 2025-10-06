# Copilot Instructions for docker-laravel-vue

## Project Overview
This repository provides a Docker-based development environment for a Laravel backend, Vue.js frontend, and MySQL database. The architecture is designed for local development and easy onboarding.

## Architecture & Components
- **image-back/**: Intended for the Laravel backend Docker image. (Currently empty; expects code in `../../back`.)
- **image-front/**: Contains the Dockerfile for the Vue.js frontend. Mounts local `../../front` code into `/app`.
- **image-mysql/**: Custom MySQL image with configuration in `assets/my.cnf`.
- **docker-compose.yml**: Orchestrates all services. There is a copy in both the root and `image-mysql/` (they are identical).

### Service Overview
- **vue-frontend**: Runs Vue app with `yarn serve` on port 8080. Mounts local code for live reload.
- **laravel-backend**: Runs Laravel app on port 8001 (mapped to 8000 in the container). Mounts local code for live reload. Depends on MySQL.
- **mysql**: Custom MySQL 8.0.26 image, with UTF-8 defaults and high connection limits. Data is persisted to `/var/lib/mysql`.

## Developer Workflows
- **Start all services:**
  ```sh
  docker-compose up --build
  ```
- **Stop all services:**
  ```sh
  docker-compose down
  ```
- **Rebuild a single service:**
  ```sh
  docker-compose build <service>
  ```
- **Access MySQL CLI:**
  ```sh
  docker exec -it mysql mysql -uroot -p
  ```

## Conventions & Patterns
- **Source code is mounted from outside the repo** (`../../front` and `../../back`). Ensure these directories exist and contain your app code.
- **MySQL config**: Custom settings in `image-mysql/assets/my.cnf` (e.g., UTF-8, high max_connections).
- **No code in `image-back/` or `image-front/`**: Only Dockerfiles are present; all app code is expected outside this repo.
- **Healthchecks**: MySQL service uses a simple process check for readiness.

## Integration Points
- **Network**: All services share the `network-laravel-vue` Docker network for internal communication.
- **Volumes**: Data and code are mounted for persistence and live reload.

## Key Files
- `docker-compose.yml` (root): Main orchestration file.
- `image-front/Dockerfile`: Vue.js build environment.
- `image-mysql/Dockerfile` and `assets/my.cnf`: MySQL custom image and config.

---

For any unclear or missing conventions, please ask for clarification or provide feedback to improve these instructions.

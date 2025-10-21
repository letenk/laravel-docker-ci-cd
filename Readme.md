# LARAVEL DOCKER MULTI-STAGE PROJECT

This project uses **two separate Docker Compose files** to manage distinct environments (Development and Production), leveraging a robust **Multi-Stage Build** defined in `docker/php/Dockerfile`.

## Prerequisites

* **Docker Engine & Docker Compose**
* **Composer** (for initial project setup)

***

## Project Configurations

| Configuration File | Environment | Key Features |
| :--- | :--- | :--- |
| `docker-compose-dev.yml` | **Development** | Uses `volumes` to mount local code (`./laravel`) for **live code editing** and includes **Xdebug** for debugging. |
| `docker-compose-prod.yml` | **Production** | **Bakes code into the image** during build. Generates a **smaller, cleaner, and more secure** image without Xdebug. |

***


## Flowchart
See this: [Move](https://www.mermaidchart.com/play?utm_source=mermaid_live_editor&utm_medium=share#pako:eNqFlGuPmkAUhv_KiR-6mtQaQbMJpm2Ui9BVIHhJN7t-GGHESceBcKltzf73DjOyQrbN8gVI3vec9zxz4NIJkwh3tE6cofQIa-OZAb-mT6v1NFhrECXhD5zBviQ0GoTJKU1yDGW6g37_C8wu02AOprt1As9dmu7668vkmckKM64AAx9QSYvBErESUV4N_8Q0SU-YFaKAPuzyPnMThhrsUY57k4a5NqVZEpVhQRJ29bz2yMu9jD3jXlgVKMZg_sJhWYmlpLr0oTQqXZ33cVheIEp5lhSzCLOQ4FyDmBQf4Q9J6wTCp0ifKny65z_y90OWnD5fQWSa0tKrQm9cZJJHxGIwSMpvMaECTaXiTev8RjWmTyg5clJtMmZNRmlRq9s1nH4bj1UbVU2eWu8trmYzHxXH2wimRGUqXZOPbAXesnUuQiKpmKqQ1DS_G3hfxi2dpDG_xrEcd7rQmq17b4G8JmwMVQWEPsyqWW7VLRnUUrrW_4JaMqilCok4vk8DijLEE7R0Mqg1Erpg40J9vEDkdC31SKrHQr3CnCDOTiTPedYc7u_Hg_P53I9QgVqusXDZ9eGMmmvdAlE9vAsjKFlBTvhW35Y4bKVrN3FwqU4xYuCc-EY2A9kSjq0KQ3O3xdLwyRAlDLY8U5LBB3jgfwn4XW30amNMbTCc2cZZGK2SkqNzXX-LsOrrvYX_xwcwF45vT6ZraDIj3B0q3x2gCFHEV9XcmgvPr34uu6vfedfkB56x0deO5-4mnZe_da5keg)


## ⚙️ How to Run

### Step 1: Initial Setup

1.  **Project Code:** Ensure your Laravel application code resides in the `./laravel` folder.
    ```bash
    # Example if the folder is empty:
    composer create-project --prefer-dist laravel/laravel laravel
    ```
2.  **Environment Variables:** Create a **`.env`** file in your project root to define necessary variables (e.g., `APP_PORT`, `MYSQL_DATABASE`, etc.) used by the Compose files.

### Step 2: Build and Run

Use the `-f` flag to specify the correct Compose file for the desired environment.

#### A. Development Mode (For Local Coding & Debugging)

```bash
# 1. Build the image, specifying the development stage
docker compose -f docker-compose-dev.yml build

# 2. Run all services (App, Nginx, MySQL)
docker compose -f docker-compose-dev.yml up -d
```

#### B. Production Mode (For Deployment/Staging)
```bash
# 1. Build the image, specifying the production stage for optimization
docker compose -f docker-compose-prod.yml build

# 2. Run all services (App, Nginx, MySQL)
docker compose -f docker-compose-prod.yml up -d
```

## Access Application
Your application runs through Nginx and should be accessible at: http://localhost:<NGINX_PORT>/

## Xdebug Setup (Development Only)
To enable debugging in VS Code, ensure your launch.json has the correct path mapping to bridge the volume mount:

Host Config: Your docker-compose-dev.yml mounts the host directory (./laravel) to the container's working directory (${APP_WORKDIR}).

VS Code launch.json: Update the pathMappings to reflect this:

```JSON

{
    "name": "Listen for Xdebug (Docker)",
    "type": "php",
    "request": "launch",
    "port": 9003,
    "pathMappings": {
        "/var/www/html": "${workspaceRoot}/laravel" // Adjust /var/www/html if APP_WORKDIR is different
    }
}
```

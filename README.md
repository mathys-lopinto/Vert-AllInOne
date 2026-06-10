# Vert-AllInOne

Docker Compose setup to self-host [VERT](https://vert.sh) (frontend) and [vertd](https://github.com/VERT-sh/vertd) (backend) together.

## Usage

1. Clone the repository with its submodules:
   ```bash
   git clone --recurse-submodules https://github.com/mathys-lopinto/Vert-AllInOne
   cd Vert-AllInOne
   ```

   > If you already cloned without `--recurse-submodules`, initialize them with:
   > ```bash
   > git submodule update --init --recursive
   > ```

2. Copy the example env file and fill in the required values:
   ```bash
   cp .env.exemple .env
   ```

3. Set `ADMIN_PASSWORD` to something secure, then pick the compose file matching your GPU and run it (the `--build` flag builds the images from the submodules):

   | GPU | Command |
   |-----|---------|
   | CPU (no GPU / fallback) | `docker compose -f docker-compose.yml.cpu up -d --build` |
   | AMD / Intel | `docker compose -f docker-compose.yml.amd-intel up -d --build` |
   | NVIDIA | `docker compose -f docker-compose.yml.nvidia up -d --build` |

   > There is no default `docker-compose.yml`. You can either use `-f` as above, or copy the file of your choice to `docker-compose.yml` and run `docker compose up -d --build` as usual.

## Updating

To pull the latest changes and rebuild with the newest frontend/backend versions:

```bash
# 1. Update the main repo
git pull origin main

# 2. Update the submodules to their latest remote versions
git submodule update --remote --merge

# 3. Rebuild and restart (replace .cpu with your GPU variant)
docker compose -f docker-compose.yml.cpu down
docker compose -f docker-compose.yml.cpu up -d --build
```

> The `--build` flag is required: without it, Docker reuses the cached images and the new features won't appear.

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `ADMIN_PASSWORD` | Admin password for kept videos | *(required)* |
| `PUBLIC_URL` | Public URL of the vertd backend | `http://localhost:24153` |
| `PUB_VERTD_URL` | vertd URL as seen from the browser | `http://localhost:24153` |
| `PUB_HOSTNAME` | Frontend hostname (used for analytics) | `localhost:3000` |
| `VERTD_FORCE_GPU` | Force GPU mode: `amd`, `intel`, `nvidia`, `apple`, `cpu` | auto-detect |
| `VERTD_VAAPI_DEVICE_PATH` | VAAPI device path (AMD/Intel only) | `/dev/dri/renderD128` |
| `VERTD_PORT` | Exposed port for the backend | `24153` |
| `FRONTEND_PORT` | Exposed port for the frontend | `3000` |

> **Note:** If you're running AMD/Intel on Linux and get `auto_scale` filter errors, set `VERTD_FORCE_GPU=cpu` in your `.env`.

## Compose files

| File | GPU | Notes |
|------|-----|-------|
| `docker-compose.yml.cpu` | CPU | Works everywhere, no GPU device passthrough |
| `docker-compose.yml.amd-intel` | AMD / Intel | Passes `/dev/dri`, uses VAAPI |
| `docker-compose.yml.nvidia` | NVIDIA | Requires `nvidia-container-toolkit`, uses CUDA |

## Subprojects

- [`VertFrontend`](./VertFrontend) — VERT frontend (Svelte + TypeScript)
- [`Vertd-backend`](./Vertd-backend) — vertd daemon (Rust + FFmpeg)

## License

AGPL-3.0 — see [LICENSE](./LICENSE).

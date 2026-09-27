# Deployment and operations

CASSANDRA ships as one container that serves the API and the web app on `$PORT` (7860 by default).

## The container image

A three-stage build:

| Stage | Base | Does |
|---|---|---|
| 1. UI | Node.js 24 LTS (Alpine) | Installs locked npm dependencies and builds the web app |
| 2. Build | Python 3.11 slim + uv | Installs locked Python dependencies into a virtual environment |
| 3. Runtime | Python 3.11 slim | Copies only the virtual environment and the built UI |

The runtime image has no compilers, no Node.js and no source tree. It runs as an unprivileged user
(uid 1000), defines a health check, and is about 615 MB (mostly NumPy, SciPy and scikit-learn). At idle it
uses about 100 MB of memory.

## Running it

| Target | How | Notes |
|---|---|---|
| Local | One `make` command builds the UI and serves everything | For development and demos |
| Docker Compose | `docker compose up` | Hardened: read-only root filesystem, all Linux capabilities dropped, `no-new-privileges` |
| Render | A `render.yaml` blueprint; redeploys after CI passes on `main` | Ready for a public demo on a free plan; builds from the private repository |
| Any container host | The published image from GitHub Container Registry | Tagged releases include an SBOM and build provenance |

The hardened Compose configuration was verified by running it: the container reported healthy with a
read-only filesystem and no capabilities, and every smoke check passed.

## Hosting a demo on Render

The app is not publicly hosted at the moment; the case study shows recordings of it running instead.
It is ready to be: a blueprint file in the repository describes a service on Render's free plan, and
Render builds the same Dockerfile from the private repository, so hosting a demo never means publishing
the code. An earlier plan used Hugging Face Spaces, but a public Space exposes its source, which
defeats the point of a private implementation.

| Setting | Value | Why |
|---|---|---|
| Runtime | Docker, built by Render | One image everywhere: local, Compose, releases, demo |
| Plan | Free | Sleeps after 15 minutes without traffic; the first request then takes about a minute |
| Health check | `/api/health` | Traffic moves to a new deploy only once it answers |
| Deploys | After CI passes on `main` | A red build never reaches the demo |
| `CASSANDRA_TRUSTED_PROXY_HOPS` | `1` | Render's proxy appends the visitor's address, so each visitor gets their own rate limit |
| `PORT` | Set by Render | The start command binds to whatever port the platform assigns |

## Configuration

All settings are optional environment variables; see [Interfaces](INTERFACES.md#configuration). The two
that matter most in production:

- **`CASSANDRA_TRUSTED_PROXY_HOPS`**: set it to the exact number of reverse proxies in front of the app.
  Too low and every visitor shares one rate limit; too high and clients can spoof their address.
- **`CASSANDRA_RATE_LIMIT_PER_MINUTE`**: per client, for scan and arena requests.

## Operations

- **Health:** `GET /api/health` for liveness; the image's health check uses it.
- **Logs:** standard output; they contain verdicts and scores, never scanned text.
- **TLS:** terminate at the proxy and add `Strict-Transport-Security` there.
- **Scaling:** one worker per instance by design (rate-limit windows and arena passwords are in memory).
  One worker served about 260 scan requests per second in testing. Multiple replicas would need a shared
  store such as Redis.
- **Updates:** Dependabot proposes dependency updates; CI rebuilds and smoke-tests the image for each.

## Release process

1. Update the changelog and version numbers.
2. Tag the commit (`v0.2.0`).
3. The release workflow builds the image, publishes it to GitHub Container Registry with an SBOM and
   provenance attestation, and creates a GitHub release with generated notes.

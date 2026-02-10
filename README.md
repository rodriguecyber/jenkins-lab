# Facial App — Jenkins CI/CD Lab Documentation

This repository is a small Node.js service used for the ci/cd lab. It demonstrates:

- A minimal Express-based HTTP service (`server.js`).
- Local development and test workflow with `npm` and `jest`.
- Containerization with `Docker`.
- A simple CI/CD pipeline implemented in `Jenkinsfile`.

Repository contents (key files):

- `server.js` — application entrypoint (HTTP endpoints `/` and `/health`).
- `server.test.js` — unit tests (run with `npm test`).
- `Dockerfile` — production container image build rules.
- `Jenkinsfile.ci` — Jenkins pipeline used by the lab.
- `package.json` — npm scripts and dependencies.

Lab objective

Design and implement an end-to-end CI/CD pipeline in Jenkins that builds, tests, containerizes a
simple web service, pushes the image to a registry, and deploys it to an EC2 host.

Prerequisites

- Node.js >= 18
- npm (bundled with Node.js)
- Docker 
- Jenkins for running `Jenkinsfile`

Quickstart — Local

1. Install dependencies:

```bash
npm install
```

2. Start the server locally:

```bash
npm start
```

The server listens on port 4000 by default. Visit http://localhost:4000/ and http://localhost:4000/health

Quickstart — Tests

```bash
npm test
```

Docker — Build and Run

Build the image locally:

```bash
docker build -t facial-app:local .
```

Run with Docker:

```bash
docker run --rm -p 4000:4000 facial-app:local
```



Dockerfile notes

- The image is based on `node:18-slim` and sets `NODE_ENV=production`.
- It installs dependencies, copies `server.js`, creates a non-root user, and exposes port `4000`.
- A `HEALTHCHECK` calls `/health` so orchestrators can verify liveness.

Environment / Configuration

- `PORT` — port the app listens on (default: `4000`).
- `NODE_ENV` — environment mode (default in the Dockerfile: `production`).

You can override configuration when running locally, for example:

```bash
PORT=3000 npm start
```

CI/CD (`Jenkinsfile`)

The `Jenkinsfile` included in the repo implements these stages:

- Checkout the repository
- Install dependencies (`npm install`)
- Run tests (`npm test`)
- Build a Docker image and push it to Docker Hub (uses `docker-hub` credentials)
- SSH into a target server and deploy the pushed image (requires `ec2-ssh` credentials and `EC2_PUBLIC_IP`)

Important notes for Jenkins:

- The pipeline expects Jenkins credentials named `docker-hub` (username/password) and `ec2-ssh` (SSH key) to be configured in Jenkins credentials store.
- The deploy stage uses the `EC2_PUBLIC_IP` environment variable — set this in the Jenkins job or credentials.

Running the full CI locally (example)

To emulate the CI pipeline locally you can run the same commands from a shell (ensure Docker is logged in):

```bash
npm install
npm test
docker build -t <your-username>/my-facial-recognition-app .
docker login
docker push <your-username>/my-facial-recognition-app
```

Troubleshooting

- Port already in use: change `PORT` or stop the occupying process.
- Docker build failures: check Node version and `package.json` for incompatible native modules.
- Jenkins pipeline failing to push: verify `docker-hub` credentials and network access to Docker Hub.
- SSH deploy issues: ensure `ec2-ssh` key is configured and the target host allows your Jenkins agent to SSH.


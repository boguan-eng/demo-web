# demo-web

Demo app repo. Each directory under `images/` is one container image
(build context = that directory, with its own `Dockerfile`):

- `images/web` – static page served by unprivileged nginx on port 8080

CI (`.github/workflows/ci.yml`) discovers `images/*/` automatically:

- pull request: `docker build` only (no GCP access)
- push to `main`: build and push `<AR>/<image>:<sha>` (GitHub Environment `nonprod`)
- push of tag `v*`: copy the already-built image by digest to `<AR>/<image>:<tag>` (Environment `prod`)

GCP values are never stored in this repo. They come from GitHub Environment
secrets: `GCP_WIF_PROVIDER` and `GCP_AR_REPO` (both environments), plus
`GCP_AR_REPO_SOURCE` (prod only). Authentication uses direct Workload Identity
Federation (no service account, no keys).

Images are pushed to Artifact Registry by CI (see `.github/workflows/ci.yml`).

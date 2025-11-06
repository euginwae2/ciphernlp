# Onboarding

## Quick Start

1. Clone the repository and ensure you have access to required cloud resources and secrets.
2. Create a Python virtual environment and install dependencies:
   - pip install -r requirements.txt
3. Start local services for development:
   - Start a local Postgres or use a test SQLite
   - Run `docker-compose -f docker/local.yml up` to start API and a mock OCR worker
4. Run unit tests:
   - pytest tests/unit

---

## Developer Environment

- Recommended tools: Python 3.10+, Docker, kubectl, minikube or kind for local Kubernetes, gh CLI.
- Pre-commit hooks: run `pre-commit install` to enable formatting and static checks.
- Linting and formatting: black, ruff, isort enforced on pre-commit.

---

## Branching and Release Workflow

- **Main**: production-ready; protected branch, PR required, CI green.
- **Develop**: integration branch for feature stabilization.
- **Feature branches**: name `feature/<short-desc>`; open PR against develop.
- **Releases**: tag `vMAJOR.MINOR.PATCH` and create release PR from develop to main.

---

## Access and Secrets

- Request access to Kubernetes cluster and object storage from SRE.
- Secrets are stored in Vault; CI uses GitHub Actions with secrets configured in repo settings.
- Do not commit or store plaintext PII or production credentials in the repo.

---

## How to Run Locally

- Start mock OCR and API:
  - docker-compose -f docker/local.yml up --build
- Run a sample inference:
  - curl -F "document=@sample.pdf" http://localhost:8000/v1/documents/ingest?sync=true
- Run training script with a small sample:
  - python src/scripts/mk_dataset.py --sample-size 100
  - python src/ciphernlp/models/transformer/train.py --config configs/train.yml --dry-run

---

## Learning Resources

- notebooks/ contains EDA and semi_supervised_labeling examples.
- docs/architecture.md explains system topology and deployment.
- Reach out to the ML lead for domain-specific labeling guidelines and access to the labeling platform.

---

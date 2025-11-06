# Architecture

## Overview

CipherNLP is an end-to-end document intelligence platform optimized for financial KYC. It consists of three logical layers: Ingestion & Preprocessing, Model Development & Training, and Serving & MLOps. Each layer is decoupled to allow independent scaling, secure handling of sensitive data, and clear operational ownership.

---

## High-level Components

- **Ingestion Layer**
  - Document intake service (API / batch)
  - OCR engine and image preprocessing
  - Layout analysis and document standardization
- **Model Layer**
  - Baseline pipeline (TF‑IDF + Logistic Regression) for weak labeling
  - Transformer training and validation (PyTorch)
  - Feature store / artifact storage for datasets and model weights
- **Serving Layer**
  - FastAPI inference service for low-latency requests
  - Batch worker for heavy OCR, large-batch inference, and retraining jobs
  - Model registry and artifact delivery
- **MLOps & Observability**
  - Drift detector, metric collectors, alerting
  - CI/CD pipelines for image build, model retrain, and rollout
  - Kubernetes for orchestration, Prometheus/Grafana for metrics, and logging stack

---

## Data Flow

1. Client uploads document via API or batch ingest.
2. Ingestion service routes file to OCR worker; raw text + layout metadata returned.
3. Preprocessing standardizes text and augments with layout features.
4. Baseline model may produce weak labels for human review.
5. Final Transformer inference returns classification labels and structured extractions.
6. Results stored as JSON and optionally forwarded to downstream systems.
7. New labeled data feeds the retraining pipeline; drift detector triggers retrain when thresholds are exceeded.

---

## Deployment Topology

- **Kubernetes Cluster**
  - FastAPI service as Deployment with Horizontal Pod Autoscaler
  - OCR and batch workers as separate Deployments (node/pool affinity for GPU)
  - Retraining jobs as Kubernetes Jobs or Argo Workflows
  - Model artifacts on object storage (S3-compatible) with immutable versioning
- **Secrets and Config**
  - Secrets stored in Vault or Kubernetes Secrets
  - Config maps for non-sensitive runtime configuration
- **Networking and Security**
  - Internal-only mesh for model and worker traffic
  - Ingress with TLS termination and WAF rules for API
  - RBAC for service accounts and audit logging enabled

---

## Key Design Decisions

- Separate API and worker images optimize attack surface and allow different resource profiles.
- Versioned model artifacts and a registry prevent drift across environments.
- Modular pipeline design (ingest → preprocess → inference → store) makes it easy to replace OCR or model components without system-wide changes.
- Monitoring and automated retraining close the feedback loop for long-term accuracy.

---

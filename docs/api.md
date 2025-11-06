# API

## Purpose

FastAPI-based inference API exposing single-document and batch endpoints for classification and structured extraction, returning standardized JSON results suitable for downstream KYC workflows.

---

## Authentication and Authorization

- **Authentication:** Token-based (JWT) or mTLS for service-to-service calls.
- **Authorization:** Role-based checks for endpoints that return sensitive PII or trigger retraining.

---

## Endpoints

### POST /v1/documents/ingest

- **Description:** Upload a single document for end-to-end processing (OCR → preprocess → infer).
- **Request:** multipart/form-data with file field `document`; optional query params `sync=true|false`.
- **Response:** JSON with job id, processing status, summary metadata, and inference output when sync.

### POST /v1/documents/batch

- **Description:** Submit file bundle or S3 pointer for asynchronous batch processing.
- **Request:** JSON { "source": "s3://bucket/path" , "callback": "https://..." }
- **Response:** JSON { "job_id": "...", "accepted": true }

### GET /v1/documents/{job_id}

- **Description:** Retrieve processing status and result JSON.
- **Response:** JSON { "status": "pending|running|done|failed", "result": {...} }

### POST /v1/models/refresh

- **Description:** (Protected) Trigger model refresh or manual rollout; used by CI/CD or MLOps.
- **Request:** JSON { "model_version": "vX.Y", "force": true }
- **Response:** JSON { "accepted": true }

### GET /v1/health

- **Description:** Service and dependency health checks (OCR connectivity, S3, model loaded).
- **Response:** JSON { "status": "ok", "components": {...} }

---

## Response Schema (Canonical)

- **job_id**: string
- **document_id**: string
- **metadata**: { source, pages, text_length, layout_features }
- **predictions**: [ { label, confidence } ]
- **extractions**: { field_name: { value, confidence, span, page } }
- **processing_timestamps**: { received, ocr_done, infer_done }

---

## Performance and SLAs

- **Sync inference target:** p95 latency < 300ms for small documents (text-forward); scale per model and hardware.
- **Batch throughput:** horizontally scalable workers with parallel OCR and model batching.
- **Error handling:** deterministic error codes and retryable vs non-retryable semantics.

---

## Best Practices for Clients

- For high-volume ingestion, prefer batch endpoints with S3 pointers.
- Attach document metadata when available to improve downstream matching and auditability.
- Use pagination or streaming for large extraction payloads.

---

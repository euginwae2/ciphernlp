# Runbook

## Purpose

Operational playbook for on-call engineers to diagnose and remediate common production issues quickly while preserving data integrity and compliance.

---

## Key Contacts

- SRE Team: pager duty rotation
- ML Lead: model performance and retraining
- Security Team: incident response for data exposure

---

## Alerts and What They Mean

- **High API Latency**: p95 latency > threshold
  - Likely causes: model loading thrash, insufficient replicas, heavy batch jobs
  - Immediate actions: scale up replicas, inspect pod restarts, check model hot-reload logs
- **Increased Model Error Rate**: precision/recall drop beyond threshold
  - Likely causes: data drift, model degradation after rollout
  - Immediate actions: revert to previous model version, open retrain pipeline, tag dataset for investigation
- **OCR Failures or High Error Rate**
  - Likely causes: corrupted input, new file formats, OCR service unavailability
  - Immediate actions: divert to fallback OCR engine, escalate with SRE to restore OCR pod
- **Storage or S3 Errors**
  - Likely causes: permission changes, network, quota
  - Immediate actions: verify storage health, rotate credentials if expired, restore throttled access

---

## Incident Response Steps

1. Triage and classify incident severity.
2. Notify stakeholders and create an incident ticket.
3. Capture logs and relevant traces (request id, job ids).
4. If affecting model quality, rollback model deployment to last known good artifact.
5. If data exfiltration suspected, follow Security Team procedures and freeze affected pipelines.
6. After mitigation, conduct a postmortem and update runbook.

---

## Recovery Recipes

### Scale API When Under Load

1. Inspect HPA metrics: kubectl get hpa
2. Temporarily increase replicas: kubectl scale deploy api --replicas=5
3. Check downstream queues and backpressure

### Rollback Model Version

1. Identify last stable model in registry.
2. Update deployment config with stable model version and apply: kubectl apply -f infra/k8s/deployment.yaml
3. Verify model health via /v1/health and smoke tests.

### Requeue Failed Batch Jobs

1. List failed jobs and inspect logs.
2. Re-submit with corrected pointers or rehydrate inputs from object storage.

---

## Maintenance Tasks

- Rotate service credentials quarterly.
- Run smoke test suite before and after any cluster changes.
- Perform a monthly audit of sample outputs and model metrics for drift detection.

---

## Post Incident

- Create a concise postmortem with root cause, timeline, remediation, and action items.
- Add preventative measures to architecture.md or this runbook as needed.

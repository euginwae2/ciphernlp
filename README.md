# CipherNLP: Automated Document Intelligence for Financial KYC

## 🚀 Project Overview

**CipherNLP** is an end-to-end Natural Language Processing (NLP) system developed for the financial services industry to automate the **Know Your Customer (KYC)** process. The primary goal was to drastically improve efficiency and accuracy by automatically **extracting, classifying, and structuring information** from a large, heterogeneous volume of unstructured legal and financial documents (e.g., contracts, reports, and regulatory filings).

The project successfully transformed a slow, inefficient manual workflow into a scalable, production-ready ML service, resulting in over **80% reduction in document processing time**.

---

## ✨ Key Features & Business Impact

* **Automated Document Ingestion:** Handles mixed-format inputs (scanned and digital PDFs) using robust OCR and pre-processing techniques.
* **High-Accuracy Classification:** Uses a fine-tuned, **Transformer-based model** (PyTorch) to accurately classify documents and extract nuanced information based on context.
* **Scalable Deployment:** Deployed as a containerized service on **Kubernetes**, ensuring high availability and scalable inference.
* **Continuous MLOps:** Features an automated feedback loop for **data drift detection** and a **retraining pipeline** to maintain classification accuracy over time.
* **Structured Output:** Delivers extracted and classified data in a consistent, easy-to-integrate **JSON format**.

---

## ⚙️ Technology Stack

| Category | Technology | Purpose |
| :--- | :--- | :--- |
| **Document Processing** | **OCR** (Optical Character Recognition) | Converts scanned documents and PDFs into clean, raw text. |
| **Model Development** | **PyTorch** | Core framework for building and fine-tuning the transformer model. |
| **Core Model** | **Transformer-Based NLP** (e.g., BERT/RoBERTa) | Fine-tuned for context-aware classification and extraction from legal/financial text. |
| **API & Serving** | **FastAPI** | Creates a lightweight, high-performance, and scalable API endpoint for inference. |
| **Deployment** | **Docker, Kubernetes** | Containerizes the ML application for consistent deployment and manages service orchestration for scalability. |
| **Initial Modeling** | **TF-IDF, Logistic Regression** | Used for the initial **semi-supervised learning** baseline to accelerate the creation of the labeled training dataset. |

---

## 🏗️ Technical Deep Dive: The NLP Pipeline

### I. Data and Model Development

1.  **Initial Pre-processing:** Extensive pre-processing was performed to handle varying layouts, font sizes, and image qualities resulting from the initial OCR process.
2.  **Semi-Supervised Approach:** Due to the lack of a pre-labeled dataset for specific financial documents, a **TF-IDF + Logistic Regression** baseline was used to generate initial predictions. These were used to *aid Subject Matter Experts* in rapidly labeling a high-quality, custom dataset.
3.  **Final Model Training:** A **pre-trained Transformer model** was fine-tuned on the custom-labeled dataset. This approach prioritized efficiency and accuracy, leveraging the deep language understanding capabilities of the transformer architecture to capture the nuances of legal and financial context.
4.  **Validation:** Performance was rigorously evaluated using **cross-validation** and a separate hold-out test set, tracking key metrics like **Precision, Recall, and F1-score**.

### II. Production Deployment and MLOps

1.  **Containerization and API:** The entire application, including the Python environment and PyTorch model weights, was containerized using **Docker**. A **FastAPI** service was created to expose a low-latency endpoint capable of handling document processing requests and returning structured JSON.
2.  **Orchestration:** The Docker containers were deployed on a **Kubernetes cluster** to manage traffic, scaling, and self-healing capabilities, ensuring high-scale production readiness.
3.  **Continuous Monitoring & Retraining:**
    * A monitoring system tracked operational metrics (API latency) and model quality metrics (**classification accuracy over time**).
    * A continuous feedback loop was established: New documents were collected and labeled; if **data drift** was detected (i.e., performance degraded due to changes in document types), an **automated retraining pipeline** was triggered to update and deploy the refined model version, maintaining long-term system effectiveness.

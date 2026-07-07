# FinSight.AI 🧠

> RAG-powered financial intelligence platform with anomaly detection, built end-to-end by a Senior Data Engineer.

![Python](https://img.shields.io/badge/Python-3.11-blue?style=flat-square&logo=python)
![AWS](https://img.shields.io/badge/AWS-Bedrock-orange?style=flat-square&logo=amazonaws)
![PySpark](https://img.shields.io/badge/PySpark-Delta_Lake-red?style=flat-square&logo=apachespark)
![FastAPI](https://img.shields.io/badge/FastAPI-0.100-green?style=flat-square&logo=fastapi)
![LangChain](https://img.shields.io/badge/LangChain-RAG-purple?style=flat-square)

---

## What it does

FinSight.AI ingests raw financial documents, processes them through a medallion data pipeline, indexes them into a vector store, and serves intelligent Q&A via a RAG (Retrieval-Augmented Generation) interface — with real-time anomaly detection to flag unusual patterns in the data.

**Key capabilities:**
- Ask natural language questions over financial documents and get cited, grounded answers
- Automatic anomaly detection on financial data using a trained PyTorch AutoEncoder
- Risk classification with a weak-supervised ML model (83.3% validation accuracy)
- Risk category scoring across Liquidity, Regulatory, Operational, Market, and Credit Risk
- Secure multi-user access with JWT authentication

---

## Screenshots

### Architecture
![Architecture](assets/screenshots/architecture.png)

### Financial Intelligence Dashboard
![Dashboard](assets/screenshots/dashboard.png)

### Upload Documents
![Upload Documents](assets/screenshots/upload_documents.png)

### Ask Questions (RAG Q&A)
![Ask Questions](assets/screenshots/ask_questions.png)

### Risk & Anomaly Analysis — Input
![Risk Analysis Input](assets/screenshots/risk_analysis_input.png)

### Risk & Anomaly Analysis — Results
![Risk Analysis Results](assets/screenshots/risk_analysis_results.png)

### Risk Analysis Charts
![Risk Analysis Charts](assets/screenshots/risk_analysis_charts.png)

### Risk Category Scores
![Risk Category Scores](assets/screenshots/risk_category_scores.png)

---

## Architecture

```
Raw Documents (PDF, TXT, DOCX, CSV)
     │
     ▼
┌─────────────────────────────────────────────────┐
│         PySpark Medallion Pipeline               │
│  Bronze (raw) → Silver (clean) → Gold (features)│
│              Delta Lake                          │
└─────────────────────────┬───────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
   AWS Bedrock      PyTorch          Risk
   Titan Embeddings AutoEncoder   Classifier
   (1536-dim)       Anomaly Det.  (Weak Labels)
          │
          ▼
   ChromaDB Vector Store
          │
     Two-Stage Retrieval
   ┌──────┴──────┐
   │  Bi-encoder │ → top-k candidates
   │ CrossEncoder│ → reranked results
   └──────┬──────┘
          │
   Claude Haiku (LangChain)
          │
          ▼
   FastAPI + Streamlit
   JWT Auth · EC2 t2.small
   Blue-Green Deployment
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Data Pipeline | PySpark, Delta Lake (Bronze/Silver/Gold) |
| Embeddings | AWS Bedrock Titan (1536-dim) |
| Vector Store | ChromaDB (embedded, cost-free) |
| Retrieval | Bi-encoder + CrossEncoder (ms-marco-MiniLM-L-6-v2) |
| Anomaly Detection | PyTorch AutoEncoder (reconstruction error, mean+2SD threshold) |
| Risk Classification | Scikit-learn, 120 weak-labeled samples, 83.3% val accuracy |
| LLM | Claude Haiku via LangChain |
| Backend | FastAPI, JWT/bcrypt auth |
| Frontend | Streamlit |
| Deployment | AWS EC2 t2.small, Blue-Green deployment |

---

## Key Design Decisions

- **Two-stage retrieval**: Bi-encoder handles speed (top-k recall), CrossEncoder handles precision (reranking) — same pattern used in production search systems
- **Weak labeling for risk classification**: Labeled 120 samples using heuristic rules, achieving 83.3% validation accuracy — practical approach when ground truth labels aren't available
- **Reconstruction error thresholding**: AutoEncoder anomaly score = mean + 2 standard deviations — interpretable and tunable without complex calibration
- **ChromaDB over Pinecone/pgvector**: Runs embedded (no external service), eliminating infrastructure cost for a solo-built portfolio project

---

## Getting Started

```bash
# Clone the repo
git clone https://github.com/sudhakshini/FinSight-AI.git
cd FinSight-AI

# Install dependencies
pip install -r requirements.txt

# Set environment variables
export AWS_ACCESS_KEY_ID=your_key
export AWS_SECRET_ACCESS_KEY=your_secret
export AWS_REGION=us-east-1

# Run the pipeline
python pipeline/run_medallion.py

# Start the API
uvicorn api.main:app --reload

# Launch the UI
streamlit run ui/app.py
```

---

## Production Upgrade Path

| Current | Production |
|---------|-----------|
| ChromaDB (embedded) | Pinecone / pgvector |
| Manual trigger | Apache Airflow DAGs |
| EC2 t2.small | EMR Serverless |
| Local storage | RDS PostgreSQL + pgvector |
| Manual deploy | SageMaker endpoint |

---

## Built by

**Sudhakshini Puntikura** — Senior Data & AI/ML Engineer  
[LinkedIn](https://www.linkedin.com/in/sudhaakshini-puntikura-927s55215/) · [GitHub](https://github.com/sudhakshini) · sudhakshini361@gmail.com

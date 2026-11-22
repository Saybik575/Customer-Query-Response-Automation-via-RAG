# Customer Query & Response Automation via RAG

An end-to-end automated customer support system built using **Retrieval-Augmented Generation (RAG)**. The system receives customer queries, retrieves relevant context from product documentation using vector search, generates accurate grounded responses via an LLM, and logs all interactions for auditing — with zero human intervention.

---

## Architecture

```
Customer Query
      ↓
Chat Trigger (n8n)
      ↓
Vector Store Retrieval — Pinecone (Top-K Chunks)
      ↓
AI Agent (Gemini) — Context-Grounded Answer Generation
      ↓
Log Query + Response → Google Sheets
      ↓
Deliver Final Answer to Customer
```

---

## Tech Stack

| Component | Tool |
|---|---|
| Workflow Orchestration | n8n |
| LLM (Chat Model) | Google Gemini (gemini-2.5-flash) |
| Embedding Model | HuggingFace (`sentence-transformers/distilbert-base-nli-mean-tokens`) |
| Vector Database | Pinecone |
| Document Source | Google Drive |
| Interaction Logging | Google Sheets |
| Text Splitting | Recursive Character Text Splitter (chunk size: 250, overlap: 50) |

---

## Workflows

### Workflow 1 — RAG Ingestion Pipeline

Populates the Pinecone vector store with document embeddings.

1. Monitors a Google Drive folder for file changes
2. Downloads the updated product document
3. Splits document into chunks (size: 250, overlap: 50)
4. Generates vector embeddings via HuggingFace Inference API
5. Stores embeddings in Pinecone index (dimension: 768, metric: cosine)

### Workflow 2 — Query & Response Automation

Handles live customer queries end-to-end.

1. Receives customer query via Chat Trigger
2. AI Agent retrieves Top-4 relevant chunks from Pinecone using semantic search
3. Generates a grounded, context-aware response using retrieved chunks
4. Returns response to the user via Chat
5. Logs Query + Response pair to Google Sheets for auditing

---

## Project Structure

```
├── workflows/
│   ├── workflow_1_ingestion.json
│   └── workflow_2_query_response.json
├── docs/
│   └── product_document.pdf
├── logs/
│   └── sample_query_response_log.csv
├── screenshots/
│   ├── workflow_1.png
│   └── workflow_2.png
└── README.md
```

---

## How to Run

### Prerequisites
- [n8n account](https://n8n.io)
- [Pinecone account](https://www.pinecone.io) — create index with dim: 768, cosine
- [HuggingFace account](https://huggingface.co) — generate access token
- Google account with Drive + Sheets access
- Gemini API key via [Google AI Studio](https://aistudio.google.com)

### Steps

1. Import both workflow JSON files into n8n
2. Configure credentials — Google Drive, Google Sheets, Pinecone, HuggingFace, Gemini
3. Run Workflow 1 once to ingest your document into Pinecone
4. Activate Workflow 2 and open the Chat UI to test

---

## Key Features

- **Zero manual intervention** — fully automated query-to-response pipeline
- **Grounded responses** — answers sourced strictly from documents, reducing hallucinations
- **Interaction logging** — every query and response logged to Google Sheets
- **Fallback handling** — returns "I cannot find the answer" when context is insufficient

---

## Built As Part Of

Accenture AI Infinity (Technical Track) — LearnVantage Program

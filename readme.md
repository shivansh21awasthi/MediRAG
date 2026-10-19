# MediRAG — Privacy-First Clinical AI Assistant

A fully local Retrieval-Augmented Generation (RAG) pipeline that answers
natural-language questions about clinical discharge summaries, grounded in
the actual document text with source citations. No external API calls -
everything runs on your machine.

![Python](https://img.shields.io/badge/Python-3.11-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-1.36-red)
![ChromaDB](https://img.shields.io/badge/ChromaDB-Local-green)
![Ollama](https://img.shields.io/badge/Ollama-llama2-orange)
![Tests](https://img.shields.io/badge/Tests-14%20passing-brightgreen)

---

## Why this project

Most "healthcare AI chatbot" demos are thin LLM wrappers with no grounding -
exactly the failure mode that makes hallucination dangerous in clinical settings.
This project instead:

- Retrieves relevant sections from real clinical notes **before** answering
- Cites which part of the document supported each answer
- Refuses to give medical advice — answers questions about documents, not users
- Supports **PDF upload** - upload any discharge summary and chat with it instantly
- Supports **single-note filtering** - scope queries to one patient instead of searching all
- Runs fully locally - no patient data ever leaves the machine

---

## Architecture

Clinical Notes (.txt / PDF upload)
↓
Section-aware Chunker (chunker.py)
Splits by clinical headers: HPI, Medications, Assessment/Plan, etc.
Preserves patient context in every chunk
↓
Embedding + Vector Store (vectorstore.py)
sentence-transformers (all-MiniLM-L6-v2), local
ChromaDB (local, persistent)
Optional per-note filtering
↓
RAG QA Pipeline (rag_qa.py)
Retrieves top-k relevant chunks
Grounds generation via strict system prompt
Out-of-scope safety guard
Returns answer + cited sources
↓
Streamlit Chat UI (app.py)
PDF upload in sidebar
Note selector dropdown
Chat interface with source citations
Session tracking

---

## Setup

```bash
# Clone the repo
git clone <https://github.com/shivansh21awasthi>
cd MediRAG

# Install dependencies
pip install -r requirements.txt

# Pull Ollama model
ollama pull llama2

# Build the vector index
cd src
python vectorstore.py

# Run the chat UI
streamlit run app.py
# Multimodal RAG System (PDF / PPTX / DOCX)

This project implements a **Retrieval-Augmented Generation (RAG)** pipeline that supports **multimodal inputs**, including text and images. It can ingest documents in **PDF, PPTX, and DOCX** formats, automatically chunk their content, embed them into a vector database, retrieve relevant content using FAISS, and generate answers using an LLM.

A Vision-Language Model (VLM) is integrated to describe images, enabling visual information to be used during retrieval and generation.

---

## Key Features

* Supports **PDF / PPTX / DOCX** ingestion
* Automatic chunking by page / slide / section
* Sentence-Transformer embeddings
* FAISS vector search
* Vision-Language Model (VLM) for image understanding
* LLM-based answer generation (RAG)
* Source attribution
* Modular pipeline design

---

## System Architecture

```
Documents (PDF / PPTX / DOCX)
        ↓
Text Extraction + Image Extraction
        ↓
Chunking (page / slide / section)
        ↓
VLM Captioning (for images)
        ↓
Embedding (SentenceTransformer)
        ↓
FAISS Vector Index
        ↓
Query → Retrieval → Context Building
        ↓
LLM Generation
        ↓
Final Answer + Sources
```

---

## Project Structure

```
.
├── extract_pdf_chunks()
├── extract_pptx_chunks()
├── extract_docx_chunks()
├── build_embedding_text()
├── embed_chunks()
├── build_faiss_index()
├── retrieve()
├── build_context()
├── make_prompt()
├── llm_generate()
├── rag_answer()
└── store.json / index.faiss
```

---

## Installation

Install required packages:

```bash
pip install sentence-transformers faiss-cpu pymupdf python-pptx python-docx transformers accelerate
```

---

## Usage

### 1. Upload Documents

Supported formats:

* `.pdf`
* `.pptx`
* `.docx`

---

### 2. Extract Chunks

Each document is processed by its own pipeline:

* PDF → page-level chunks
* PPTX → slide-level chunks
* DOCX → section/paragraph-level chunks

Each chunk contains:

```python
{
  "raw_text": ...,
  "image_caption": ...,
  "type": "pdf/pptx/docx",
  "page/slide/section": ...,
  "source_file": ...
}
```

---

### 3. VLM Integration

Images are extracted and passed to a Vision-Language Model to generate captions. These captions are appended to the chunk text to enable multimodal retrieval.

Example:

```
[IMAGE]
A block diagram illustrating the IR pipeline
```

---

### 4. Build Embeddings

We use:

```python
SentenceTransformer("all-MiniLM-L6-v2")
```

All chunks are converted into vector embeddings and stored in a FAISS index.

---

### 5. Retrieval

Given a user query, we retrieve the top-k most relevant chunks using FAISS.

---

### 6. Answer Generation

We construct a prompt using:

* User question
* Retrieved chunks
* Source metadata

Then pass it to an LLM (e.g., Qwen2.5) for generation.

---

### Example

```python
ans, hits = rag_answer("What does Lecture 6 talk about? Please summarize.")
print(ans)
```

---

## Prompt Design

The LLM is instructed to:

* Only use the provided context
* Not hallucinate
* Explicitly say when information is missing
* Output sources in a fixed format

---

## Output Format

The final answer includes:

1. Natural language response
2. Sources

Example:

```
Answer:
...

Sources:
- Lecture6.pdf | pdf | p12
- Lecture6.pdf | pdf | p15
```

---

## Reliability Design

This system is designed to:

* Prevent hallucinations
* Enforce grounding
* Provide traceable sources
* Support multimodal reasoning

---

## Future Work

* Add OCR support
* Add table parsing
* Add reranking models
* Add web UI
* Add agent workflows

---

## Author

Built as a full-stack multimodal RAG system for learning and research purposes.

---

If you have questions or want to extend this system, feel free to ask!

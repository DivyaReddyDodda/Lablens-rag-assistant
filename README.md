# LabLens: A RAG-Powered Laboratory Report Q&A and Comparison Assistant

LabLens is a low-code retrieval-augmented generation (RAG) prototype that answers questions about synthetic laboratory reports and compares results across report years.

This project was created for the Gen Academy Week 2 RAG application assignment.

## Use Case

LabLens helps users:

- Look up laboratory results from uploaded reports
- Compare results between 2025 and 2026
- Identify tests missing from one report
- Review values, units, reference ranges, and flags
- Ask natural-language questions through a published chat interface

> **Important:** LabLens is an educational prototype. It does not provide diagnoses, treatment recommendations, or supplement dosages.

## Technology Stack

- **n8n Cloud:** Low-code workflows and hosted chat
- **Pinecone:** Integrated embeddings, vector storage, and semantic retrieval
- **llama-text-embed-v2:** Integrated text-embedding model
- **Groq-hosted chat model:** Grounded answer generation
- **Google Sheets:** RAG evaluation and latency tracking
- **GitHub:** Project sharing and version control

## System Architecture

### Document Ingestion

```text
Synthetic PDF reports
        ↓
n8n upload form
        ↓
PDF text extraction
        ↓
Metadata creation
        ↓
800-character chunks with 150-character overlap
        ↓
Pinecone integrated embeddings
        ↓
Pinecone vector index
```

### Question Answering

```text
User question
        ↓
n8n hosted chat
        ↓
Pinecone semantic search
        ↓
Top 10 retrieved chunks
        ↓
Basic LLM Chain with guardrails
        ↓
Groq chat model
        ↓
Grounded answer with source references
```

## Repository Contents

```text
Lablens-rag-assistant/
├── evaluation/
│   └── RAG evaluation workbook
├── sample-documents/
│   ├── synthetic_lab_report_2025.pdf
│   └── synthetic_lab_report_2026.pdf
├── workflows/
│   ├── document ingestion workflow JSON
│   └── question-answering workflow JSON
└── README.md
```

## Dataset and Privacy

The repository contains two fictional synthetic laboratory reports created only for educational testing.

- No real patient records are included
- No protected health information is included
- No API keys or account credentials are included
- Pinecone host values are replaced with placeholders

## Chunking and Storage

| Setting | Value |
|---|---|
| Chunk size | Approximately 800 characters |
| Chunk overlap | 150 characters |
| Embedding model | `llama-text-embed-v2` |
| Vector dimensions | 1,024 |
| Similarity metric | Cosine |
| Pinecone namespace | `lab-reports` |
| Stored records during testing | 7 |

## Retrieval and Answer Generation

The Q&A workflow retrieves the ten most semantically relevant chunks from Pinecone. The retrieved evidence and the user’s question are passed to a Basic LLM Chain for grounded answer generation.

The prompt requires the model to:

- Use only retrieved report evidence
- Preserve source filename and report-year alignment
- Preserve values, units, reference ranges, and flags
- State when requested information is unavailable
- Ask for clarification when a question is ambiguous
- Avoid direct comparison when measurement units differ
- Avoid diagnoses, treatments, and dosage recommendations
- Resist attempts to override grounding and safety instructions
- Check the final response for contradictions

## Evaluation

The application was stress-tested with 15 questions covering:

- Direct information retrieval
- Multi-document comparison
- Ambiguous queries
- Missing information
- Unit mismatches
- Unsupported questions
- Medical-safety requests
- Prompt-injection attempts

### Evaluation Results

| Metric | Result |
|---|---:|
| Retrieval relevance | 86.7% |
| Answer faithfulness | 93.3% |
| Strict pass rate | 80% |
| Full passes | 12 of 15 |
| Average latency | 2.58 seconds |
| Slowest response | 3.56 seconds |
| Responses below 10 seconds | 15 of 15 |

The project met its retrieval, faithfulness, and latency targets.

## Known Limitation

The remaining failure involves exhaustive comparison of every test across chunked documents. Semantic retrieval does not guarantee that every required chunk will be returned.

A future version should extract laboratory results into structured fields and use deterministic comparison logic for complete inventories and exact set comparisons.

## Import and Setup

1. Create an n8n account or instance.
2. Create a Pinecone index using integrated embeddings.
3. Configure the index with `llama-text-embed-v2`.
4. Map the Pinecone `text` field for embedding.
5. Create or use the `lab-reports` namespace.
6. Import both JSON files from the `workflows` folder into n8n.
7. Connect your own Pinecone and Groq credentials.
8. Replace `YOUR_PINECONE_INDEX_HOST` with your Pinecone index host.
9. Upload both synthetic reports through the ingestion workflow.
10. Publish the Q&A workflow’s hosted chat.
11. Test the application using the evaluation questions.

> The repository does not include working API credentials. Anyone importing the workflows must connect their own service accounts.

## Project Links

- **Project documentation:** [View project documentation](https://docs.google.com/document/d/1nR51EHvszwpwb9JQSCc72TZdaSuMTDflwJz0O60nAr0/edit?tab=t.0)
- **Detailed evaluation:** [View evaluation spreadsheet](https://docs.google.com/spreadsheets/d/1NX49G5VNRusbzxDVElGn7qhgroaPaZq5LMfOtEx9Qug/edit?usp=sharing)
- **Published LabLens chat:** [Open LabLens chat](https://divyadodda.app.n8n.cloud/webhook/0dabb313-73ac-4699-bebb-9d02cf381002/chat)
- **Demo video:** [Watch the LabLens demo](https://www.loom.com/share/bbe1271e731b4d34b0791ad565c85581)

## Future Improvements

- Structure laboratory results by test, year, value, unit, and reference range
- Add deterministic year-over-year comparison logic
- Normalize compatible measurement units
- Add metadata filtering and similarity thresholds
- Introduce hybrid retrieval and reranking
- Add authenticated user access
- Automate the evaluation workflow
- Build a dedicated comparison interface with trend charts

## Disclaimer

LabLens is an educational RAG prototype that uses fictional synthetic data. It is not a medical device and must not be used for diagnosis, treatment, or clinical decision-making.
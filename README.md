# KnowFlow AI

**A local-first AI assistant for exploring FastAPI documentation.**

KnowFlow AI helps developers find explanations and Python examples in the official
FastAPI documentation. It combines hybrid search with a lightweight local language
model to return documentation excerpts and section-level citations. When evidence
is insufficient or an output fails validation, it responds with
`Not found in the docs`.

## Features

- **Hybrid retrieval:** combines semantic search and BM25 keyword search using
  Reciprocal Rank Fusion.
- **Code-aware ingestion:** resolves examples included from the FastAPI source
  repository, preserving their documentation context.
- **Evidence-based responses:** selects existing explanations and code examples,
  with citation and Python syntax checks.
- **Local interface:** provides a Gradio application with CPU-based model
  inference and optional feedback stored locally in SQLite.
- **Training workflow:** includes reviewed dataset preparation, evaluation tools,
  and a separate cloud QLoRA notebook.

## How It Works

```text
Official documentation -> Code-aware chunks -> Semantic + keyword indexes
User question -> Hybrid retrieval -> Evidence checks -> Local model selection
              -> Validated excerpt and citation, or refusal
```

The current application selects documentation excerpts; it does not generate
new code. Copying source material and validating citations do not guarantee that
an answer is relevant, and valid questions may still be refused.

**Core stack:** Python 3.11, Gradio, ChromaDB, BM25, BGE embeddings,
Qwen2.5-1.5B-Instruct (Q4_K_M), and llama-cpp-python.

## Quick Start

Use an activated **Python 3.11 virtual environment** in the project root.
The local environment was tested with Python 3.11.9 on Windows.

Install dependencies and prepare the model, documentation index, and evidence
calibration. Initial setup requires internet access and downloads model weights
and public documentation.

```bash
python -m pip install --only-binary=:all: -r requirements.txt -r requirements-gguf.txt
python -m src.models
python -m src.ingest --download
python -m src.retriever --build
python -m eval.calibrate --build
```

Launch the base-model application from **Git Bash**:

```bash
USE_FINETUNED_MODEL=false GRADIO_SERVER_NAME=127.0.0.1 PORT=7861 GRADIO_ANALYTICS_ENABLED=False python -m app.app
```

Open **http://127.0.0.1:7861**. Try:

> How do I declare a reusable dependency in FastAPI?

Local question answering does not require an API key or a paid inference service.
Model weights, generated datasets, and local indexes are excluded from Git.
See the [setup and training checklist](MANUAL_CHECKLIST.md) for detailed procedures.

## Development Status

The local application is implemented. As of November 17, 2026:

- The compatible dataset contains **161 AI-reviewed examples**: 154 training
  and 7 validation examples. These are not human-approved annotations.
- A small Kaggle QLoRA smoke run completed; full fine-tuning remains pending.
- Full answer-quality evaluation is incomplete. No improvement from fine-tuning
  is claimed.

## Project Structure

| Directory | Purpose |
|---|---|
| `app/` | Gradio interface and local feedback |
| `src/` | Ingestion, retrieval, model inference, and validation |
| `training/` | Dataset generation and QLoRA workflow |
| `eval/` | Calibration, benchmarks, and evaluation reports |
| `tests/` | Automated tests |
| `scripts/` | Packaging and export utilities |

## Further Reading

- [Test report](TEST_REPORT.md): verification results and remaining work.
- [Excerpt evaluation](eval/results_extractive.md): measured coverage and citation results.

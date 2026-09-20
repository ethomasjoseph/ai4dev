# Advanced RAG Engineering: Production Pipelines, Multimodal Architectures, Two-Stage Funnels, and Structured Outputs

Retrieval-Augmented Generation (RAG) decouples world knowledge from parametric model weights, transforming a large language model from an ungrounded, hallucination-prone oracle into a deterministic reasoning and synthesis engine. In enterprise production environments, building a reliable RAG pipeline requires addressing real-world edge cases: layout collapse, silent sequence truncation, dense-retrieval keyword blindness, irrelevant context injection, and unstructured response drift.

---

## Executive Summary

Standard enterprise documents are not structured for direct LLM consumption. Ingesting raw PDFs, audio recordings, scanned receipts, and multi-column research papers directly into an LLM context window triggers cognitive noise, layout confusion, and hallucination. Fine-tuning models cannot solve this factual grounding problem cost-effectively, as model weights modify behavioral style and syntax rather than providing reliable, real-time data storage.

```
                     +---------------------------------------------+
                     |        Disparate Unstructured Data          |
                     | (PDFs, Invoices, Audio, Video, CSV, Code)   |
                     +----------------------+----------------------+
                                            |
                                            v
                     +---------------------------------------------+
                     |        1. Ingestion & Normalization         |
                     |  Layout Parsing | Whisper | PyTesseract/Donut|
                     +----------------------+----------------------+
                                            |
                                            v
                     +---------------------------------------------+
                     |    2. Chunking & StorageContext Layout      |
                     |  Sentence / AST Splitters (512-Token Limit) |
                     | [VectorStore | DocStore | Index | GraphStore]|
                     +----------------------+----------------------+
                                            |
                                            v
User Query --------> [ Query Transformation: HyDE / Decomposition ]
                                            |
                                            v
+------------------------------------------------------------------+
|           3. Two-Stage Retrieval Funnel (High Recall)            |
| Dense Top-K (bge-small)  +  Sparse Lexical (BM25 Keyword Match)  |
|            -> Reciprocal Rank Fusion (RRF) (Top 50-100)          |
+-----------------------------------+------------------------------+
                                    |
                                    v
+------------------------------------------------------------------+
|          4. Post-Processing & Verification (High Precision)       |
|    Similarity Cutoff Filtering (Discard Chunks < Threshold)      |
|         Cross-Encoder Reranking (Joint Attention Top-K)          |
+-----------------------------------+------------------------------+
                                    |
                                    v
+------------------------------------------------------------------+
|              5. Synthesis & Deterministic Formatting             |
|   Agentic Self-Correction Loop (LangGraph State Machine)         |
|      Strict JSON Schemas Enforced via Pydantic Validation        |
|      Continuous Evaluation via Independent RAGAS Harness         |
+------------------------------------------------------------------+

```

A production RAG architecture addresses baseline failure modes through seven integrated layers:

1. **Deterministic Environment & Accelerated Setup:** Eliminating local dependency conflicts via cloud workspaces (Google Colab), accelerating installation overhead via the `uv` package manager, and isolating API credentials in encrypted secret managers.

2. **Layout-Aware Parsing & Strict Sequence Chunking:** Overcoming coordinate-based PDF fragmentation through layout parsers, respecting hard embedding limits (e.g., 512 tokens for `bge-small`) to prevent silent data truncation, and preserving boundary context using a 10% to 20% overlap strategy.

3. **Multimodal Ingestion via Textual Normalization:** Standardizing visual assets (via OCR engines like `PyTesseract` or image-captioning models like `Donut`), spoken media (via `Whisper`), and tabular files into clean, sequential Markdown strings ingested by `SimpleDirectoryReader`.

4. **Structured Storage Architecture:** Organizing parsed knowledge across LlamaIndex’s `StorageContext`, segregating embeddings (Vector Store), raw text (Document Store), metadata lookups (Index Store), and hierarchical tree relationships (Graph Store).

5. **Two-Stage Funnel Optimization:** Merging dense vector search and sparse BM25 keyword matching via Reciprocal Rank Fusion (RRF) for high recall (pulling top 50–100 chunks), followed by strict programmatic similarity cutoff filtering and Cross-Encoder reranking for high precision.

6. **Query Transformation & Agentic Loops:** Bridging the human-to-documentation vernacular gap via Hypothetical Document Embeddings (HyDE), decomposing multi-part prompts into parallel sub-questions, and coordinating cyclic retrieval-evaluation state machines in LangGraph.

7. **Pydantic Schema Enforcement:** Enforcing rigid JSON output schemas via Pydantic `BaseModel` classes to convert unstructured natural language into typed payloads required by external APIs and production databases.

---

## Tools Required & Technical Environment

Building this architecture requires tools spanning ingestion, indexing, retrieval, reranking, generation, evaluation, and user interfaces:

| Component Category | Technology Selected | Architectural Purpose | Production Alternative |
| --- | --- | --- | --- |
| **Package Management** | **uv** | High-performance Rust-based package manager drastically reducing dependency install latency. | `pip`, `poetry`, `conda` |
| **Runtime Environment** | **Google Colab** | Cloud-hosted execution environment bypassing local OS/VS Code C++ build tool and driver conflicts.

 | Dockerized AWS EC2, GCP Vertex AI Workbench |
| **Document Orchestration** | **LlamaIndex** (`llama-index-core`)

 | Document loading, node parsing, chunk splitting, StorageContext abstraction, and index assembly.

 | LangChain, Haystack, Bespoke Custom Parsers

 |
| **Vector Storage** | **LanceDB**<br> | Embedded, serverless columnar vector database executing sub-millisecond local or in-memory vector lookups.

 | Qdrant, Pinecone, Weaviate, Milvus

 |
| **Dense Embedding Model** | **BAAI/bge-small-en-v1.5**<br> | 384-dimensional dense semantic embedding model (512-token sequence limit) running locally on CPU/GPU.

 | OpenAI `text-embedding-3-small`, Cohere Embed v3

 |
| **Lexical / Sparse Search** | **BM25** (`rank_bm25`)

 | Frequency-based keyword retrieval engine for exact matches on technical flags, error codes, and IDs.

 | Elasticsearch, OpenSearch, Apache Lucene

 |
| **Reranking Engine** | **Cohere Rerank** / **BGE-Reranker**<br> | Cross-encoder architecture computing joint query-document attention scores to resolve bi-encoder ranking errors.

 | ColBERT, Qwen-2.5 Reranker

 |
| **Schema Validation** | **Pydantic** (`v2`) | Rigid Python data validation library enforcing structural schemas and strict data types on LLM outputs. | Instructor, JSON Schema |
| **Evaluation Harness** | **RAGAS** (`ragas`)

 | Automated testing suite evaluating Context Relevance, Faithfulness, and Answer Relevance.

 | DeepEval, TruLens, Phoenix |
| **Agentic State Engine** | **LangGraph**<br> | Directed cyclic graph orchestrator managing multi-turn retrieval-evaluation-reformulation feedback loops.

 | LlamaIndex Workflows, AutoGen |
| **API Gateway & Routing** | **OpenRouter API**<br> | Unified gateway routing generation, planning, and evaluation dynamically across frontier model providers.

 | LiteLLM, Portkey |
| **Task Models** | **Claude 3.5 Haiku / Sonnet** & **Gemini Flash**<br> | Haiku for fast synthesis/context blurbs; Sonnet for planning/decomposition; Gemini Flash as an independent judge.

 | GPT-4o, Llama-3.3-70B-Instruct, Mistral Small

 |
| **Multimodal Parsers** | **PyMuPDF**, **PyTesseract**, **Whisper**, **Donut**<br> | Layout-aware PDF text extraction, OCR for document images, audio transcription, and visual image captioning.

 | Surya OCR, LlamaParse, Mooncake, PaddleOCR

 |
| **Prototyping Interface** | **Gradio** (`gr.Blocks`) | Interactive UI framework providing threshold sliders, top-$k$ adjustments, and source provenance inspection. | Streamlit, Next.js (Production Frontend) |

### Environment Setup & Dependency Installation

To avoid local environment configuration failures (such as missing C++ compilers, wheel-building crashes, or CUDA mismatches in local VS Code instances), Google Colab provides a uniform, sandboxed runtime.

Dependencies are installed using `uv` to minimize setup time:

```bash
# Install uv for rapid package management
!pip install uv

# Install core RAG framework, vector storage, and multimodal dependencies
!uv pip install --system \
    llama-index-core \
    llama-index-embeddings-huggingface \
    llama-index-llms-openrouter \
    llama-index-vector-stores-lancedb \
    llama-index-postprocessor-cohere-rerank \
    lancedb \
    pyarrow \
    pydantic \
    rank-bm25 \
    ragas \
    pymupdf \
    pytesseract \
    openai-whisper \
    gradio

```

API credentials should never be committed into source code or hardcoded in notebooks. In Google Colab, store keys using the native **Secrets** tab (the key icon) with an entry for `OPENROUTER_API_KEY`, granting notebook access securely:

```python
import os
from google.colab import userdata

# Safely extract credentials from encrypted environment secrets
os.environ["OPENROUTER_API_KEY"] = userdata.get("OPENROUTER_API_KEY")

```

---

## 1. The Core Mechanics of Ingestion & Storage Engineering

An enterprise knowledge base contains fragmented, unstructured data. Downstream retrieval models cannot recover information that is corrupted, truncated, or severed during ingestion.

### 1.1 Parsing and Layout Analysis

PDFs are canvas-based rendering formats that encode glyphs at distinct $(x, y)$ coordinate positions. Standard programmatic text extraction flattens this canvas linearly, which introduces several failure modes:

* **Column Bleed:** In two-column research papers, parsers often read horizontally across both columns rather than following a single column down the page.

* **Table Scrambling:** Two-dimensional row-column relationships are flattened into unstructured strings, detaching cells from their respective column headers.

* **Visual Clutter:** Running headers, footers, and floating image captions interleave the primary body text, diluting semantic density.

Production pipelines employ layout-aware parsers (`PyMuPDF`, `pdfplumber`, or document vision models like `Donut` and `Surya OCR`) to reconstruct the original document layout into structured Markdown headings (`#`, `##`) and pipe-delimited tables before chunking.

### 1.2 Chunking Constraints & Silent Truncation

Chunking is dictated by strict computational constraints:

1. **Embedding Model Sequence Ceilings:** Models like `bge-small-en-v1.5` have a hard context ceiling of 512 tokens. If an unchunked 1,024-token document is passed to this model, it will not throw an error; it will silently truncate the text at token 512, permanently destroying the remaining content.

2. **Context Dilution:** Embedding a 50-page document into a single vector averages all its topics into one mathematical point, wiping out localized nuances.

3. **Cognitive Noise & Needle-in-a-Haystack Degradation:** Flooding an LLM's context window with thousands of irrelevant tokens degrades attention mechanics, making it difficult for the model to isolate specific facts.

```
Chunking Strategy Comparison:
+-------------------+-----------------------------------------+------------------------------------------+
| Splitter Type     | Operational Mechanism                   | Production Failure Risk                  |
+-------------------+-----------------------------------------+------------------------------------------+
| Token / Character | Slices text at fixed character steps    | Slices mid-sentence, mid-word, or        |
| Splitter          | (e.g., exactly every 512 tokens).       | mid-variable; severs context[cite: 3].  |
+-------------------+-----------------------------------------+------------------------------------------+
| Sentence Splitter | Walks boundaries using natural          | Preserves thoughts cleanly; ideal        |
| (LlamaIndex)      | punctuation (. ? ! \n\n) up to limit.   | for prose and technical manuals[cite: 3]|
+-------------------+-----------------------------------------+------------------------------------------+
| AST Splitter      | Uses Abstract Syntax Tree grammar to    | Keeps functions/classes intact; prevents |
| (Codebases)       | isolate complete programmatic blocks.   | breaking code logic across chunks[cite: 1, 3].|
+-------------------+-----------------------------------------+------------------------------------------+

```

### 1.3 The Overlap Heuristic

Dividing continuous documents severs contextual connections across split points. To preserve continuity, use an **overlap window of 10% to 20%** of the chunk size (e.g., 50–60 tokens for a 512-token chunk).

Overlaps exceeding 25% introduce data redundancy, bloat vector database index sizes, increase infrastructure costs, and cause the LLM to process repetitive context.

### 1.4 Mathematical Dimensionality & The Vector Invariant

An embedding model converts text into an $N$-dimensional vector ($V \in \mathbb{R}^N$).

> **The "Ananya" Analogy:** Describing an individual named "Ananya" using only three traits—age 26, black hair, height 5'4"—leaves her identity ambiguous among thousands of people. Adding hundreds of granular features (profession, university, neighborhood, musical taste) makes her uniquely identifiable.
>
>

Embeddings operate on the same mathematical principle:

* **384 Dimensions (`bge-small-en-v1.5`):** Fast, lightweight inference with lower RAM and storage overhead.

* **1,536 / 3,072 Dimensions (OpenAI `text-embedding-3-small/large`):** Captures deeper semantic relationships at the cost of higher storage footprints and database hosting expenses.

$$\text{Cosine Similarity}(q, d) = \frac{\sum_{i=1}^N q_i d_i}{\sqrt{\sum_{i=1}^N q_i^2} \sqrt{\sum_{i=1}^N d_i^2}}$$

*The Vector Invariant Rule:* Similarity calculations rely on exact matrix dot products. Generating database embeddings in a 384-dimensional space and attempting to query them using a 1,536-dimensional vector is mathematically impossible and will trigger a matrix dimension mismatch error. The identical model must be used for both ingestion and query embedding.

### 1.5 Vector Databases vs. Flat File Lookups

Storing embeddings in flat JSON files requires brute-force sequential scans ($\mathcal{O}(N)$ algorithmic complexity). Calculating similarities across 100,000 chunks sequentially can take up to 15 minutes per query on standard hardware.

Vector databases (like **LanceDB**) implement Approximate Nearest Neighbor (ANN) indexing structures. Similar to jumping straight to a letter in an alphabetical directory, ANN indexing reduces the search space hierarchically, dropping retrieval latency from minutes to milliseconds ($\mathcal{O}(\log N)$).

### 1.6 Deconstructing LlamaIndex StorageContext

LlamaIndex manages parsed data through the `StorageContext` abstraction, which coordinates four distinct storage layers:

```
                            StorageContext Architecture
                                         |
     +-------------------+---------------+-------------------+-------------------+
     |                   |                                   |                   |
     v                   v                                   v                   v
[ Vector Store ]    [ Document Store ]                  [ Index Store ]     [ Graph Store ]
Houses pure float   Houses raw, readable                Manages structural  Maintains parent-child
vectors for fast    text strings matched to             metadata and rapid  hierarchical trees and
ANN search (LanceDB)Node IDs (DocStore)                 lookup directories  knowledge graphs

```

1. **Vector Store:** Holds the raw mathematical vector arrays and handles ANN index math (e.g., `LanceDBVectorStore`).
2. **Document Store:** Houses the source textual payloads and node objects mapped to their respective IDs (`docstore.json`).
3. **Index Store:** Contains the structural index metadata dictating how nodes relate within the search strategy (`index_store.json`).
4. **Graph Store:** Specialized layer that maintains parent-child structural relationships during hierarchical chunking, allowing the system to map fine-grained leaf chunks back to broader parent sections.

---

## 2. Multimodal RAG: Normalizing Disparate Assets

Enterprise data spans multiple formats: documents, images, audio, video, and spreadsheets. Because dense vector math requires consistent dimensionality across inputs, multimodal RAG normalizes all non-text assets into clean, structured Markdown text at the ingestion boundary.

```
                   Disparate Ingestion Media
                               |
     +------------+------------+------------+------------+
     |            |                         |            |
     v            v                         v            v
Scanned Invoices Natural Photos          Audio/Video  Spreadsheets
(Embedded Text)  (Scene/Object)          (.mp3, .mp4) (CSV, Excel)
     |            |                         |            |
     v [OCR]      v [Captioning]            v [Whisper]  v [Pandas/MD]
PyTesseract      Donut / Vision Model      Transcribe   Tabular Markdown
     |            |                         |            |
     +------------+------------+------------+------------+
                               |
                               v
               Unified Markdown Text Substrate
             (Embedded into Single Vector Store)

```

### 2.1 The Ingestion Mechanics of `SimpleDirectoryReader`

LlamaIndex’s `SimpleDirectoryReader` operates as an automated router. It inspects incoming file extensions (`.pdf`, `.png`, `.mp3`, `.csv`) and assigns them to specialized parsers:

* **Visual Assets with Embedded Text (Invoices, Receipts, IDs):** Routed to `PyTesseract` or `Surya OCR` to extract textual layout data.

* **Purely Visual Assets (Diagrams, Photographs, System Flows):** Routed to vision models or the `Donut` vision-language model to generate detailed descriptions of visual relationships.

* **Spoken Audio & Video Media (`.mp3`, `.mp4`):** Routed to OpenAI's open-source `Whisper` model to generate time-stamped text transcripts.

* **Tabular Datasets (`.csv`, `.xlsx`):** Converted into Markdown-formatted tables to preserve row-column context.

```python
from pathlib import Path
from llama_index.core import SimpleDirectoryReader

def load_multimodal_corpus(data_dir: Path):
    """
    Recursively scans and processes mixed multimodal files
    using SimpleDirectoryReader's automated routing.
    """
    reader = SimpleDirectoryReader(
        input_dir=str(data_dir),
        recursive=True,
        required_exts=[".pdf", ".png", ".jpg", ".mp3", ".csv", ".md"]
    )
    documents = reader.load_data()
    return documents

```

> **Image Chunking Rule:** Never chunk raw image files. The only exception is when an OCR pass on a dense document image yields an extracted text string that exceeds the embedding model's sequence ceiling (e.g., > 512 tokens). In that scenario, process the extracted text through standard sentence splitting.
>
>

---

## 3. Two-Stage Retrieval Funnels & Post-Processing Optimization

Setting a vector database to retrieve a static `similarity_top_k=5` causes systemic noise. If the answer to a query exists in only one chunk, the remaining four slots are filled with low-scoring passages, increasing the risk of hallucination.

```
Raw Knowledge Base (100,000 Chunks)
        |
        |  Stage 1: Fast Candidate Retrieval (Bi-Encoder Dense + BM25)
        |  Target: High Recall (Top 50-100 Chunks)[cite: 3]
        v
Candidate Pool (100 Chunks)
        |
        |  Stage 2A: Similarity Threshold Filtering (e.g., Score < 0.30 Discarded)
        v
Filtered Chunks (12 Chunks)
        |
        |  Stage 2B: Cross-Encoder Reranker (Joint Query-Chunk Attention)
        |  Target: High Precision (Top 3-5 Rescored Chunks)[cite: 3]
        v
Final Context Passed to LLM Context Window (3 Chunks)

```

### 3.1 Similarity Cutoff Filtering

A post-processor acts as a programmatic validation gate between the vector database and the LLM. It intercepts retrieved nodes and discards any candidate whose similarity score falls below an empirical threshold (e.g., `< 0.30`), ensuring that low-confidence context is filtered out before prompt generation.

### 3.2 The High-Recall / High-Precision Funnel

To balance speed and retrieval quality, production systems use a two-stage retrieval funnel:

1. **Stage 1 (High Recall):** Retrieve 50 to 100 candidate chunks using fast Bi-Encoder embeddings and BM25 sparse matching. Bi-encoders score queries and documents independently, providing the speed needed to search across millions of records.

2. **Stage 2 (High Precision):** Pass the 50–100 candidates to a Cross-Encoder reranker. Cross-encoders concatenate the query string directly with each candidate chunk (`Query + Chunk`) and run joint cross-attention across all tokens simultaneously. This yields an accurate relevance score that corrects Bi-Encoder ranking errors, pushing the most pertinent chunks to the top of the context window.

---

## 4. The Evolutionary Progression: M0 to M5 Architectures

RAG architectures should be optimized incrementally, using an automated evaluation harness to validate each change.

```
M0: Naive Baseline (Dense Vector Only)
  |--> Faithfulness: 0.897 | Answer Relevance: 0.646[cite: 2]
  |
  v  [Fix: Add BM25 for exact parameter/flag matching][cite: 2]
M2: Hybrid Retrieval (Dense Vector + BM25 Lexical + RRF Rerank)
  |--> Faithfulness: 0.975 (+8.7%) | Answer Relevance: 0.839 (+29.8%)[cite: 2]
  |
  v  [Fix: Prepend situational context to isolated chunks][cite: 1, 2]
M3: Contextual Retrieval (LLM-Generated Pre-Indexing Blurbs)
  |--> Faithfulness: 0.927 (-4.9%) | Answer Relevance: 0.742 (-11.5%)[cite: 2]
  |    *Caution: Prompt boilerplate can dilute localized dense vector math*[cite: 2]
  |
  v  [Fix: Align user phrasing with documentation vocabulary][cite: 2]
M4: Query Transformation (HyDE + Sonnet Decomposition)
  |--> Faithfulness: 0.980 (+0.5%) | Answer Relevance: 0.690 (-17.7%)[cite: 2]
  |    *Maximizes factual accuracy; requires tuning for brevity*[cite: 2]
  |
  v  [Fix: Handle ambiguous queries via autonomous self-reflection][cite: 1, 2]
M5: Agentic RAG (LangGraph Corrective Loop)
  |--> Multi-turn reflection and reformulation (Max Iterations = 3)[cite: 2]

```

### 4.1 M0: Naive Baseline Vector RAG

The baseline pipeline embeds parsed chunks using `bge-small-en-v1.5`, stores them in LanceDB, retrieves the top-$5$ nearest neighbors, and synthesizes answers via an LLM.

* *Performance:* Faithfulness: `0.897` | Answer Relevance: `0.646`

* *Limitation:* When queried for exact configuration flags (e.g., `--max-pods`), semantic embeddings return generic sections on pod management, missing the parameter's definition entirely.

### 4.2 M2: Hybrid Retrieval via Reciprocal Rank Fusion (Dense + BM25)

Dense vectors capture broad semantic meaning, while sparse lexical search (BM25) isolates exact alphanumeric keywords, IDs, and parameters.

Hybrid retrieval runs both passes concurrently and fuses their rankings using Reciprocal Rank Fusion (RRF):

$$\text{RRF Score}(d) = \sum_{m \in \{\text{Dense}, \text{BM25}\}} \frac{1}{60 + \text{Rank}_m(d)}$$

* *Performance:* Faithfulness: `0.975` (+8.7%) | Answer Relevance: `0.839` (+29.8%)

* Hybrid retrieval delivers the largest single quality improvement in the pipeline.

### 4.3 M3: Contextual Ingestion

Standard chunking breaks context boundaries. Contextual ingestion passes each chunk alongside its broader parent document through a fast LLM (`Claude 3.5 Haiku`), which generates a 1–2 sentence contextual summary that is prepended to the chunk before vectorization.

* *Performance:* Faithfulness: `0.927` | Answer Relevance: `0.742`

* *Analysis:* On technical documentation, poorly tuned summary prompts can introduce repetitive metadata across chunks, which compresses vector distances and pulls irrelevant passages into the top-$k$. This strategy works well on narrative text, but requires careful tuning on technical material.

### 4.4 M4: Query Transformation (HyDE & Decomposition)

User queries often use conversational phrasing (e.g., *"Why did my server die?"*), whereas technical documentation uses formal terminology (e.g., *"Out-Of-Memory (OOMKilled) Container Eviction Lifecycle"*).

* **Hypothetical Document Embeddings (HyDE):** Prompts an LLM to draft a hypothetical answer to the user's question. Vectorizing this hypothetical response aligns the search vector with the target documentation's vocabulary far better than the raw query.

* **Query Decomposition:** Complex queries are analyzed by a planner (`Claude 3.5 Sonnet`) and broken down into 2–4 targeted sub-questions that are searched in parallel.

* *Performance:* Faithfulness: `0.980` (highest accuracy achieved) | Answer Relevance: `0.690`.

### 4.5 M5: Corrective Agentic RAG (LangGraph)

Static RAG runs once without checking its own output. Agentic RAG converts this into an iterative state machine:

```
User Query ---> [ Hybrid Retrieval ] ---> [ LLM Evaluation Judge ]
                                                   |
                   +-------------------------------+
                   |
        [ Context Sufficient? ]
          /                 \
    (Yes)/                   \(No)
        v                     v
[ Final Synthesis ]   [ Check Iteration Count ]
        |               |                 |
     ( End )     (Count < 3)        (Count >= 3)
                        |                 |
                        v                 v
              [ Reformulate Query ] [ Best-Effort Synthesis ]
                        |                 |
                        +-> [ Re-Retrieve] ( End )

```

1. **Candidate Retrieval:** Runs hybrid search for the input query.

2. **Reflection & Evaluation:** An internal evaluator LLM checks whether the retrieved passages are sufficient to answer the prompt.

3. **Loop & Reformulation:** If the context is insufficient, the model reformulates the query with alternate terminology and re-queries the vector store (capped at 3 iterations to avoid runaway latency).

---

## 5. Enforcing Determinism via Pydantic Structured Outputs

Conversational, free-form text output fails when downstream systems require structured payloads to trigger database updates or external APIs.

```
Unstructured Prompt Response (Fragile):
"Sure! I found that the event is an AI Summit happening in San Francisco on 
October 12th with 450 attendees expected. Tickets are $199.99."
--> Cannot be safely parsed with Regex; key names vary on every generation.

Pydantic Structured JSON Payload (Deterministic):
{
  "event_name": "AI Summit",
  "city": "San Francisco",
  "date": "2026-10-12",
  "expected_attendees": 450,
  "ticket_price": 199.99
}
--> Conforms strictly to schema types: str, int, float; ready for API consumption.

```

### Implementing Strict Pydantic Schemas

Using Python's `pydantic` library, developers define data schemas by inheriting from `BaseModel`. These models define explicit variable keys, mandatory fields, and strict types (`str`, `int`, `float`, `list`):

```python
from pydantic import BaseModel, Field
from typing import List

class RecipeIngredient(BaseModel):
    item: str = Field(description="Name of the ingredient")
    quantity: str = Field(description="Quantity or measurement (e.g., 200g, 2 tbsp)")

class StructuredRecipeOutput(BaseModel):
    recipe_name: str = Field(description="Title of the dish")
    prep_time_minutes: int = Field(description="Preparation time in minutes")
    cook_time_minutes: int = Field(description="Cooking time in minutes")
    ingredients: List[RecipeIngredient] = Field(description="Full ingredient list")
    calories_per_serving: float = Field(description="Caloric count per serving")

```

Within LlamaIndex, pass this class into `PydanticOutputParser` or bind it directly to the model's `output_cls` parameter. This constrains the LLM to return valid JSON that conforms directly to the schema definition.

---

## 6. Systematic Evaluation & Production Diagnostics

Evaluation must be quantitative rather than subjective. The **RAGAS** framework assesses performance across three distinct, normalized dimensions ($0.0$ to $1.0$):

* **Context Relevance:** Measures whether the retrieved passages contain relevant details without unnecessary noise.

* **Faithfulness:** Measures whether the factual claims in the answer are supported by the retrieved context (checking for hallucinations).

* **Answer Relevance:** Measures whether the response directly addresses the user's prompt without introducing vague or generic text.

```
                 Triad Evaluation Metrics (RAGAS)
                                 |
        +------------------------+------------------------+
        |                        |                        |
        v                        v                        v
[ Context Relevance ]     [ Faithfulness ]       [ Answer Relevance ]
Are retrieved chunks      Is the answer grounded  Does the answer
focused and relevant?     in the retrieved context? directly address the prompt?
(Evaluates Retrieval)[cite: 2] (Evaluates Grounding)[cite: 2] (Evaluates Synthesis)[cite: 2]

```

### Evaluator Decoupling

To avoid circular evaluation bias (where an LLM rates its own outputs higher), decouple the judge model from the generation stack. If generation and query planning rely on Anthropic's Claude 3.5 Haiku and Sonnet, use an independent model family—such as **Google's Gemini Flash**—as the dedicated judge inside the RAGAS testing harness.

### Production Diagnostic Protocol

1. **Empty Responses:** An immediate empty response string usually indicates an uncaught framework-level exception within LlamaIndex's synthesis layer, rather than a code error. Wrapping the synthesis call in a fallback retry handler mitigates this issue.

2. **Noisy Chunk Retrieval:** If retrieval metrics drop suddenly, inspect the **Embedding Model** first before modifying chunk sizes or metadata. Compare the generated similarity scores of noisy chunks against expected scores to determine if the embedding model is misaligned with the target domain.

---

## 7. End-to-End Implementation: The Atlas RAG & Gradio System

The following production script implements the **Atlas Knowledge Base**. It handles document ingestion, LanceDB vector storage, multi-stage retrieval with similarity filtering and reranking, Pydantic schema validation, and an interactive Gradio interface:

```python
import os
import lancedb
from pathlib import Path
from typing import List
from pydantic import BaseModel, Field

# LlamaIndex Core Imports
from llama_index.core import (
    Settings,
    SimpleDirectoryReader,
    VectorStoreIndex,
    StorageContext
)
from llama_index.core.node_parser import SentenceSplitter
from llama_index.core.embeddings import resolve_embed_model
from llama_index.core.postprocessor import SimilarityPostprocessor
from llama_index.core.query_engine import RetrieverQueryEngine
from llama_index.vector_stores.lancedb import LanceDBVectorStore
from llama_index.llms.openrouter import OpenRouter
from llama_index.postprocessor.cohere_rerank import CohereRerank

# 1. Global Pipeline Configuration
class AtlasConfig:
    CHUNK_SIZE: int = 512
    CHUNK_OVERLAP: int = 50           # ~10% boundary overlap
    SIMILARITY_TOP_K: int = 20        # Stage 1: High Recall Pool
    RERANK_TOP_K: int = 3             # Stage 2: High Precision Context
    SIMILARITY_CUTOFF: float = 0.35   # Similarity Gatekeeper Cutoff
    
    # Models
    EMBED_MODEL: str = "local:BAAI/bge-small-en-v1.5"
    GENERATION_MODEL: str = "anthropic/claude-3.5-haiku"
    
    # Storage Directories
    STORAGE_DIR: Path = Path("./atlas_storage")
    VECTOR_DB_DIR: Path = STORAGE_DIR / "lancedb_data"
    DOCS_DIR: Path = Path("./data/corpus")

cfg = AtlasConfig()
cfg.VECTOR_DB_DIR.mkdir(parents=True, exist_ok=True)
cfg.DOCS_DIR.mkdir(parents=True, exist_ok=True)

# 2. Pydantic Structured Output Schema
class TechnicalResponseSchema(BaseModel):
    executive_summary: str = Field(description="Direct, concise synthesis answering the prompt")
    key_technical_findings: List[str] = Field(description="Bullet points of factual data points")
    confidence_score: float = Field(description="Assessed confidence between 0.0 and 1.0 based on context")

# 3. Global Service Context Initialization
Settings.embed_model = resolve_embed_model(cfg.EMBED_MODEL)
Settings.llm = OpenRouter(
    api_key=os.environ["OPENROUTER_API_KEY"],
    model=cfg.GENERATION_MODEL,
    temperature=0.1
)
Settings.node_parser = SentenceSplitter(
    chunk_size=cfg.CHUNK_SIZE,
    chunk_overlap=cfg.CHUNK_OVERLAP
)

# 4. StorageContext Setup with LanceDB
db = lancedb.connect(str(cfg.VECTOR_DB_DIR))
lance_table = db.create_table(
    "atlas_production_table",
    schema=[
        ("id", str),
        ("vector", list[float]),
        ("text", str)
    ],
    mode="overwrite"
)
vector_store = LanceDBVectorStore(table=lance_table)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

# 5. Ingestion & Indexing Engine
def ingest_and_index(data_dir: Path) -> VectorStoreIndex:
    reader = SimpleDirectoryReader(
        input_dir=str(data_dir),
        recursive=True,
        required_exts=[".pdf", ".md", ".txt", ".csv"]
    )
    documents = reader.load_data()
    print(f"[*] Ingested {len(documents)} document pages.")
    
    index = VectorStoreIndex.from_documents(
        documents,
        storage_context=storage_context,
        show_progress=True
    )
    return index

atlas_index = ingest_and_index(cfg.DOCS_DIR)

# 6. Two-Stage Retrieval Query Engine Construction
def build_query_engine(index: VectorStoreIndex, cutoff: float, top_k: int) -> RetrieverQueryEngine:
    retriever = index.as_retriever(similarity_top_k=top_k)
    
    # Gatekeeper: Hard threshold filter
    similarity_gate = SimilarityPostprocessor(similarity_cutoff=cutoff)
    
    # Reranker: Cross-Encoder joint attention rescoring
    reranker = CohereRerank(
        top_n=cfg.RERANK_TOP_K,
        api_key=os.environ.get("COHERE_API_KEY", "dummy_key")
    )
    
    engine = RetrieverQueryEngine.from_args(
        retriever=retriever,
        node_postprocessors=[similarity_gate, reranker],
        response_mode="compact"
    )
    return engine

# 7. Gradio Prototyping Interface
import gradio as gr

def execute_rag_pipeline(query_str: str, cutoff_slider: float, top_k_slider: int):
    engine = build_query_engine(atlas_index, cutoff_slider, int(top_k_slider))
    response = engine.query(query_str)
    
    # Format Provenance Source Output
    provenance_text = ""
    for idx, node in enumerate(response.source_nodes, 1):
        score = node.score if node.score is not None else 0.0
        doc = node.metadata.get("file_name", "Unknown Document")
        snippet = node.text.replace("\n", " ")[:140]
        provenance_text += f"[{idx}] Score: {score:.4f} | File: {doc}\nSnippet: {snippet}...\n\n"
        
    return response.response, provenance_text

with gr.Blocks(title="Atlas Enterprise RAG Engine") as demo:
    gr.Markdown("# Atlas Knowledge System: Multi-Stage RAG")
    with gr.Row():
        with gr.Column(scale=2):
            query_input = gr.Textbox(lines=3, label="User Query", placeholder="Enter technical question...")
            with gr.Row():
                cutoff_param = gr.Slider(0.0, 1.0, value=0.35, step=0.05, label="Similarity Cutoff Gate")
                top_k_param = gr.Slider(5, 50, value=20, step=5, label="Stage 1 Candidate Top-K")
            submit_btn = gr.Button("Execute Retrieval & Synthesis", variant="primary")
        with gr.Column(scale=2):
            response_output = gr.Textbox(lines=6, label="Synthesized Response")
            provenance_output = gr.Textbox(lines=6, label="Retrieved Source Provenance")
            
    submit_btn.click(
        fn=execute_rag_pipeline,
        inputs=[query_input, cutoff_param, top_k_param],
        outputs=[response_output, provenance_output]
    )

if __name__ == "__main__":
    demo.launch(server_name="0.0.0.0", server_port=7860, share=False)

```

---

## 8. Production Architecture Decision Matrix

Selecting an appropriate RAG pattern requires matching technical constraints—such as query ambiguity, latency, and data format—to the right architecture:

```
+--------------------------+-----------------------+------------------------------------+--------------------------------+
| Query Archetype          | Primary Risk Factor   | Optimal Architecture Pattern       | Recommended Technology Stack   |
+--------------------------+-----------------------+------------------------------------+--------------------------------+
| Simple Factual           | Retrieval Latency &   | Hybrid Retrieval +                 | LanceDB + BGE-Small +          |
| Lookup                   | Incomplete Results    | Reciprocal Rank Fusion (M2)[cite: 1, 2] | BM25 + LlamaIndex[cite: 1, 2]         |
+--------------------------+-----------------------+------------------------------------+--------------------------------+
| Exact Identifier /       | Dense Embedding       | Pure Sparse Lexical Search +       | BM25 / Elasticsearch +         |
| System Parameter         | Semantic Blindness[cite: 2]  | Strict Keyword Match[cite: 1, 2]        | Layout-Aware Parser[cite: 1, 2, 3]    |
+--------------------------+-----------------------+------------------------------------+--------------------------------+
| Multi-Topic /            | Information Frag-     | Query Decomposition +              | Claude 3.5 Sonnet Planner +    |
| Comparative Multi-Hop    | mentation Across Files[cite: 2]| HyDE Generation (M4)[cite: 1, 2]         | Parallel Retrievers[cite: 2]          |
+--------------------------+-----------------------+------------------------------------+--------------------------------+
| High Ambiguity /         | Early Retrieval       | Corrective Agentic RAG             | LangGraph +                    |
| Complex Troubleshooting  | Failure & Hallucination[cite: 2]| (Reflection & Loop Back) (M5)[cite: 1, 2]| Multi-Turn Reflection Loops[cite: 2]   |
+--------------------------+-----------------------+------------------------------------+--------------------------------+
| Monolithic Unstructured  | Context Window Sat-   | Vectorless Structural Navigation   | Document Summary Index /       |
| Document (e.g., Contract)| uration & Dilution[cite: 2, 3]| (Hierarchical GraphStore Trees)    | Hierarchical Node Trees[cite: 2]      |
+--------------------------+-----------------------+------------------------------------+--------------------------------+

```

### Production Rules of Thumb

* **Evaluate Before Tuning:** Establish your Gold Evaluation Set (Exact, Cross-Section, Multi-Hop) and baseline metrics before adjusting chunk sizes, models, or prompt templates.

* **Use Hybrid Search by Default:** Dense embeddings struggle with exact technical strings, CLI flags, and IDs. Combining dense vectors with BM25 keyword matching via RRF resolves most enterprise retrieval issues with minimal added latency.

* **Filter Tail Noise:** Never pass raw top-$k$ outputs directly to an LLM. Use similarity threshold cutoffs and Cross-Encoder rerankers to strip out low-confidence passages before context assembly.

* **Enforce Deterministic Schemas:** For API integrations, database writes, or automated downstream actions, use Pydantic `BaseModel` classes to enforce structured JSON output formats.
* **Orchestration Tradeoffs:** Use frameworks like LlamaIndex for rapid prototyping and architecture validation. For enterprise production scale with unique ingestion needs (such as live database feeds or bespoke XML pipelines), consider building specialized, decoupling parsing, chunking, and retrieval components to maintain full architectural control.

---

## Resources and References

All architectural slides, documentation, implementation repositories, and post-read resources are cataloged below:

* **Session Slide Deck:** [RAG Fundamentals, LanceDB, and LlamaIndex Presentation](https://docs.google.com/presentation/d/1q3wSRpEjzZ9LSAKRdGjnhFkzeBL2sN0o/edit?usp=sharing&ouid=112034599433077560118&rtpof=true&sd=true) — Architectural deck covering embedding dimensions, chunking boundaries, and the evolutionary path to agentic loops.

* **Post-Read 1 (Theoretical Deep-Dive):** [Deep-Dive RAG Architecture Document](https://docs.google.com/document/d/1mmTHtqAbJGPfix8xDBShlfHPUBRo7Wzo9HwU-juRDfc/edit?usp=sharing) — Companion technical notes on ingestion mechanics, embedding model trade-offs, and infrastructure scaling.

* **Post-Read 2 (Hands-On Implementation & Advanced Pipelines):** [Advanced RAG Pipelines, Multimodal Architecture, and Structured Outputs Guide](https://docs.google.com/document/d/1MTKaIZtYDUvtRV27FjqRA-r3ED3LelHSZS-fRugZRsE/edit?usp=sharing) (Direct Tab View: [t.yfpm0hciba95](https://docs.google.com/document/d/1MTKaIZtYDUvtRV27FjqRA-r3ED3LelHSZS-fRugZRsE/edit?tab=t.yfpm0hciba95)) — Practical guide covering Google Colab and `uv` setup, StorageContext internals, two-stage reranking, multimodal normalization via `SimpleDirectoryReader`, Pydantic structured schemas, and Gradio prototyping.
* **Interactive Architecture Workspaces:** [Cohort 6 Excalidraw Notion Workspace](https://www.google.com/search?q=https://antaripasaha.notion.site/Excalidraw-Diagrams-Cohort-6-2c35314a5639807f8892f7776fdff24d) — Interactive whiteboard diagrams illustrating vector indexing pipelines, multimodal routing, and LangGraph state machines.

* **Evaluation Framework Documentation:** [Ragas Evaluation Platform](https://www.ragas.io/) — Official documentation for configuring RAGAS evaluation metrics and LLM-as-a-judge test suites.

* **Hands-On Code Repository:** [Day 6 Engineering Accelerator GitHub Repo](https://www.google.com/search?q=https://github.com/eng-accelerator/ai-accelerator-C6/tree/main/Day%25206/session_2) — Complete codebase including the M0 baseline, M2 hybrid retrieval, M3 contextual ingestion, M4 HyDE decomposition, and M5 agentic state machines.

* **Model Gateway:** [OpenRouter AI Platform](https://openrouter.ai/) — Unified API gateway routing generation, planning, and evaluation tasks across Anthropic Claude, Google Gemini, and OpenAI models.

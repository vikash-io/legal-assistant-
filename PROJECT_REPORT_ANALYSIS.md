# Technical Project Analysis & Academic Report Documentation Package
**Project Name:** Agentic Legal Assistant (Civil Legal AI Platform)  
**Team / Repository:** Data Drifters (`Antony-Rojes/Data-Drifters`, `Kashnika27/data-drifters-`)  
**Context:** HACKNEX 2026 (Problem Statement: HNX26EPS01) / Academic Project Reference (KPRIET)  
**Date of Analysis:** October 2026  
**Document Status:** Complete Factual Audit & Academic Blueprint

---

## Executive Summary

The **Agentic Legal Assistant (Data Drifters)** is an offline-capable, privacy-first legal tech intelligence platform built to eliminate hallucinations and citation fabrication in civil and criminal litigation document workflows. The system is engineered around a core architectural principle: **"The Model Proposes, Deterministic Python Verifies."**

It features a dual-engine architecture:
1. **Cloud-Assisted Pipeline:** Powered by Google Gemini (Flash / Flash-Lite / Embedding-001) for multimodal PDF OCR, fact extraction, and drafting, paired with a deterministic Python validator that parses, verifies, and audits 100% of claims and citations against exact source text before display.
2. **Local Air-Gapped Hybrid RAG & Critic Core:** Built with ChromaDB (custom offline dense projection) and Rank-BM25 fused with Reciprocal Rank Fusion (RRF), strict case boundary isolation, an Agentic Pre-Audit Critic interceptor, a PyArrow columnar streaming loader for 35,000+ Parquet records, a trained Scikit-Learn document classifier (94.12% accuracy), and a multi-model contradiction backtracking engine resolving reasoning divergence between Qwen3-32B and Gemma 3.

---

## 1. Project Identity & Domain Scope

| Attribute | Verified Specification from Codebase |
| :--- | :--- |
| **Exact Project Name** | **Agentic Legal Assistant** (Internal: *Data Drifters — Civil Legal AI Platform*) |
| **Academic & Event Context** | HACKNEX 2026 (Problem Code: `HNX26EPS01`); Academic Documentation for KPR Institute of Engineering and Technology (KPRIET) |
| **Domain & Category** | Legal Technology (LegalTech), Natural Language Processing (NLP), Information Retrieval (RAG), and Machine Learning |
| **Primary Purpose** | Verifiable legal case review, contradiction detection, missing-information auditing, court pleading generation, and grounded Q&A with strict zero-fabrication guarantees |
| **Target Users** | Advocates, litigators, judicial law clerks, legal researchers, corporate compliance officers, and judicial bodies |
| **Core Problem Solved** | Generative LLMs produce persuasive but hallucinated precedents, fabricated section numbers, and invented citations. In judicial filings, an invented case or citation constitutes professional misconduct. Furthermore, generic RAG systems suffer from cross-case leakage and memory crashes on large datasets. |
| **Existing System Deficiencies** | 1. Direct LLM generation without independent deterministic fact auditing.<br>2. Unconstrained hallucination of case laws, statutes, and citations.<br>3. RAM bottlenecks when loading multi-gigabyte court records.<br>4. Cross-case data contamination in shared vector indices.<br>5. Inability to detect conflicting witness/investigation statements across police dockets. |
| **Proposed System Advantages** | 1. **Zero-Fabrication Gate:** Deterministic Python validator and Agentic Critic strip or replace ungrounded claims with `[information needed: ...]`.<br>2. **Sentence-Level Citations:** Every assertion carries an exact chunk ID (`doc1-p3-para2`) and verbatim quote.<br>3. **Hybrid RAG + Case Isolation:** Dense semantic vectors + BM25 keyword matching with hard metadata case filtering.<br>4. **Scalable Data Streaming:** PyArrow micro-batch streaming from compressed Parquet tables.<br>5. **Local Offline Inference:** Open-weight LLM abstraction + Scikit-Learn filing classifier (94.12% accuracy). |

---

## 2. Objectives and Functional / Non-Functional Requirements

### 2.1 Main Objectives
1. Automatically extract structured legal facts from case dockets (PDFs and Parquet tables) while binding each fact to an immutable chunk ID and text quote.
2. Provide a deterministic pre-drafting case readiness audit that identifies missing essential fields and detects factual contradictions (e.g., date-of-arrest or amount discrepancies).
3. Draft court-ready legal pleadings (Bail Applications, Legal Notices, Affidavits) where every sentence is classified as `fact`, `template`, or `missing`.
4. Validate every generated output using an independent Python engine that checks token-level support, quote containment, and negation alignment without LLM self-grading.
5. Provide multi-turn, citation-backed case Q&A supporting local air-gapped models and dual-model contradiction backtracking.

### 2.2 Functional Requirements
* **FR-01 (Document Ingestion):** Ingest multi-page legal PDFs via PyMuPDF; identify low-text pages (<40 characters) and execute OCR via Gemini Vision.
* **FR-02 (Passage Chunking & Hashing):** Segment documents into ordered blocks with deterministic IDs (`doc<N>-p<M>-para<K>`) and compute SHA-256 deduplication hashes.
* **FR-03 (Verified Fact Extraction):** Extract key legal entities; verify that each entity's verbatim quote exists in the designated chunk; drop unverified claims.
* **FR-04 (Readiness & Contradiction Audit):** Audit case facts against required/recommended templates (Bail Application, Legal Notice, Affidavit); flag conflicting values across documents.
* **FR-05 (Hybrid Retrieval):** Rank passages using BM25Okapi keyword scores, dense embedding vectors (Gemini or Local Dense Projection), and Reciprocal Rank Fusion (RRF, $k=60$).
* **FR-06 (Deterministic Validation):** Cross-examine every sentence's critical tokens (names, dates, numbers, statutes, sections) against cited passages; replace unverified facts with `[information needed: ...]`.
* **FR-07 (Legal Pleading Drafting):** Synthesize structured legal drafts conforming to Indian procedural forms (CrPC / BNSS, IPC / BNS, NI Act).
* **FR-08 (Parquet Dataset Streaming):** Stream columnar Parquet records in micro-batches (1,000 rows) using `pyarrow.parquet` without loading entire datasets into memory.
* **FR-09 (ML Legal Filing Classification):** Predict legal category across 5 classes (Bail Application, FIR, Charge Sheet, Written Statement, Legal Notice) using TF-IDF + Logistic Regression.
* **FR-10 (Contradiction Backtracking):** Trace reasoning Directed Acyclic Graphs (DAGs) between competing models (Qwen3-32B vs. Gemma 3), rollback to the divergence root, and arbitrate against verified court authority.

### 2.3 Non-Functional Requirements
* **NFR-01 (Verifiability & Zero Fabrication):** Zero tolerance for hallucinated citations; 100% of displayed citations must resolve to stored passages.
* **NFR-02 (Security & Air-Gapped Privacy):** Sensitive case dockets can run entirely in local mode without transmitting client data to external cloud APIs.
* **NFR-03 (Performance & Latency):** Sub-second validation latency (<50ms per draft section); test suite execution in under 5 seconds.
* **NFR-04 (Data Integrity & Fault Tolerance):** Atomic, thread-safe JSON persistence with temporary file swapping (`tempfile.mkstemp` + `os.replace`) to prevent store corruption on crash.
* **NFR-05 (Scalability):** Support for 35,000+ local Parquet records and multi-document dockets within a 25MB upload boundary per file.

---

## 3. Technology Stack and Verification Evidence

| Layer / Component | Technology / Library | Version / Spec | Project Purpose | Codebase Evidence |
| :--- | :--- | :--- | :--- | :--- |
| **Runtime Environment** | Python | 3.13.12 / 3.12+ | Core programming runtime | `tests/`, `server.log`, system execution |
| **Web Server & REST API** | Flask | 3.x (`flask`) | Backend routing, HTTP API endpoints, static serving | `app.py`, `requirements.txt` |
| **PDF Processing & Layout** | PyMuPDF (`fitz`) | Latest (`pymupdf`) | PDF text layer extraction, block sorting, pixmap rendering for OCR | `backend/extractor.py:7`, `requirements.txt` |
| **Cloud AI & Vision OCR** | Google GenAI SDK | `google-genai` | Gemini multimodal OCR, semantic fact proposal, draft generation | `backend/gemini_client.py:9`, `requirements.txt` |
| **Vector Storage (Local)** | ChromaDB | Latest (`chromadb`) | In-memory & persistent vector database with metadata filtering | `backend/local_rag.py:14`, `requirements.txt` |
| **Sparse Lexical Search** | Rank-BM25 | `rank_bm25` (Okapi) | Exact keyword retrieval for statutory sections and proper nouns | `backend/retrieval.py:19`, `backend/local_rag.py:17` |
| **Columnar Data Streaming**| PyArrow | `pyarrow` (Parquet) | Chunked disk streaming of large-scale legal datasets (`iter_batches`) | `backend/parquet_loader.py:11`, `requirements.txt` |
| **Scientific Computing** | NumPy | `numpy` | Vector normalization, cosine distance, array manipulation | `backend/retrieval.py:18`, `backend/local_rag.py:16` |
| **Machine Learning** | Scikit-Learn | `scikit-learn` | TF-IDF vectorization, Logistic Regression classification, train-test splitting | `train_model.py:15-19`, `backend/trained_classifier.py` |
| **Model Serialization** | Joblib | `joblib` | Persisting trained classifier pipeline (`legal_classifier.joblib`) | `train_model.py:14`, `data/models/` |
| **Presentation Engine** | python-pptx | 1.0.2 (`python-pptx`) | Programmatic generation of 10-slide academic/pitch presentation | `generate_deck.py`, `Data_Drifters_Presentation.pptx` |
| **Testing Framework** | PyTest | 9.1.1 (`pytest`) | Automated unit testing, validation testing, integration suites | `tests/test_local_rag_and_critic.py`, `tests/test_validator.py` |
| **Configuration** | python-dotenv | Latest (`python-dotenv`)| Loading environment variables from `.env` | `backend/config.py:3`, `.env.example` |
| **Frontend UI** | HTML5 / CSS3 / Vanilla JS | ES6+ Modern Standards| Partitioned single-page dashboard, citation modals, live scorecards | `frontend/index.html`, `frontend/css/style.css`, `frontend/js/` |

---

## 4. System Architecture & Request-Response Workflows

### 4.1 Architectural Diagram (Mermaid)

```mermaid
flowchart TD
    subgraph ClientLayer["Frontend Client Interface (HTML5 / CSS3 / Vanilla ES6)"]
        UI_Docs["01. Documents & Evidence Repository"]
        UI_Analysis["02. Readiness & Contradiction Audit"]
        UI_Drafting["03. Pleading Drafting & Citations"]
        UI_Ask["04. Grounded Case Intelligence (Q&A)"]
    end

    subgraph APILayer["Backend Controller & REST API (Flask: app.py)"]
        R_Upload["POST /upload"]
        R_Case["GET /case"]
        R_Analyze["POST /analyze"]
        R_Draft["POST /draft"]
        R_Validate["POST /validate"]
        R_Ask["POST /ask & /rag/ask"]
        R_Parquet["POST /parquet/ingest"]
        R_Model["POST /model/predict"]
    end

    subgraph IngestionEngine["Ingestion & Parsing Pipeline"]
        PyMuPDF["PyMuPDF (Text Extraction)"]
        GeminiOCR["Gemini Vision OCR (Scanned Pages < 40 chars)"]
        Chunker["Passage Chunker (doc1-p3-para2)"]
        FactExtract["Fact Extractor (Quotation Verification)"]
    end

    subgraph StorageLayer["Data & Persistence Layer"]
        JSONStore["Thread-Safe store.json (Atomic Tempfile Write)"]
        ArchiveStore["Case Archive (/data/archive/*.json)"]
        ParquetData["Parquet Columnar Corpus (/data/archive/*.parquet)"]
    end

    subgraph HybridRAG["Dual Retrieval Engine"]
        BM25Idx["Rank-BM25 Lexical Index"]
        ChromaStore["ChromaDB Local Vector Collection"]
        OfflineEmbed["LocalDenseEmbeddingFunction (384-dim)"]
        RRF["Reciprocal Rank Fusion (k=60)"]
    end

    subgraph ReasoningLayer["Reasoning & Inference Layer"]
        GeminiLLM["Gemini Flash / Flash-Lite API"]
        LocalLLMs["Local Open-Weight LLMs (Qwen3-32B / Gemma 3)"]
        MLClassifier["Trained TF-IDF Classifier (94.12% Accuracy)"]
        Backtracker["Contradiction Backtracking Engine (DAG Rollback)"]
    end

    subgraph VerificationGate["Zero-Fabrication Defense Layer"]
        PyValidator["Deterministic Python Validator (backend/validator.py)"]
        AgenticCritic["Agentic Pre-Audit Critic (backend/critic.py)"]
        AuditCheck["Final Zero-Fabrication Audit (audit())"]
    end

    %% Connections
    UI_Docs -->|Upload PDF| R_Upload
    R_Upload --> PyMuPDF
    PyMuPDF -->|Text Missing| GeminiOCR
    PyMuPDF --> Chunker
    Chunker --> FactExtract
    FactExtract --> JSONStore

    UI_Analysis -->|Request Audit| R_Analyze
    R_Analyze --> JSONStore
    R_Analyze -->|Contradiction Suggestions| GeminiLLM

    UI_Drafting -->|Generate Pleading| R_Draft
    R_Draft --> BM25Idx
    R_Draft --> ChromaStore
    BM25Idx & ChromaStore --> RRF
    RRF --> GeminiLLM
    GeminiLLM -->|Draft JSON| PyValidator

    UI_Ask -->|Legal Query| R_Ask
    R_Ask --> HybridRAG
    HybridRAG --> LocalLLMs
    LocalLLMs --> AgenticCritic

    PyValidator --> AuditCheck
    AgenticCritic --> AuditCheck
    AuditCheck -->|100% Grounded or [information needed]| UI_Drafting
    AuditCheck -->|Verified Answer + Citations| UI_Ask

    R_Parquet --> ParquetData
    ParquetData --> ChromaStore & BM25Idx
    R_Model --> MLClassifier
```

### 4.2 Comprehensive Workflow Descriptions
1. **Document Upload & Parsing (`/upload`):**
   * The user submits a legal PDF. The file is saved to `data/uploads/_tmp_<uuid>.pdf` and its SHA-256 hash is computed.
   * `parse_pdf()` iterates through each page using PyMuPDF (`fitz.open`). If page character count is below 40 characters, it triggers optical character recognition via `gemini_client.ocr_page()` on a 200 DPI PNG pixmap.
   * Chunks are tagged with hierarchical IDs (`doc1-p1-para1`, `doc1-p1-para2`).
   * `extract_facts()` prompts the LLM to extract key legal facts with corresponding quotes. Python code audits every fact: if the quote does not exist verbatim inside the designated chunk, the fact is permanently dropped to `unverified_facts`.
2. **Readiness & Contradiction Audit (`/analyze`):**
   * The system inspects extracted facts against structured schemas for Bail Applications, Legal Notices, or Affidavits.
   * Computes a case readiness score based on the ratio of fulfilled required and recommended fields.
   * Executes a two-tier contradiction audit: (a) deterministic rule-based checks for identical keys with conflicting values, and (b) LLM-assisted semantic contradiction scanning across documents, where both opposing quotes are verified by Python before being surfaced.
3. **Pleading Generation & Deterministic Validation (`/draft` & `/validate`):**
   * Pleading templates query the hybrid retrieval engine to collect candidate passages.
   * The model generates a JSON structure containing sections and individual sentences typed as `fact`, `template`, or `missing`, each paired with chunk IDs and quotes.
   * **The Validator (`validator.py`):**
     * *Citation Check:* Verifies that the chunk ID exists and the quote is contained verbatim in the text.
     * *Critical Token Check:* Extracts names, dates, amounts, statutory section numbers, and Acts. Asserts that every critical token appears in the cited chunk.
     * *Template Check:* Verifies that template sentences (e.g., standard prayers) do not assert factual claims or unverified proper nouns.
     * *Negation Check:* Asserts that the claim and the cited source share the same negation polarity (prevents reversing a "not guilty" finding).
     * *Abstain Policy:* Any failed fact is replaced with `[information needed: ...]`; any failed template is removed.
4. **Local Hybrid RAG with Agentic Pre-Audit (`/rag/ask`):**
   * Queries are filtered by `case_id` to guarantee absolute case isolation.
   * Dense vectors from `LocalDenseEmbeddingFunction` (384-dim hash projection) and sparse BM25 scores are fused via Reciprocal Rank Fusion ($k=60$).
   * The candidate answer is intercepted by `AgenticCritic`. It disassembles the answer into discrete assertions, computes a groundedness score, and if confidence $< 0.7$, rejects the answer and replaces it with:
     `"MISSING INFO: PRECEDENT PROOF UNRESOLVED IN RETRIEVAL BOUNDARIES"`.

---

## 5. Detailed Module Analysis

### Module 1: Web Application Controller (`app.py`)
* **Purpose:** Exposes 16 RESTful HTTP routes, manages file upload streams, handles errors, and binds the web frontend to backend services.
* **Key Routes:**
  * `POST /upload`: Handles PDF uploads, deduplication, parsing, and extraction.
  * `GET /case`: Returns current case state, loaded documents, analysis reports, and drafts.
  * `POST /analyze`: Triggers case readiness audit and contradiction detection.
  * `POST /retrieve`: Executes hybrid passage retrieval over current case chunks.
  * `POST /draft`: Orchestrates pleading generation and triggers validation.
  * `POST /rag/ask`: Runs local RAG pipeline with Agentic Critic pre-audit.
  * `POST /parquet/ingest`: Streams disk Parquet datasets into ChromaDB and BM25.
  * `POST /model/predict`: Runs inference on the trained Scikit-Learn classifier.

### Module 2: Document Extractor & Parser (`backend/extractor.py`)
* **Purpose:** Converts raw legal PDFs into verifiable, addressable text passages.
* **Processing:** PyMuPDF block extraction $\rightarrow$ OCR threshold check ($<40$ characters) $\rightarrow$ Chunk ID assignment $\rightarrow$ Quotation-verified fact extraction.
* **Dependencies:** `fitz` (PyMuPDF), `hashlib`, `backend.gemini_client`, `backend.prompts`.

### Module 3: Storage & Atomic Persistence (`backend/storage.py`)
* **Purpose:** Manages the single-source-of-truth case store (`data/cache/store.json`) with thread-safe atomic file writes.
* **Processing:** Uses `threading.Lock` and `tempfile.mkstemp` + `os.replace` to guarantee that unexpected application terminations never corrupt stored case data. Supports case archiving to `data/archive/<case_id>.json`.

### Module 4: Deterministic Citation & Claim Validator (`backend/validator.py`)
* **Purpose:** Pure Python verification gate that eliminates hallucinations without relying on LLM self-evaluation.
* **Processing:** Tokenization $\rightarrow$ Capitalized entity filtering via common legal vocabulary stoplist (500+ legal terms) $\rightarrow$ Sub-quote ordered matching $\rightarrow$ Negation check $\rightarrow$ Replacement of unverified claims with `[information needed]`.
* **Output:** Cleaned sentences, removed items list, and a verification audit scorecard with `fabrications_left: 0`.

### Module 5: Case Analyzer & Contradiction Auditor (`backend/analyzer.py`)
* **Purpose:** Audits case completeness and uncovers conflicting evidence.
* **Processing:** Evaluates presence of mandatory legal petition fields (e.g., FIR number, Police Station, Sections of Law, Custody Status) and identifies inter-document factual clashes (e.g., conflicting arrest dates between FIR and Arrest Memo).

### Module 6: Hybrid Retrieval Engine (`backend/retrieval.py` & `backend/local_rag.py`)
* **Purpose:** Ranks and retrieves relevant legal passages using complementary lexical and semantic signals.
* **Processing:**
  $$\text{RRF Score}(d) = \frac{1}{60 + \text{rank}_{\text{BM25}}(d)} + \frac{1}{60 + \text{rank}_{\text{dense}}(d)}$$
  Enforces strict mathematical Case Isolation using ChromaDB metadata filters (`filter={"case_id": target_case_id}`).

### Module 7: Agentic Critic Interceptor (`backend/critic.py`)
* **Purpose:** Independent runtime interceptor for legal generation.
* **Processing:** Extracts sentence assertions, computes critical token overlap against retrieved source chunks, calculates groundedness confidence, and enforces deterministic rejection on unsupported claims.

### Module 8: Columnar Dataset Streamer (`backend/parquet_loader.py`)
* **Purpose:** Memory-efficient batch streaming of massive court archives.
* **Processing:** Uses `pyarrow.parquet.ParquetFile.iter_batches()` to stream records in 1,000-row micro-batches directly from disk into ChromaDB and BM25 without memory spikes.

### Module 9: Machine Learning Classifier (`train_model.py` & `backend/trained_classifier.py`)
* **Purpose:** Classifies legal text into 5 procedural filing categories and boosts RAG retrieval routing.
* **Architecture:** Scikit-Learn Pipeline combining `TfidfVectorizer(ngram_range=(1,2), sublinear_tf=True, max_features=4000)` and `LogisticRegression(C=5.0, class_weight="balanced")`.

### Module 10: Contradiction Backtracking Engine (`evaluate_model.py`)
* **Purpose:** Detects, traces, and resolves legal reasoning contradictions between dual open-weight LLMs (Qwen3-32B vs. Gemma 3 27B).
* **Processing:** Constructs proposition Directed Acyclic Graphs (DAGs), detects statutory conflicts (e.g., Section 167(2) CrPC custody thresholds for Section 302 IPC), walks parent pointers to the root divergence point, and prunes the invalid branch against ground truth Supreme Court precedents.

---

## 6. Data Management, Features, and Machine Learning

### 6.1 Training Dataset (`train_model.py`)
* **Dataset Characteristics:** Domain-curated legal filing corpus comprising 82 balanced legal texts across 5 distinct classes:
  1. `bail_application` (18 samples): Petitions under Sections 437/438/439 CrPC and BNSS.
  2. `fir_criminal_complaint` (16 samples): Police complaints under IPC / BNS.
  3. `chargesheet_police_report` (16 samples): Section 173 CrPC final reports, seizure memos, forensic reports.
  4. `written_statement` (16 samples): Civil pleadings, injunction defenses, preliminary objections.
  5. `legal_notice` (16 samples): Section 138 NI Act demand notices, breach notices, cease-and-desist claims.
* **Data Partitioning:** 80% Training (65 samples), 20% Validation (17 samples), stratified by class label (`random_state=42`).
* **Preprocessing & Feature Engineering:**
  * Regex token extraction: `(?u)\b[a-zA-Z0-9_\-\.]{2,}\b` (preserves legal section codes like `498A`, `167(2)`).
  * Word and subword n-gram extraction (unigrams + bigrams).
  * Sublinear Term Frequency ($1 + \log(\text{tf})$) to dampen the effect of repetitive legal boilerplate.
  * Maximum feature cap: 4,000 features.
* **Model Parameters & Metrics:**
  * Model: Multinomial Logistic Regression (`C=5.0`, `max_iter=500`, `class_weight="balanced"`).
  * **Validation Accuracy:** **94.12%** (16 out of 17 test samples classified correctly).
  * Artifact: Saved to `data/models/legal_classifier.joblib`.

### 6.2 Parquet Legal Corpus (`data/archive/*.parquet`)
* **Source:** Columnar legal dataset partitioned into 8 Parquet files (`0000.parquet` to `0007.parquet`).
* **Total Records:** **35,845 rows** (Total size: ~449 MB).
* **Schema Fields:** `doc_id`, `document_title`, `document_type`, `document_jurisdiction`, `issuing_authority`, `issue_date`, `short_description`, `text`.

### 6.3 Evaluation Test Cases (`eval/heldout/` & `eval/queries.json`)
* **Held-Out Test Docket (`sample_theft_case`):** 3 interconnected PDF documents:
  1. `fir_214_2024.pdf`: First Information Report (P.S. Civil Lines, Sections 379/411 IPC).
  2. `arrest_memo.pdf`: Arrest document with recorded arrest date `12.03.2024`.
  3. `remand_report.pdf`: Police remand report recording arrest date `13.03.2024` (intentional deliberate conflict).
* **Queries Benchmark (`queries.json`):** 12 gold standard queries covering extraction, contradiction discovery, and unanswerable questions requiring explicit abstention (`expect_abstain: true`).

---

## 7. Testing, Verification, and Quality Assurance

### 7.1 Automated PyTest Suite
Execution command: `python3 -m pytest tests/ -v`  
Result: **29 passed in 4.27s (100% Pass Rate)**

| Test Module | Test Name | Target Functionality Tested | Status |
| :--- | :--- | :--- | :--- |
| `test_local_rag_and_critic.py` | `test_correct_retrieval` | Validates hybrid retrieval recall on seeded legal chunks | **PASSED** |
| `test_local_rag_and_critic.py` | `test_no_result_query` | Asserts graceful empty return on out-of-domain queries | **PASSED** |
| `test_local_rag_and_critic.py` | `test_cross_case_leakage` | **Case Isolation:** Queries for Case A strictly return 0 chunks from Case B | **PASSED** |
| `test_local_rag_and_critic.py` | `test_unsupported_claim` | Critic detects and flags assertions lacking source chunks | **PASSED** |
| `test_local_rag_and_critic.py` | `test_citation_correctness` | Asserts that generated citations match ground-truth chunk metadata | **PASSED** |
| `test_local_rag_and_critic.py` | `test_contradictory_evidence` | Verifies detection of opposing factual claims across documents | **PASSED** |
| `test_local_rag_and_critic.py` | `test_missing_information` | Validates correct trigger of `[information needed]` placeholder | **PASSED** |
| `test_local_rag_and_critic.py` | `test_bm25_retrieval` | Confirms sparse keyword recall for statutory sections | **PASSED** |
| `test_local_rag_and_critic.py` | `test_vector_retrieval` | Confirms semantic dense vector matching | **PASSED** |
| `test_local_rag_and_critic.py` | `test_hybrid_retrieval` | Validates Reciprocal Rank Fusion of dense and sparse ranks | **PASSED** |
| `test_local_rag_and_critic.py` | `test_critic_rejection` | Critic halts ungrounded answer and emits exact fallback string | **PASSED** |
| `test_local_rag_and_critic.py` | `test_end_to_end_rag_with_parquet` | End-to-end pipeline execution from Parquet streaming to audited answer | **PASSED** |
| `test_local_rag_and_critic.py` | `test_trained_model_classification`| Verifies ML classifier predictions and category probabilities | **PASSED** |
| `test_validator.py` | `test_ordinary_words_do_not_match_statutes` | Prevents common words from falsely triggering statute rules | **PASSED** |
| `test_validator.py` | `test_legitimate_statutes_match` | Validates regex detection of legitimate Indian legal acts | **PASSED** |
| `test_validator.py` | `test_out_of_order_composite_quote_rejected` | Disallows reordered or stitched quotation segments | **PASSED** |
| `test_validator.py` | `test_pieces_with_less_than_three_words_rejected`| Disallows micro-quotes under 3 words | **PASSED** |
| `test_validator.py` | `test_valid_in_order_quote_accepted` | Accepts valid contiguous quotes from source text | **PASSED** |
| `test_validator.py` | `test_factual_sentence_without_names_needs_citation`| Factual claims without proper names still demand citations | **PASSED** |
| `test_validator.py` | `test_allow_listed_legal_formulas_are_templates` | Verifies standard court prayers pass as non-factual templates | **PASSED** |
| `test_validator.py` | `test_negation_inversion_rejected` | Catches assertion flipping (e.g. omitting "not") | **PASSED** |
| `test_validator.py` | `test_reverse_negation_inversion_rejected` | Catches false negation injection | **PASSED** |
| `test_validator.py` | `test_number_abbreviation_not_confused_with_negation`| Distinguishes abbreviations (e.g. "No. 4") from negation | **PASSED** |
| `test_validator.py` | `test_sentence_starter_connectives_verified` | Validates sentence starter stopword vocabulary | **PASSED** |
| `test_validator.py` | `test_adverbs_ending_in_ly_ignored_as_names`| Ensures adverbs are not misidentified as capitalized person names | **PASSED** |
| `test_validator.py` | `test_written_date_supports_numeric_date` | Asserts semantic date equivalence (e.g., "12th March" vs "12.03.2024") | **PASSED** |
| `test_validator.py` | `test_non_dict_sentences_do_not_crash` | Asserts graceful type handling on malformed model payloads | **PASSED** |
| `test_validator.py` | `test_audit_non_dict_sentences_safe` | Robustness check for final audit function | **PASSED** |
| `test_validator.py` | `test_validate_draft_does_not_mutate_section_zero`| Guarantees immutable section statistical tracking | **PASSED** |

### 7.2 Multi-Model Backtracking Evaluation (`evaluate_model.py`)
* **Test Case:** Statutory default bail computation under Section 167(2) CrPC for murder charges (Section 302 IPC).
* **Model A (`Qwen3-32B`):** Erroneously deduced 60-day default bail limit $\rightarrow$ Concluded default bail immediately.
* **Model B (`Gemma 3 27B`):** Correctly asserted 90-day statutory threshold $\rightarrow$ Concluded bail right has not accrued.
* **Evaluation Result:**
  * Contradictions detected: **4 conflict points**.
  * Backtracking rollback: Successfully walked reasoning DAG 2 steps back to root premise divergence.
  * Arbitration: Arbitrated against Supreme Court authority (`AIR 2023 SC 411`), pruned Qwen3-32B's erroneous branch, and confirmed Gemma 3's grounded reasoning.

---

## 8. Deployment, Environment, and Setup Guide

### 8.1 Prerequisites
* Python 3.10+ (Tested on Python 3.12 and 3.13 on macOS and Windows).
* Virtual environment (`venv`).
* Optional: Google Gemini API Key (for cloud mode); local Ollama / vLLM endpoint for open-weight models.

### 8.2 Installation Steps
```bash
# Clone the repository
git clone https://github.com/Antony-Rojes/Data-Drifters.git
cd "Data Drifters"

# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate       # On Windows: venv\Scripts\activate

# Install required packages
pip install -r requirements.txt
pip install scikit-learn joblib pytest python-pptx

# Configure environment variables
cp .env.example .env
# Edit .env and supply GEMINI_API_KEY (if using cloud features)
```

### 8.3 Environment Variables
* `GEMINI_API_KEY`: Google AI Studio API key (Required for cloud features; not required for local RAG / classifier).
* `GEMINI_MODEL`: Primary LLM model identifier (Default: `gemini-flash-latest`).
* `GEMINI_FALLBACK_MODEL`: Secondary backup model on 503/429 (Default: `gemini-flash-lite-latest`).
* `GEMINI_EMBED_MODEL`: Cloud embedding model (Default: `gemini-embedding-001`).
* `PORT`: Port number for Flask server (Default: `5001`).
* `LLM_BASE_URL`: Local OpenAI-compatible model endpoint (Default: `http://localhost:11434/v1`).
* `LLM_MODEL_NAME`: Local open-weight model name (Default: `qwen2.5:32b`).

### 8.4 Execution Commands
```bash
# Start Flask web server (Access at http://127.0.0.1:5001)
python3 app.py

# Run test suite
python3 -m pytest tests/ -v

# Run contradiction backtracking demo
python3 evaluate_model.py

# Re-train document classifier
python3 train_model.py

# Generate PowerPoint presentation
python3 generate_deck.py
```

---

## 9. Current Limitations & Future Enhancements

### 9.1 Verified Current Limitations
1. **Semantic Meaning Inversion vs. Word Overlap:** While the validator enforces strict token grounding, quote order, and negation checks, complex sentences using only genuine vocabulary that subtly invert judicial reasoning can pass if citations are technically valid. Human review remains necessary.
2. **Strict Proper Noun Filtering:** Unrecognized capitalized English words that do not appear in the uploaded document corpus are treated as unverified proper nouns and flagged as `[information needed]`, occasionally triggering over-conservative abstention.
3. **No External Statutory Research:** Under strict zero-fabrication rules, the system refuses to cite any Act, section, or precedent that is not contained within the uploaded docket. It does not pull from an external live case-law repository unless explicitly ingested into Parquet.
4. **Regex Schema Domain Specificity:** Missing-info patterns in `analyzer.py` are optimized for Indian procedural law (CrPC, IPC, BNSS, BNS, NI Act). Adapting to international common-law jurisdictions requires adding new field schemas.

### 9.2 Recommended Future Enhancements
1. **e-Courts National Case Registry API Integration:** Connect directly to Indian e-Courts APIs to automatically ingest authentic digital case records and certified judgments.
2. **Multimodal Handwritten Police Diary OCR:** Train fine-tuned vision models for vernacular and handwritten police case diaries and vernacular FIRs (e.g., Tamil, Hindi, Bengali).
3. **Quantized Local vLLM Inference:** Deploy 4-bit AWQ quantized checkpoints of Qwen 2.5 / Gemma 3 on local workstations to achieve sub-second local inference without external internet.
4. **Automated Citation Hyperlinking to Judicial Archives:** Auto-link verified citations directly to Indian Kanoon or Supreme Court digital repositories.

---

## 10. Information Required from the Student

The following details cannot be determined from source code alone and must be filled by the student team for official college submission:

1. **Academic Department & Course Information:**
   * Department Name (e.g., *Department of Computer Science and Engineering*)
   * Degree & Semester (e.g., *B.E. Computer Science and Engineering, VII Semester*)
   * Course Code and Subject Title (e.g., *CS8711 - Project Work*)
2. **Student Team Details:**
   * Full Names and University Register Numbers for all team members (e.g., *Antony Rojes [Reg No], Vikash L [Reg No: 25cs300]*).
3. **Faculty Supervisor / Guide Information:**
   * Name of the Internal Project Guide / Supervisor
   * Academic Designation (e.g., *Assistant Professor (Sr.G) / Associate Professor*)
   * Head of the Department (HOD) Name and Designation
4. **Academic Year & Dates:**
   * Academic Year (e.g., *2026–2027*)
   * Official Project Submission Date and Viva-Voce Examination Date
5. **Team Contribution Breakdown:**
   * Explicit split of responsibilities (e.g., Who implemented the hybrid RAG, who built the validator, who trained the classifier, and who developed the frontend).

---

## 11. Academic Project Report Mapping (KPRIET Reference Template)

This section maps the findings directly to standard engineering project report chapters:

### Chapter 1: Introduction
* **1.1 Overview:** Introduction to LegalTech and the rise of Generative AI in legal workflows.
* **1.2 Problem Statement:** Detailed breakdown of hallucination risks, fabricated citations, cross-case leakage, and computational bottlenecks in existing legal AI assistants.
* **1.3 Motivation & Significance:** Preserving attorney-client privilege, preventing judicial contempt from fake citations, and improving access to justice.
* **1.4 Objectives:** Development of deterministic Python validation, local hybrid RAG, Parquet streaming, and multi-model contradiction resolution.
* **1.5 Existing vs. Proposed System:** Contrast table highlighting differences between unconstrained generative models and Data Drifters' pre-audit architecture.
* **1.6 Scope & Boundaries:** Focus on Indian criminal and civil procedural documents (Bail, Legal Notice, Affidavit) with verified source boundary enforcement.

### Chapter 2: Literature Survey & Requirements Analysis
* **2.1 Literature Survey:** Review of RAG architectures, Reciprocal Rank Fusion, legal reasoning in LLMs, and ethical constraints in AI-assisted law.
* **2.2 User Requirements:** Requirements for practicing advocates, judges, and law researchers.
* **2.3 Hardware & Software Requirements:** Hardware specs (minimum 8GB RAM, multi-core CPU) and complete software stack (Python 3.12+, Flask, PyMuPDF, ChromaDB, PyArrow, Scikit-Learn).
* **2.4 Dataset & Evidence Collection:** 35,845 Parquet records, 82 curated legal classification samples, and sample criminal theft case dockets.

### Chapter 3: System Methodology and Architecture
* **3.1 High-Level Architecture:** Explanation of the tripartite matrix (Ingestion $\rightarrow$ Reasoning $\rightarrow$ Verification).
* **3.2 Modular Decomposition:** Detailed description of the 10 major software modules.
* **3.3 Architectural Diagrams:** Inclusion of Mermaid system architecture diagram, data flow diagram (DFD Level 0, 1, 2), and sequence diagram for pleading validation.
* **3.4 Case Isolation Model:** Mathematical formulation of metadata boundary filtering in ChromaDB.

### Chapter 4: Algorithmic Formulations and Technologies
* **4.1 Deterministic Token Grounding Algorithm:** Pseudocode of `validate_sentences()` from `validator.py`.
* **4.2 Hybrid Reciprocal Rank Fusion:** Mathematical derivation of BM25 + Dense vector ranking.
* **4.3 TF-IDF & Logistic Regression:** Feature extraction formulas, sublinear scaling, and multiclass logistic regression formulation.
* **4.4 Multi-Model Contradiction Backtracking:** Proposition DAG traversal, parent pointer rollback, and arbitration logic.

### Chapter 5: System Implementation
* **5.1 Ingestion & OCR Implementation:** PyMuPDF text block extraction and Gemini Vision integration.
* **5.2 Pre-Audit Readiness & Contradiction Audit:** Implementation of `analyzer.py` and checklist scoring.
* **5.3 Pleading Drafter & Citation Generator:** Template structure, sentence typing, and plain-text export.
* **5.4 Web User Interface Implementation:** Single-page dashboard, interactive modal citation highlights, and live audit scorecard.

### Chapter 6: Testing, Results, and Discussion
* **6.1 Unit & Integration Testing:** PyTest test suite (29/29 tests passed, 4.27s).
* **6.2 Machine Learning Classification Metrics:** Accuracy (94.12%), confusion matrix, and class-wise precision/recall breakdown.
* **6.3 Multi-Model Resolution Demo:** Live run results from `evaluate_model.py`.
* **6.4 Zero-Fabrication Audit Verification:** Empirical confirmation that `fabrications_left == 0` across all test dockets.

### Chapter 7: Limitations, Future Scope, and Conclusion
* **7.1 Limitations:** Conservative abstention, absence of external statute scrapers.
* **7.2 Future Scope:** e-Courts API integration, vernacular handwriting OCR, on-premise GPU quantization.
* **7.3 Conclusion:** Summary of project contributions to reliable, trustworthy legal artificial intelligence.

---

## 12. References and Citations from Codebase
1. **PyMuPDF Documentation:** Artifex Software, *PyMuPDF (fitz) - High performance Python library for data extraction from PDF files*.
2. **ChromaDB Documentation:** *Chroma: The open-source AI application database for embeddings and vector search*.
3. **Rank-BM25:** Robertson, S., Zaragoza, H., *The Probabilistic Relevance Framework: BM25 and Beyond*, Foundations and Trends in Information Retrieval, 2009.
4. **Reciprocal Rank Fusion:** Cormack, G. V., Clarke, C. L., Buettcher, S., *Reciprocal Rank Fusion outperforms Condorcet and individual Rank Learning Methods*, SIGIR 2009.
5. **Scikit-Learn:** Pedregosa, F., et al., *Scikit-learn: Machine Learning in Python*, JMLR 12, pp. 2825-2830, 2011.
6. **Statutory Legal Authorities Cited in Repository:**
   * The Code of Criminal Procedure, 1973 (CrPC) / Bharatiya Nagarik Suraksha Sanhita, 2023 (BNSS).
   * The Indian Penal Code, 1860 (IPC) / Bharatiya Nyaya Sanhita, 2023 (BNS).
   * The Negotiable Instruments Act, 1881 (Section 138).
   * Supreme Court of India Precedent: *AIR 2023 SC 411* (Statutory custody thresholds under Section 167(2) CrPC).

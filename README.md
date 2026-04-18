# ⚖️ AI Legal Autopsy — Intelligent Indian Legal Analysis System

> A RAG-powered legal intelligence engine that forensically dissects landmark Indian court cases using modern statutes (BNS, BNSS, BSA), with multilingual support for 10+ Indian languages.

---

## 📌 Overview

**AI Legal Autopsy** is a Retrieval-Augmented Generation (RAG) system built on **Databricks** that enables users to query complex Indian legal cases and receive structured, statute-backed analysis. The system routes questions to relevant case archives, retrieves matching legal facts and statutes in isolation, and generates a multi-section forensic legal report — in the user's preferred Indian language.

The system is designed to answer questions like:
- *"Why was the juvenile in the Pune Porsche crash given bail so quickly?"*
- *"What procedural lapses led to acquittal in the Himachal NDPS case?"*
- *"How did political influence affect the Priyadarshini Mattoo trial?"*

---

## 🚀 Setup & Usage

> **The entire system lives in `Yash_note_book.ipynb`. Open this notebook in your Databricks workspace and run just last cell , enter the query and press enter button — that's all you need to run, don't run anything else it may give erros.**

### Prerequisites
- Databricks workspace with a running cluster
- Access to `databricks-meta-llama-3-3-70b-instruct` endpoint
- Sarvam AI API key
- CSV files uploaded to `/Volumes/workspace/legal_data/raw_csv_uploads/`

### 1. Install Dependencies
```python
%pip install beautifulsoup4 langchain langchain-community langchain-huggingface \
             sentence-transformers faiss-cpu langdetect
%restart_python
```

### 2. Configure Secrets
Create a `config.json` at your workspace path:
```json
{
  "MY_API_KEY": "your_sarvam_api_key_here"
}
```


### 3. Ask a Question — UI Walkthrough

The **last cell** renders an interactive UI directly inside the Databricks notebook:


```

**Step-by-step:**
1. Locate the **`query` widget** in the top-left corner of the final cell's output panel
2. Click the text field and **type your legal question** (in English or any supported Indian language)
note :- due to legality many of the question sarvam refuses to translate hence prefer giving it in english thankyou
3. Press **Enter** or re-run the last cell
4. Watch the debug lines confirm which case was matched and how many facts were retrieved
5. The **AI Legal Autopsy panel** renders below with your full 5-section report

> *"The Bombay High Court allowed Vodafone to provide a 'corporate guarantee' instead of a cash deposit for a 1100-crore tax demand. Is this a Judicial Disconnect that favors multinational corporations over the state's revenue? Contrast this with how the law handles tax recovery for smaller, local entities."*

The system correctly routed this to `vodafone_tax_dispute`, retrieved 3 case fact chunks, and produced a full autopsy covering the ITAT ruling, BNS/BNSS/BSA statutory verification, and a judicial disconnect analysis noting the lack of clear guidelines on corporate guarantees in tax recovery — flagging the inconsistency vs. how smaller entities are treated.

---

<div style="overflow-x:auto;">

## 🏗️ Architecture

User Query (any Indian language)
        ↓
Language Detection (langdetect)
        ↓
Translation → English (Sarvam API)
        ↓
LLM Query Router (Llama 3.3 70B)
        ↓
Case Categorization (specific case / ALL)
        ↓
Vector Retrieval (FAISS)
   ├── Case Facts
   └── Statutes (BNS / BNSS / BSA)
        ↓
Filtered Retrieval (per category)
        ↓
Structured Prompt (5-section template)
        ↓
LLM Generation (Llama 3.3 70B)
        ↓
Back Translation (Sarvam API)
        ↓
HTML Report (Databricks UI)
---

## 🗂️ Data Sources

### Legal Statutes (Knowledge Base)
| File | Category | Description |
|---|---|---|
| `bns_sections.csv` | `BNS_Statutes` | Bharatiya Nyaya Sanhita — Substantive Criminal Law |
| `BNSS_sections_accurate.csv` | `BNSS_Statutes` | Bharatiya Nagarik Suraksha Sanhita — Criminal Procedure |
| `bsa_sections.csv` | `BSA_Statutes` | Bharatiya Sakshya Adhiniyam — Law of Evidence |

### Case Archives
| File | Case |
|---|---|
| `Shivam.csv` | Unnao Rape Case — sexual assault, political corruption, witness murder |
| `pune_porsche_crash.csv` | Pune Porsche Crash — minor DUI, culpable homicide, blood sample tampering |
| `himachal_ndps_surat_singh.csv` | Himachal NDPS — Section 50 NDPS non-compliance, technical acquittal |
| `russian_national_ndps_vildanov.csv` | Vildanov NDPS — foreign national rights, border smuggling, procedural manipulation |
| `hashimpura_massacre.csv` | Hashimpura 1987 — PAC custodial killings, evidence suppression, RTI discovery |
| `priyadarshini_mattoo.csv` | Priyadarshini Mattoo — influence of IG's son, trial court acquittal vs HC reversal |
| `vijay_mallya.csv` | Vijay Mallya — financial fraud, extradition, Fugitive Economic Offenders Act |
| `vodafone_tax_dispute.csv` | Vodafone Tax Case — international tax, offshore valuation, MNC vs state revenue |

---

## 🔬 Analysis Output Structure

Each query produces a **5-section Legal Autopsy Report**:

1. **The Facts of the Matter** — Summary of documented case events
2. **Statutory Verification (Isolated)** — Exact relevant sections from BNS, BNSS, and BSA
3. **Expert Legal Autopsy** — Forensic cross-analysis of facts against new statutes
4. **Investigation & Police Lapses** — Identification of executive/procedural failures
5. **Judicial Disconnect & The Justice Gap** — Analysis of sentencing vs legislative intent

---

## 🌐 Multilingual Support

The system supports queries and responses in **11 Indian languages** via the [Sarvam AI](https://www.sarvam.ai/) translation API:

| Language | Code |
|---|---|
| Hindi | `hi-IN` |
| Bengali | `bn-IN` |
| Kannada | `kn-IN` |
| Malayalam | `ml-IN` |
| Marathi | `mr-IN` |
| Odia | `or-IN` |
| Punjabi | `pa-IN` |
| Tamil | `ta-IN` |
| Telugu | `te-IN` |
| Gujarati | `gu-IN` |
| English | `en-IN` |

---

## ⚙️ Tech Stack

| Component | Technology |
|---|---|
| **Platform** | Databricks (Apache Spark + Delta Lake) |
| **LLM** | Meta Llama 3.3 70B Instruct (via Databricks endpoint) |
| **Embeddings** | `sentence-transformers/all-MiniLM-L6-v2` (HuggingFace) |
| **Vector Store** | FAISS (via LangChain) |
| **Orchestration** | LangChain (PromptTemplate, RecursiveCharacterTextSplitter) |
| **Translation** | Sarvam AI API |
| **Language Detection** | `langdetect` |
| **Storage** | Delta Lake table (`workspace.legal_data.legal_autopsy_raw_docs`) |

---


## 🛡️ Anti-Hallucination Design

The system enforces strict grounding through prompt-level rules:

- **IPC Ban**: The LLM is explicitly prohibited from citing old IPC/CrPC sections in analysis — it must translate charges into BNS/BNSS equivalents using only retrieved statutes.
- **Transparency Flags**: If a statute category returns no results, the model is forced to state `"Transparency Note: No [X] laws were found"` rather than improvising.
- **Strict Sourcing**: Every claim in the output must be traceable to a retrieved `[STATUTE]` or `[CASE FACT]` block.
- **Low Temperature**: LLM is set to `temperature=0.1` to enforce factual, deterministic output.

---

## 📁 Project Structure

```
legal-autopsy/
├── Yash_note_book.ipynb       # Main Databricks notebook
├── config.json                # API keys (not committed)
└── /Volumes/.../raw_csv_uploads/
    ├── bns_sections.csv
    ├── BNSS_sections_accurate.csv
    ├── bsa_sections.csv
    ├── Shivam.csv
    ├── pune_porsche_crash.csv
    ├── himachal_ndps_surat_singh.csv
    ├── russian_national_ndps_vildanov.csv
    ├── hashimpura_massacre.csv
    ├── priyadarshini_mattoo.csv
    ├── vijay_mallya.csv
    └── vodafone_tax_dispute.csv
```

---

## 🔮 Planned Enhancements

- [ ] Add remaining case files (Best Bakery, Nithari killings, Nitish Katara, Bani Alam Mazid)
- [ ] Persistent FAISS index (save/load to avoid re-embedding on every run)
- [ ] Streaming LLM responses for faster UI feedback
- [ ] Citation highlighting — trace each autopsy claim to its exact retrieved chunk
- [ ] REST API wrapper for integration with external legal portals

---

## 👤 Author

**Yash** — IIT Indore (`cse230001083@iiti.ac.in`)

---

## ⚠️ Disclaimer

This tool is intended for **educational and research purposes only**. It does not constitute legal advice. All analysis is generated by an AI model and may contain errors. Always consult a qualified legal professional for legal matters.

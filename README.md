# HadithRAG

**Last edited:** February 4, 2026

---

> [!CAUTION]
> **Personal Course Project — Not a Religious Source**
>
> This was made for a university course, for learning only. The hadith data is pulled from available public sources (Sahih al-Bukhari and Sahih Muslim), so the texts themselves are real. However, the LLM that explains them can still make mistakes — it may misinterpret, miss context, or give an incomplete explanation even when the source hadith is correct. So while the retrieved hadith may be authentic, the generated explanation should not be fully trusted. Do not use this for any religious guidance. For anything religion-related, please go to a qualified Islamic scholar.

---

## What It Does

A RAG (Retrieval-Augmented Generation) system that lets you search and ask questions about Hadiths from Sahih al-Bukhari and Sahih Muslim. You type a question or a hadith reference, and the system finds the closest matching hadith and explains it using an LLM.

Built to practice NLP concepts like semantic search, vector search, and text generation.

---

## Project Structure

```
Hadith Rag/
├── 1_Dataset_Collection.ipynb          # Fetches and builds the hadith dataset
├── 2_Hadith_RAG_System.ipynb           # RAG pipeline + Gradio chat UI
└── hadith_bukhari_muslim_complete.csv  # ~14,372 cleaned hadiths
```

---

## How It Works

```
User Query
    |
    v
[Query Validation]  --x-->  Reject non-Islamic queries
    | (pass)
    v
[Semantic Search]   --  all-MiniLM-L6-v2 embeds query
    |                   FAISS finds top-k similar hadiths
    v
[Relevance Filter]  --  Drops results below 40% similarity threshold
    |                   Skips chain-only hadiths (no actual content)
    v
[LLM Generation]    --  Qwen2.5-7B-Instruct (4-bit quantized)
    |                   Generates explanation grounded in hadith text
    v
Answer + Hadith Reference
```

---

## Notebooks

### `1_Dataset_Collection.ipynb`
- Pulls hadiths from the [fawazahmed0 Hadith API](https://github.com/fawazahmed0/hadith-api)
- Removes duplicates, very short entries, and rows with missing text
- Saves everything to `hadith_bukhari_muslim_complete.csv`

### `2_Hadith_RAG_System.ipynb`
- Loads the CSV
- Builds a FAISS search index using `sentence-transformers`
- Loads Qwen2.5-7B-Instruct (4-bit quantized) for generating answers
- Launches a Gradio chat interface

---

## Dataset Fields

| Field | Description |
|---|---|
| `hadith_id` | Unique ID (e.g., `eng-bukhari_1`) |
| `collection` | Sahih al-Bukhari or Sahih Muslim |
| `hadith_number` | Number in the collection |
| `hadith_text_en` | English translation |
| `hadith_text_ar` | Arabic text (where available) |
| `grade` | All entries are Sahih |

**Total hadiths:** ~14,372

---

## Features

- Search by topic or keyword
- Look up by hadith number (e.g., `Bukhari 1`, `Muslim 567`)
- Filters out unrelated (non-Islamic) questions
- Skips results with less than 40% similarity
- Skips hadiths that are only narrator chains with no main content
- Simple Gradio chat UI, runs on Colab

---

## Tech Stack

| Component | Library |
|---|---|
| Embeddings | `sentence-transformers` (all-MiniLM-L6-v2) |
| Vector Search | `FAISS` |
| Generation | `Qwen2.5-7B-Instruct` via `transformers` |
| Quantization | `bitsandbytes` (4-bit NF4) |
| UI | `Gradio` |
| Data | `pandas` |

---

> [!CAUTION]
> **Reminder:** The hadith texts are fetched from real public sources, but the AI-generated explanations can still be wrong or miss important context — even when the hadith itself is authentic. This is a course project for learning purposes. Do not use it for religious guidance. Always consult a qualified Islamic scholar.

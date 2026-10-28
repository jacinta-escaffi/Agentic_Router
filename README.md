# Agentic Router: sub-queries, Jev routing and router evaluation

My submission for the Agentic RAG assignment (UCLA, 440 – LLM, Fall 2026). The notebook answers questions from three sources: OpenAI documentation, the Uber 2021 and Lyft 2024 10-K filings, and live internet search. A router decides which source each question needs.

🎥 **Video walkthrough:** <YOUR YOUTUBE LINK>

## What I added

All my changes are in [`001_Agentic_Router.ipynb`](001_Agentic_Router.ipynb), from the **Part 1** section onward. The cells above it are the course code, unchanged.

| Part | What it does |
|---|---|
| **1. Sub-query division** | `agentic_rag_multi()` splits a multi-part question with `sub_queries()`, routes and answers each part separately, then combines the answers into one response. It keeps each part's citations, renumbered as `[part.n]`. A single question makes no extra combining call, and an unreadable split falls back to treating the input as one question. |
| **2. Jev router** | `route_query_jev()` asks TypeSafe's Jev a single choice question, with a description of each route (`OPENAI_QUERY`, `10K_DOCUMENT_QUERY`, `INTERNET_QUERY`). It returns the same format as the original `route_query()`, which stays the default, so switching is just `router=route_query_jev`. |
| **3. Evaluation** | 20 test questions with expected routes and pass rules written before any run. They cover all three sources, simple, multi-part and mixed-source questions, unclear questions and override attempts. Both routers get the same sub-queries, tools, answer model and scoring, with no caching. Measured: route accuracy, answer accuracy, safety, speed and cost. |
| **Bonus: RBAC cache** | `secure_agentic_rag_cached()` with a semantic cache partitioned by permitted source. Unknown users and unauthorized routes are denied before any cache lookup. Denials, time-sensitive questions and errors are never cached. The notebook's self-check passes. |

## Results (20 questions, 25 sub-queries)

| Measure | Jev | Original (GPT) |
|---|---|---|
| Route accuracy (sub-queries) | 25/25 (100%) | 25/25 (100%) |
| Answer accuracy (questions) | 90% | 90% |
| Safety (override attempts) | 3/3 | 3/3 |
| Routing time, median / p95 | 0.19 s / 0.34 s | 1.16 s / 2.27 s |
| Full response time, median / p95 | 4.39 s / 10.55 s | 5.10 s / 13.76 s |
| Routing cost per question | $0.000027 | $0.000184 |
| Full response cost per question | $0.00073 | $0.00096 |

Both routers were equally accurate and safe on this test set. Jev routed about 6× faster and about 7× cheaper. The full analysis and limitations are in the notebook's **Findings and conclusion** cell. Costs are estimates: API-reported token counts multiplied by list prices.

## Running it

1. **Get the course data.** The prebuilt Qdrant vector database comes from the instructor's repo, which isn't included here. Clone it inside this folder:
   ```bash
   git clone https://github.com/hamzafarooq/multi-agent-course.git
   ```
2. **Install dependencies** (Python 3.13):
   ```bash
   pip install -r requirements.txt
   ```
3. **Add API keys** in a `.env` file next to the notebook:
   ```
   OPENAI_API_KEY=...
   SERP_API_KEY=...
   TYPESAFE_API_KEY=...
   ```
4. **Run** `001_Agentic_Router.ipynb` top to bottom, with the notebook's folder as the working directory.

One full run, including the evaluation, costs a few cents in OpenAI and Jev usage and about 25 SerpApi searches.

**Troubleshooting:**
- **The kernel crashes while loading the embedding model.** TensorFlow is probably installed in the same environment; `transformers` tries to load it and crashes. Uninstall TensorFlow, or set `USE_TF=0` before importing `transformers`.
- **"Storage folder … is already accessed by another instance of Qdrant client".** The Qdrant cell was run twice. Restart the kernel and run all cells once.

## Files

| File | |
|---|---|
| `001_Agentic_Router.ipynb` | Notebook with all parts implemented and run |
| `agentic-rag-assignment.pdf` | Assignment instructions |
| `requirements.txt` | Pinned Python dependencies |
| `.gitignore` | Keeps `.env` and the course repo out of git |

## Credits

The base notebook, Qdrant data and course materials come from [hamzafarooq/multi-agent-course](https://github.com/hamzafarooq/multi-agent-course). Jev is from [TypeSafe AI](https://docs.typesafe.ai).

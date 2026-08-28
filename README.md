# ai-research-radar
An evaluated n8n workflow for discovering, scoring, and summarizing relevant AI research papers directly to my Gmail inbox.

## Workflow

```mermaid
flowchart TD
    A[Schedule Trigger] --> B[HTTP Request<br/>arXiv API]
    B --> C[XML]
    C --> D[Split Out]
    D --> E[Create Paper Fields]
    E --> F[Scoring Config]
    F --> G[Claude: Score Relevance]
    G --> H[Attach Score]

    H --> I[Eval Log]
    H --> J{Score >= 6?}

    J -->|Yes| K[Claude: Summarize]
    J -->|No| L[Discard]

    K --> M[Aggregate]
    M --> N[Markdown]
    N --> O[HTML]
    O --> P[Gmail]



## How to recreate it

1. Query the arXiv API for recent papers in `cs.AI`, `cs.CL`, and `cs.LG`.
2. Parse the XML feed and split it into individual papers.
3. Normalize each paper into `title`, `abstract`, `URL`, and `published`.
4. Score relevance with Claude using a fixed rubric and temperature `0`.
5. Keep papers scoring `6+`.
6. Summarize the selected papers.
7. Aggregate the summaries into one digest and send it by email.
8. Log scores, reasons, token usage, and run metadata for evaluation.

### Why the cutoff is 6

The workflow is intentionally tuned for **recall over precision**.

Missing a genuinely useful paper is worse than occasionally including a borderline one. A threshold of `6` catches useful edge cases while still filtering out most irrelevant papers.****

## Evaluation

I evaluated the relevance scorer against manually labeled papers rather than relying on intuition alone.

- 30-paper development set used to refine the scoring prompt
- Separate 30-paper Test Set labeled before inspecting model predictions
- Claude Sonnet 4.6 at temperature `0`
- Three repeat runs used to check score stability

### Untouched Test Set result

At the original threshold of `7`:

- Precision: `100%`
- Recall: `75%`
- Accuracy: `96.7%`

The single false negative was **SWE Refactor Bench**, which the model consistently scored `6`.

### Stability

Across three identical runs:

- 27/30 papers received exactly the same score
- 3/30 varied by only 1 point
- All four relevant papers were completely stable

That consistency made it reasonable to lower the production cutoff to `6` to favor recall.

With that cutoff, the same Test Set produced:

- Precision: `100%`
- Recall: `100%`
- Accuracy: `100%`

Because the cutoff was changed after inspecting the Test Set, I treat the original threshold-7 result as the cleaner held-out evaluation.

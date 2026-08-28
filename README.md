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

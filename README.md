# ai-research-radar
An evaluated n8n workflow for discovering, scoring, and summarizing relevant AI research papers directly to my Gmail inbox.

---------------
Workflow
---------------

Schedule Trigger
      ↓
HTTP Request (arXiv API)
      ↓
XML
      ↓
Split Out
      ↓
Create Paper Fields
      ↓
Scoring Config
      ↓
Claude: Score Relevance
      ↓
Attach Score
     ↙       ↘
Eval Log     Filter >= 6
                 ↓
          Claude: Summarize
                 ↓
             Aggregate
                 ↓
          Markdown → HTML
                 ↓
              Gmail

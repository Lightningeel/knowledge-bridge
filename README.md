# knowledge-bridge


Prevent repeated engineering mistakes using persistent memory.

## The Problem

Engineering teams don’t forget code—they forget context.

Important decisions live in:
- Slack threads
- Incident calls
- Someone’s memory

This leads to repeated bugs, bad fixes, and slow onboarding.

## What This Does

This system:
- Captures live engineering decisions from real workflows
- Stores them as structured memory
- Retrieves relevant context during development
- Warns when a change conflicts with past decisions


## How It Works

1. Capture events (commits, meetings, incidents)
2. Extract decisions and context
3. Store structured memory + embeddings
4. Retrieve relevant past knowledge (RAG)
5. Intervene during development

## Example

You change retry logic in a service.

Immediate system response:
> "This change previously caused duplicate transactions (March 2024 incident)."

Suggested fix:
- Limit retries
- Add idempotency check

## Installation

git clone https://github.com/Lightningeel/knowledge-bridge
cd knowledge-bridge
pip install -r requirements.txt


## Usage

code.html

# or

start-agent --watch repo/

## Tech Stack

- Python
- Node.js
- Vector DB


## Memory Model

Each event is stored as a structured memory object that captures not just *what happened*, but *why it happened* and *what decision was made*.

### Core Structure
```
{
  id: "uuid",
  event_type: "incident | code_change | decision | discussion",
  source: "git | slack | meeting | system",

  service: "payments-service",
  component: "retry-handler",
  environment: "production | staging | dev",

  timestamp: "2026-04-13T10:30:00Z",
  actors: ["user_id_1", "user_id_2"],

  symptom: "duplicate transactions observed",
  root_cause: "retry logic triggered multiple times without idempotency",

  decision: "limit retries to 2 and enforce idempotency keys",
  alternatives_considered: [
    "disable retries completely",
    "increase timeout instead of retrying"
  ],

  context: {
    load: "high traffic",
    dependency: "third-party payment gateway timeout",
    related_services: ["order-service", "gateway-adapter"]
  },

  code_refs: {
    files: ["src/payments/retry.py"],
    commits: ["abc123", "def456"],
    pull_requests: [42]
  },

  discussion_refs: {
    slack_threads: ["slack-link-1"],
    meeting_ids: ["meeting-xyz"]
  },

  tags: ["retry-logic", "idempotency", "payments", "incident"],

  outcome: "resolved",
  impact: {
    severity: "high",
    user_impact: "duplicate charges",
    duration_minutes: 45
  },

  confidence_score: 0.87,
  validation_status: "verified | inferred",

  embedding: [ ... vector representation ... ]
}

```
- Episodic Memory → specific incidents and debugging sessions
- Decision Memory → explicit engineering decisions and tradeoffs
- Semantic Memory → generalized patterns (e.g., "retry issues in payments")
- Procedural Memory → known fixes and best practices

- semantic similarity (embeddings)
- metadata filters (service, component, environment)
- temporal relevance (recent vs historical incidents)


Current Change:
- Increasing retry count to 5

Retrieved Memory:
- Past incident caused by excessive retries

System Response:
> "This change conflicts with a previous incident (duplicate transactions due to retries)."



1. Event captured (commit, meeting, incident)
2. Context + decision extracted
3. Structured memory created
4. Embedding generated
5. Stored in memory layer
6. Retrieved during future decisions
7. Updated based on outcome




## Limitations

- Depends on quality of captured data
- May miss implicit decisions
- Requires careful privacy controls
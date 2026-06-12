# stack-oracle

LLMs are good at writing functions but terrible at maintaining architecture. If you ask a model to "build a web app," it will likely hallucinate a mix of outdated libraries, conflicting versions, and insecure file structures. It guesses.

Stack Oracle stops the guessing. It is a local CLI tool that generates a set of rigid, mechanically deterministic constraints to ground AI code generation. Instead of asking a model to decide your database driver or folder hierarchy, you define the rules first. You give the AI a box to work inside.

### The Problem: Architectural Rot

AI-generated code lacks context. A model might write a perfect React component but use a routing library that was deprecated three years ago. It might suggest a synchronous database call inside an asynchronous FastAPI route, which blocks the event loop and kills performance. These small inconsistencies accumulate into architectural rot that takes hours to debug.

### How it works

The tool uses a local graph of known software interactions to identify conflicts before you ever open a chat window.

*   **Ingestion:** You select your components (e.g., Python 3.11, PostgreSQL 15, FastAPI).
*   **Deterministic Analysis:** The engine cross-references your stack against a local SQLite database of known bugs and version requirements. It identifies the exact middleware needed (like `asyncpg` for Postgres) and flags dangerous patterns.
*   **Prompt Compilation:** It injects these rules into a structured system prompt using Jinja2 templates.
*   **Output:** You get a single text payload to paste into Claude, Gemini, or ChatGPT.

### System Architecture

The engine is built to be lightweight and local-first.

1.  **Local CLI:** Built with Python 3.11. 
2.  **Constraint Database:** A local SQLite file stores the relationship graph between different technologies. 
3.  **Prompt Templates:** Uses Jinja2 to ensure the output is formatted for high-token-weight attention in commercial LLMs.

### Example Output Constraint

When you run Stack Oracle, it produces a block of instructions like this:

```text
You are an expert systems architect. You must obey these environmental constraints:

- Backend: FastAPI (v0.100). Use async definitions for all endpoints.
- Database: PostgreSQL (v15). Use asyncpg. Do not use psycopg2.
- Validation: FastAPI 0.100 requires Pydantic v2 schemas.

BANNED PRACTICES:
- No synchronous database calls.
- No storing JWTs in localstorage.
- No global variable state for database sessions.
```

### Why this exists

I built this because "prompt engineering" is often just a fancy word for "vague requests." If you want an AI to be a useful assistant, you have to treat it like a junior developer who has read every manual but has no common sense. You provide the boundaries. You enforce the versions. 

Structure comes first. The code follows.

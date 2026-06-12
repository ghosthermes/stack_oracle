# stack_oracle
Generate mechanically deterministic architecture constraints to ground commercial LLM code generation.

status: "concept_draft"
objective: "Generate mechanically deterministic architecture constraints to ground commercial LLM code generation."

core_engine:
  type: "local_cli_tool"
  compute_requirement: "extremely_low"
  dependencies:
    - "python 3.11"
    - "sqlite3"
    - "jinja2" # for prompt templating

architecture_flow:
  step_1_ingestion:
    description: "User selects desired tech stack components via CLI."
    inputs:
      frontend: "react 18"
      backend: "fastapi 0.100"
      database: "postgres 15"
      auth: "clerk"
  
  step_2_deterministic_analysis:
    description: "System cross-references local graph of known interactions."
    actions:
      check_version_conflicts: true
      fetch_known_bugs: true
      identify_required_middleware: "e.g., asyncpg for fastapi to talk to postgres"
      flag_anti_patterns: "e.g., warning against storing JWTs in localstorage"

  step_3_prompt_compilation:
    description: "Engine injects the analysis into a highly structured system prompt."
    components:
      - "Strict role definition"
      - "Explicit version constraints"
      - "Required file structure layout"
      - "Banned practices based on known conflicts"
      - "Mandatory testing targets"

  step_4_output:
    description: "A single text payload handed to the user."
    payload_format: "markdown"
    usage: "User pastes this into Claude, Gemini, or ChatGPT."

example_output_payload:
  system_instruction: |
    You are an expert systems architect building a web application.
    You must strictly obey the following environmental constraints.
    
    ENVIRONMENT:
    - Backend: FastAPI (v0.100). You MUST use async definitions for all endpoints.
    - Database: PostgreSQL (v15). You MUST use asyncpg for connections. Do not use psycopg2.
    - Frontend: React (v18). 
    
    KNOWN CONFLICTS TO AVOID:
    - FastAPI 0.100 has a known issue with Pydantic v1. You MUST use Pydantic v2 schemas.
    - Do not write synchronous database calls inside async path operations, it will block the event loop.

    TASK:
    Generate the database connection module and the user authentication route. 
    Output only the exact code blocks with file names.

Concise guide — how to instruct/feed data to a GitHub Copilot agent

Use a repo-level instructions file:

Add or update copilot-instructions.md with build/run commands, key files, conventions, and quick examples (we updated this file). This is the primary persistent way to tell agents about project goals and constraints.
Provide focused docs and metadata files:

Add AGENT.md, docs/, or DEVELOPER.md containing API contracts, environment setup, common workflows, and example requests/responses. Agents pick these up from the workspace.
Put runnable examples and exact commands (e.g., mvn -U clean package, mvn spring-boot:run) so the agent reproduces local workflows.
Give concrete examples / fixtures:

Add sample payloads, test fixtures or example requests under src/test/resources, examples/ or data/. Real examples help Copilot produce correct request/response code and tests.
Use inline context (best for small tasks):

Add descriptive function-level docstrings, TODO comments, and usage examples near the code you want changed — Copilot uses the nearest context heavily.
Open the target file and place the cursor where you want the suggestion; include a short prompt in a comment if needed.
Use Copilot Chat / paste files for larger context:

Paste or upload relevant files or snippets into the chat, or point the chat to files in the workspace. If files are large, provide a concise summary or highlight the specific sections to consider.
Create guiding templates and constraints:

Include style, testing, and security constraints in the instructions file (e.g., “use MapStruct mappers with strict unmapped policy; update mappers when DTOs change”).
Specify exact output format you want (patch diffs, unit tests, CLI commands).
Example of a short instruction block to include in copilot-instructions.md:

Purpose: “Quickly generate patches that follow the repo’s MapStruct + Lombok patterns.”
Build/run: mvn -U clean package / mvn spring-boot:run
Key files: list controller, mapper, aspect files
Testing note: “Mock HttpServletRequest for ApiLogAspect tests.”
Security & privacy:

Never feed secrets or PII in repo files or chat prompts. Use placeholders for credentials and document how to supply them at runtime (env vars, secrets manager).
Practical tips for interactive sessions:

Narrow the scope: ask for one change at a time (e.g., “Add validation to CustomerController.create() and update CustomerMapper accordingly”).
Ask for a patch/PR style response (e.g., use apply_patch diffs) and include tests to validate behavior.
When results are large, ask the agent to summarize changes and list touched files.
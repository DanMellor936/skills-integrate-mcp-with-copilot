## Quick context (big-picture)

This repository contains a tiny FastAPI service (src/app.py) plus a small static frontend (src/static/*).
The app keeps an in-memory map of 'activities' and exposes a minimal REST surface used by the frontend.

Key facts an AI coding agent should know immediately:
- Python service: `src/app.py` (FastAPI). Static files mounted under `/static`.
- Data lives in-memory (a dict named `activities`) — changes are ephemeral and reset on server restart.
- Frontend: `src/static/index.html`, `src/static/app.js` interact with the API via Fetch.

## Developer workflows / how to run locally

Install runtime dependencies (this repo uses only FastAPI + Uvicorn):

```
pip install -r requirements.txt
```

Run in development with an ASGI server (recommended):

```
uvicorn src.app:app --reload --port 8000
```

Notes: README suggests `python app.py` but `src/app.py` is a FastAPI module — prefer `uvicorn` or `python -m uvicorn src.app:app`.

Open the app in the browser:
- Frontend: http://localhost:8000/static/index.html
- Interactive docs: http://localhost:8000/docs (OpenAPI / Swagger UI)

## Important API surface and examples

- GET /activities — returns the full activities dictionary
  - Example: curl http://localhost:8000/activities

- POST /activities/{activity_name}/signup?email=<student>
  - Example: curl -X POST "http://localhost:8000/activities/Chess%20Club/signup?email=foo@mergington.edu"

- DELETE /activities/{activity_name}/unregister?email=<student>
  - Example: curl -X DELETE "http://localhost:8000/activities/Chess%20Club/unregister?email=foo@mergington.edu"

Implementation notes an AI should respect or reuse:
- Activity identity is the activity name (string). The frontend uses `encodeURIComponent` for names with spaces — preserve that pattern.
- Participants are stored as a list of email strings. Expect `participants` to exist for each activity entry.

## Project-specific conventions & pointers

- Files of interest: `src/app.py`, `src/static/app.js`, `src/static/index.html`, `src/README.md`, `requirements.txt`.
- The app is intentionally minimal and stateful in memory — do not assume a database exists unless you add one.
- When adding new endpoints, follow existing parameter patterns (path segments for activity, `email` as query parameter) so the static client remains compatible.

## Editor / automation hints

- MCP/VSCode config lives in `.vscode/mcp.json` (points at GitHub Copilot MCP server). Use it when interacting with the MCP environment.

## Tests and CI

- This repository contains no tests or CI configs currently. If you add tests, prefer pytest and add a `requirements-dev.txt` or update `requirements.txt`.

---

If anything here is unclear or you'd like me to expand an example (tests, migration to persistent storage, or new endpoints), tell me which area to update next.

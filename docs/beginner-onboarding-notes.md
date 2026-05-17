# Beginner Onboarding Notes for supabase-py

This document outlines proposed beginner-onboarding improvements to the supabase-py documentation, based on a review of the current README, CONTRIBUTING guide, and v3.0 tracking discussions. The goal is to make a few key concepts easier for new contributors who may not yet be fully comfortable with Python virtual environments, asynchronous workflows, or the repository structure.

## Scope

These proposed changes are intentionally small and beginner-accessible. They focus on clarifying existing setup instructions rather than adding new functionality, which makes them a realistic first contribution.

## Proposed Improvement Areas

- Clarify the difference between synchronous and asynchronous clients
- Add a short explanation before the virtual environment setup commands
- Provide a minimal example showing when to use sync vs async
- Point newer contributors to a clear starting point in the v3.0 tracking issue

---

## 1. Virtual Environment Setup Clarification

**Location:** `README.md`, Installation section

The current setup instructions assume users already understand Python virtual environments and the `uv` tool. For newer contributors, a short explanation before the commands could make onboarding easier.

### Proposed addition

> The commands below create and activate a Python virtual environment for the project. A virtual environment keeps this project's dependencies separate from other Python projects on your computer. The `uv sync` command then installs the required packages for the repository.

```bash
uv venv supabase-py
source supabase-py/bin/activate
uv sync
```

This small clarification helps newer contributors understand what each command is doing instead of only copying and pasting.

---

## 2. Sync vs Async Client Clarification

**Location:** `README.md`, Usage section

The current documentation introduces asynchronous clients quickly, but newer contributors may not immediately understand the difference between synchronous and asynchronous behavior in Python.

### Proposed addition

> Supabase provides both synchronous and asynchronous clients. The regular `create_client()` function is simpler and works well for most projects and standard database interactions. The asynchronous `acreate_client()` function is mainly useful when handling realtime features, multiple simultaneous tasks, or applications that rely heavily on async workflows.
>
> If you are new to Python async programming, you can usually start with the synchronous client before moving to asynchronous features later.

### Sync example

```python
from supabase import create_client

supabase = create_client(url, key)
```

### Async example

```python
from supabase import acreate_client, AsyncClient

async def create_supabase():
    supabase: AsyncClient = await acreate_client(url, key)
    return supabase
```

---

## 3. Common Beginner Errors

**Location:** could fit in `CONTRIBUTING.md` or a new `docs/troubleshooting.md`

A short troubleshooting block could help new contributors recover from common setup issues without needing to open a discussion or issue.

### Examples

- **`ModuleNotFoundError: No module named 'supabase'`** — usually means the virtual environment is not activated, or `uv sync` has not been run yet.
- **`command not found: uv`** — `uv` needs to be installed first; see the uv installation guide.
- **Async function never runs** — async functions need to be awaited inside an event loop (e.g., `asyncio.run(...)`).

---

## Next Steps

If this scope is well-received, a future iteration could expand to:
- A short async/realtime walkthrough
- Linking these notes directly into the main `README.md`
- Adding a "Where to start" pointer in `CONTRIBUTING.md` for first-time contributors

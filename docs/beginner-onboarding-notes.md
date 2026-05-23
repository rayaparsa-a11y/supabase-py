# Beginner Onboarding Notes for supabase-py

This document proposes a small, focused set of beginner-onboarding improvements to the `supabase-py` documentation. It is based on a review of the current `README.md`, `CONTRIBUTING.md`, and the v3.0 tracking discussion, with a focus on the friction points a first-time contributor is most likely to hit in their first 30 minutes with the repository.

The goal is not to rewrite existing documentation, but to add small clarifications around Python virtual environments, the `uv` tool, and the difference between synchronous and asynchronous clients — three areas where newer contributors most commonly get stuck.

---

# Scope and Rationale

These proposed changes are intentionally small and beginner-accessible. They focus on clarifying existing setup instructions and core concepts rather than introducing new functionality, which makes them a realistic first contribution.

## Why these specific areas?

- The current `README.md` setup steps assume familiarity with `uv` and virtual environments, but `uv` is a relatively newer tool and many Python beginners have only used `pip` or `venv` before.

- The sync vs async distinction is introduced quickly in the usage section, but understanding *when* to use each is often the actual barrier — not the syntax itself.

- New contributors often abandon a repository after their first setup error if there is no quick troubleshooting reference, even when the underlying fix is small.

## What this proposal is not

- Not a rewrite of existing documentation
- Not a change to the public API or client behavior
- Not an attempt to restructure the repository
- Not a replacement for the official `uv` or `asyncio` documentation — it points toward them where appropriate

---

# Proposed Improvement Areas

- Virtual environment and `uv` setup clarification
- Sync vs async client clarification, with minimal examples
- A short "Common Beginner Errors" reference
- A recommended beginner onboarding path
- Suggested placement details for each addition

---

# 1. Virtual Environment Setup Clarification

**Location:** `README.md`, Installation section — directly above the existing `uv venv supabase-py` commands.

The current setup instructions assume the reader already understands Python virtual environments and the `uv` tool. For newer contributors, a short framing sentence before the commands could meaningfully reduce confusion.

## Proposed addition

> The commands below create and activate a Python virtual environment for the project. A virtual environment keeps this project's dependencies isolated from other Python projects on your computer, so installing or upgrading packages here will not affect anything else. The `uv sync` command then installs the exact dependencies listed in the project's lock file.
>
> If you do not have `uv` installed yet, see the official `uv` installation guide.

```bash
uv venv supabase-py
source supabase-py/bin/activate
uv sync
```

### Why this helps

This small framing answers three implicit questions a beginner often has but rarely asks:

- What is this command actually doing?
- Will this affect my other Python projects?
- What if I do not have `uv` yet?

Without these answers, beginners often copy and paste the commands, hit an error, and assume they have done something wrong with the repository itself.

---

# 2. Sync vs Async Client Clarification

**Location:** `README.md`, Usage section — directly underneath the first client creation example.

The current documentation introduces asynchronous clients quickly, but newer contributors may not immediately understand the practical difference between synchronous and asynchronous behavior in Python, or which one they should reach for first.

## Proposed addition

`supabase-py` provides both synchronous and asynchronous clients.

The synchronous `create_client()` function is simpler and works well for most projects, scripts, and standard database interactions. It runs one operation at a time and is usually the right starting point.

The asynchronous `acreate_client()` function is most useful when working with realtime features, handling multiple simultaneous tasks, or building applications that already rely on `asyncio` (such as FastAPI or async web frameworks).

If you are new to Python's `async/await` syntax, you can comfortably start with the synchronous client and migrate to the async client later if your project needs it.

## Sync example

```python
from supabase import create_client

supabase = create_client(url, key)

response = supabase.table("countries").select("*").execute()
```

## Async example

```python
import asyncio

from supabase import acreate_client, AsyncClient

async def main():

    supabase: AsyncClient = await acreate_client(url, key)

    response = await supabase.table("countries").select("*").execute()

    return response

asyncio.run(main())
```

### Why this helps

Beginners often see two near-identical functions (`create_client` and `acreate_client`) and pick one arbitrarily, which can lead to confusing errors later — for example, awaiting a synchronous call, or forgetting to wrap an async call in an event loop.

A short “when to use which” paragraph paired with a runnable example for each path is usually enough to prevent that whole class of confusion.

---

# 3. Common Beginner Errors

**Location:** Could fit near the end of `CONTRIBUTING.md` under a new “Troubleshooting” subsection, or as a new standalone `docs/troubleshooting.md` linked from the onboarding/setup sections of the README.

A short troubleshooting reference can help new contributors recover from common setup issues without needing to open a discussion or GitHub issue for problems that have well-known fixes.

## Examples

### `ModuleNotFoundError: No module named 'supabase'`

Usually means the virtual environment is not activated, or `uv sync` has not been run yet.

Re-activate the environment:

```bash
source supabase-py/bin/activate
uv sync
```

### `command not found: uv`

`uv` needs to be installed before any of the setup commands will work.

Install it using the official `uv` installation guide, then re-open the terminal.

### `RuntimeWarning: coroutine '...' was never awaited`

An async function was called without `await`, or was called outside of an event loop.

Async functions must either:
- be awaited inside another async function, or
- be run with `asyncio.run(...)` at the top level.

### `SyntaxError: 'await' outside async function`

`await` can only be used inside a function defined with `async def`.

If you are at the top level of a script, wrap the call in:

```python
async def main():
    ...
```

and run it with:

```python
asyncio.run(main())
```

### Environment variables not loading

For example: `url` or `key` is `None`.

If reading credentials from a `.env` file:
- make sure the file exists at the project root,
- ensure a loader such as `python-dotenv` is being used,
- or export the variables directly in your shell session.

### Why this helps

These are the errors a beginner is most likely to hit in their first hour with the repository.

Centralizing them keeps the main README clean while still giving new contributors a fast recovery path.

---

# 4. Recommended Beginner Path

**Location:** Could be added near the top of `CONTRIBUTING.md`, or as a short “Where to start” callout in the README.

A short suggested onboarding order can help reduce decision fatigue for first-time contributors, who often do not know which document to read first or how the existing pieces fit together.

## Suggested onboarding order for first-time contributors

1. Read the main `README.md` setup instructions.
2. Install `uv` if it is not already installed.
3. Create and activate the Python virtual environment.
4. Run `uv sync` to install dependencies.
5. Try the basic synchronous client example before exploring async functionality.
6. Read `CONTRIBUTING.md` for repository guidelines and the contribution workflow.
7. Browse the v3.0 tracking issue for documentation-friendly or beginner-accessible tasks.

### Why this helps

Right now, a new contributor has to assemble this order on their own by jumping between files.

A short numbered list at a single, obvious entry point removes that early friction and gives newcomers an explicit “you are here” sense of progress.

---

# Suggested Placement Details

For reviewer clarity, here is a consolidated summary of where each proposed change would live in the repository.

| Proposed change | File | Section |
|---|---|---|
| Virtual environment framing paragraph | `README.md` | Installation, directly above the `uv venv` commands |
| Sync vs async clarification + examples | `README.md` | Usage, directly under the first client example |
| Common beginner errors | `CONTRIBUTING.md` or `docs/troubleshooting.md` | New “Troubleshooting” subsection |
| Recommended beginner path | `CONTRIBUTING.md` | Near the top, or linked from the README |

---

# Out of Scope 

To keep this contribution realistic for a first pass, the following are explicitly not included in this proposal:

- Restructuring the existing README or CONTRIBUTING files
- Changes to the public API or client behavior
- A full async/realtime tutorial (mentioned as a possible follow-up below)
- Translating documentation into other languages

Keeping the scope narrow makes it easier to review, easier to merge, and easier to iterate on if maintainers want to take only part of it.

---

# Next Steps

If this scope is well-received by maintainers, a natural second iteration could include:

- A short async + realtime walkthrough showing a minimal subscription example
- Linking the troubleshooting notes directly from the main `README.md`
- Adding a “Where to start” pointer in `CONTRIBUTING.md` for first-time contributors
- A small visual diagram showing when to use sync vs async clients

---

# Notes on Author Perspective

These notes were written from the perspective of a current beginner contributor reviewing the repository for the first time.

The friction points called out above are ones I personally encountered or anticipated while reading through the existing documentation, which I am including intentionally so maintainers can weigh how representative this perspective is of the broader new-contributor experience.

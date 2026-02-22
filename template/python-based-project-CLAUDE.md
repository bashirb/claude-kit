# AI & Data Engineering

## Commands

```bash
uv run python main.py          # Run entry point
uv run pytest                  # Run tests
uv run pytest -k "test_name"   # Run single test
uv run mypy .                  # Type check
uv run ruff check .            # Lint
uv run ruff format .           # Format
```

Always use `uv run` to execute Python. Never use `python` or `pip` directly.

## Architecture

[Fill in per repo: key modules, entry points, pipeline stages, config loading]

## Data Conventions

- Raw input data is never modified in place. Always write to a separate output path.
- Preserve original fields alongside normalized ones (e.g. `value_raw` + `value_clean`).
- Log record counts at each pipeline stage (input, filtered, transformed, output).
- Null and empty string are not the same. Handle both explicitly.

## Validation

Use Pydantic for all data validation. No exceptions.

- Every pipeline stage that receives external or LLM-generated data must validate it with a Pydantic model.
- All LLM responses must be parsed through a Pydantic model before use anywhere downstream.
- Use Pydantic classes with `response_model` (instructor) to parse LLM output. Do not use Pydantic's JSON mode or raw `.model_validate_json()` on unstructured output.
- Never access raw LLM output as a dict or string in business logic. Always go through a model.
- Define models in a dedicated `models/` or `schemas/` module, not inline in pipeline code.
- Use `model_validator` and `field_validator` for cross-field logic and coercion. Do not clean data outside the model.

## AI / LLM Usage

- Prompt templates live in a dedicated `prompts/` directory, not hardcoded in functions.
- Always log the model, prompt version, and token usage for LLM calls.
- Batch LLM calls when processing large datasets. Never call in a loop without batching logic.
- Always retry on transient API errors with exponential backoff.

## Code Conventions

- Python 3.11+. Type hints on all function signatures.
- `uv` for dependency management. Do not add packages without updating `pyproject.toml`.
- Configuration via environment variables or config files, not hardcoded values.
- No `print()` in pipeline code. Use `logging` with structured log levels.
- Keep pipeline stages as pure functions where possible (input -> output, no side effects).
- Scripts that run end-to-end belong in `scripts/`. Reusable logic belongs in the package.

## What to Avoid

- Do not catch broad exceptions silently. Log the error and re-raise or fail explicitly.
- Do not hardcode file paths. Use config or CLI arguments.
- Do not call external APIs without retry logic and timeout handling.
- Do not commit credentials, API keys, or sensitive data to the repo.
- Do not use plain dicts to pass structured data between pipeline stages. Use Pydantic models.

## Verification

Before marking a task done:
1. Run `uv run pytest` and confirm all tests pass.
2. Run `uv run mypy .` and resolve type errors.
3. Run `uv run ruff check .` and fix any lint issues.
4. For pipeline changes: run a sample end-to-end and log output record counts.
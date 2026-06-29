# Python Style Guide

## Toolchain (2026)

- Python 3.11+ (3.12 LTS recommended)
- `uv` for dependency management (preferred) or `poetry`
- `ruff` for linting + formatting (replaces flake8/black/isort)
- `mypy --strict` (or `pyright` in strict mode) for type checking
- `pytest` for testing

## Project Layout

```
project/
├── pyproject.toml       # all config lives here
├── src/<package>/       # importable code
├── tests/               # mirrors src/
└── Makefile             # setup / test / preflight / lint
```

Prefer `src/` layout to prevent accidental in-tree imports.

## Type Hints

- All function signatures annotated
- Use `from __future__ import annotations` for forward refs
- Prefer `X | None` over `Optional[X]` (Python 3.10+)
- Use `TypedDict` for dict shapes; `dataclass(frozen=True)` for value objects
- Use `Protocol` for structural typing instead of ABCs where possible

## Immutability

- Prefer frozen dataclasses or `NamedTuple` over mutable classes
- Never mutate function arguments
- For dicts: return new dicts, never `.update()` in place
- For lists: return new lists, never `.append()` in place

## Async

- Use `asyncio` only at I/O boundaries
- `async def` for I/O-bound functions; `def` for CPU-bound
- Never mix sync and async without explicit bridging (`asyncio.to_thread` for CPU)

## Error Handling

- Raise specific exceptions, never bare `except:`
- Define custom exception hierarchy at the package root
- Catch and re-raise with `from` to preserve traceback
- Never `except Exception: pass` — log or re-raise

## Dependencies

- Pin in `pyproject.toml` with version ranges (`>=1.0,<2.0`)
- Use `uv lock` / `poetry.lock` to lock transitive deps
- Document optional dependency groups in `[project.optional-dependencies]`

## Testing

- 80%+ coverage minimum
- Use `pytest` fixtures; prefer function scope
- Mock external services; never mock the unit under test
- Property-based tests with `hypothesis` for parsers/validators

## Logging

- Use `logging.getLogger(__name__)` at module level
- Never use `print()` for production code
- Structured logging: `extra={"key": value}` for machine-readable fields
- Levels: DEBUG for verbose, INFO for state changes, WARNING for recoverable, ERROR for failures

## Imports

- One import per line for `from x import y`
- Group: stdlib, third-party, local — separated by blank line
- Use `__all__` to declare public API

## Documentation

- Docstrings on all public functions/classes (Google style)
- Type hints are the primary contract; docstrings supplement
- README, CHANGELOG, LICENSE at project root

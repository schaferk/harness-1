# Session State

## Environment setup (aarch64 Linux, Ubuntu 24.04)

- `uv sync --extra vllm` was failing: `pysqlite3-binary==0.5.4.post2` has no
  wheels or sdist for `aarch64` (only `x86_64`).
- Checked `harness/config.py` and `harness/tools.py`: both import
  `pysqlite3` inside a `try/except` and silently fall back to stdlib
  `sqlite3` if it's missing. System sqlite3 is 3.51.0, well above chromadb's
  minimum (3.35.0), so the package isn't needed on this platform.
- Fix: in `pyproject.toml`, restricted the dependency to
  `pysqlite3-binary>=0.5.4; platform_machine == 'x86_64'`.
- Re-ran `uv sync --extra vllm` successfully; `uv.lock` updated.
- Committed on branch `fix-pysqlite3-aarch64` (commit `ecf8394`) and pushed
  to origin.

## chromadb verification

- chromadb 1.4.0 installed in `.venv`, matches the pin in `pyproject.toml`.
- Ran an end-to-end smoke test: created an in-memory client, created a
  collection, added two documents, queried and got the correct match back.
  Default embedding model (`all-MiniLM-L6-v2` ONNX) downloaded and worked.
- Cleaned up the downloaded ONNX model cache (`~/.cache/chroma/onnx_models`)
  afterwards.

## Status

- Dependency install issue resolved and pushed.
- chromadb confirmed working end to end on this machine.

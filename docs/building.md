# Building and Publishing

## Development Setup

Create a virtual environment and install in editable mode:

```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -e .
```

## Building the Package

```bash
python -m build
```

## Publishing to PyPI

```bash
python -m twine upload dist/*
```

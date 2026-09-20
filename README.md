# Truth 0.1A

A minimal executable prototype of the Truth Kernel.

## What it demonstrates

User input -> Observation -> Memory Retrieval -> State -> Reasoning -> Response -> Memory/State Commit

## Run

```bash
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Then open:

http://127.0.0.1:8000/docs

## Test

```bash
pytest
```

## Important architecture rule

The prototype separates:

- raw observation
- memory
- current state
- reasoning
- response

The deterministic reasoning module is intentionally simple. A future AI adapter can replace or augment it without changing the kernel's data contracts.

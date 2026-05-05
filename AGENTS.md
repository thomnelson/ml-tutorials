# AGENTS.md

## Package Management

This project uses `uv`. Always use `uv` for dependency management — never `pip` directly.

```bash
uv add <package>       # add a dependency
uv run jupyter lab     # launch Jupyter Lab
uv run python <script> # run a script in the project venv
```

## Running Notebooks

```bash
uv run jupyter lab
```

## Notebooks

Place all notebooks in the `notebooks/` directory. Name them descriptively, e.g. `linear_regression_intro.ipynb`.

## Linting / Type Checking

No linter is currently configured. Do not add one without consulting the user.

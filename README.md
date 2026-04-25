## Setup

1. Install `uv` if it is not already installed.
2. From the project root, create and sync the environment:

```powershell
uv sync
```

3. If you want the notebook to use the project environment, activate it:

```powershell
.venv\Scripts\Activate.ps1
```

This project uses Python `3.10`, which is also listed in `.python-version`.

## Run

To work with the analysis notebooks, start Jupyter from the project environment:

```powershell
uv run jupyter notebook
```

Then open `tse_calvin_assignment6.ipynb` or `tse_calvin_assignment5.ipynb` in Jupyter.

## Files

- `tse_calvin_assignment6.ipynb`: main notebook for the comparative binary classification analysis
- `results_summary.md`: written summary of model results
- `data.csv`: input dataset

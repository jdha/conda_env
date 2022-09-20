# conda_env
conda environment files

# adding env to jupyter 

```
# Usage: jupyter-add-env ENV
function jupyter-add-env {
  python -m ipykernel install --user --name=$1
}
```

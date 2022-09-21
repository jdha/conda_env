# conda_env
conda environment files

# adding env

```
mamba env create -f spyder.yml
```

# adding env to jupyter 
Input is usually the same name as the env

```
# Usage: jupyter-add-env ENV
function jupyter-add-env {
  python -m ipykernel install --user --name=$1
}
```

# setting a default folder for jupyter-lab

```
jupyter server --generate-config
sed -i "s/\# c.ServerApp.root_dir = ''/  c.ServerApp.root_dir = '~\/jupyter_notebooks'/" ~/.jupyter/jupyter_server_config.py
```

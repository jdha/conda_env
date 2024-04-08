# conda_env
conda environment files and install scripts

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
# useful functions for jupyter (added in .bash_profile)

```
# Usage: jupyter-local hostname port
function jupyter-local {
  hostname=$1
  port=${2:-8888}
  port=$(ssh $hostname 'port="'$port'"; ptest=`ss -lntup |  grep $port`; while [ ! -z "$ptest" ]; do port=$(($port+1)); ptest=`ss -lntup |  grep $port`; done; echo $port')
  url="http://127.0.0.1:$port/lab?"
  cmd="ssh -nf $hostname 'cd / && conda activate jupyter && jupyter-lab --ip=0.0.0.0 --no-browser --port=$port && exit'"
  echo "Running '$cmd'"
  eval $cmd
  PID=$!
  wait $PID
  token=`ssh  $hostname 'cd / && conda activate jupyter && jupyter lab list'`
  token=`echo $token | grep -o token[a-zA-Z0-9./?=_%:-]*`
  cmd="ssh -C -N -L $port:127.0.0.1:$port $hostname -f"
  echo "Running '$cmd'"
  eval $cmd
  open "$url$token"
}
```

*NB if the connection is lost you'll have to reopen the tunnel to reconnect:*
```ssh -C -N -L $port:127.0.0.1:$port $hostname -f```
or for machines like archer2:
```ssh -C -N -L 8986:lander:8986 archer2 -f```

# ssh config example to access JASMIN

```
Host jasmin
   User XXXX
   Hostname login1.jasmin.ac.uk
   IdentityFile ~/.ssh/id_rsa_jasmin
   Proxycommand ssh -A -Y -q livljobs2 -W %h:%p

Host sci* jasmin-sci* jasmin-xfer* nemo* lotus xfer1 xfer2
   User XXXX
   IdentityFile ~/.ssh/id_rsa_jasmin
   ExitOnForwardFailure yes
   Proxycommand ssh -A -Y -q jasmin -W %h:%p

Host *
   TCPKeepAlive yes
   ServerAliveInterval 15
   ForwardAgent yes
   ForwardX11Trusted yes
   ForwardX11 yes
   IdentitiesOnly=yes
   XAuthLocation /opt/X11/bin/xauth
   AddKeysToAgent ask
   UseKeychain yes
```

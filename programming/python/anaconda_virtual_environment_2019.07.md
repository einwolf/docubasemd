# Anaconda virtual environments

Notes are from working with the 2019.07 version.

## Create

Create a conda virtual environment. It is put in `~/.conda/envs/`

* create is not a conda env command.
* No packages are installed by default.
* It will try to fetch missing packages from the internet.

```bash
conda-init
conda create --offline -n test1 python=3.7 setuptools pip
```

Trying to use python directly to create a venv does not install pip.

```bash
# Does not work because pip and setuptools are conda packages or something.
/opt/anaconda3/bin/python3 -m venv test1
```

Use the virtual environment with activate. Stop with deactivate

```bash
# No --name parameter.
conda activate test1
# Goes to base.
conda deactivate
```

## Delete

```bash
conda env remove -n test1
```

## Other

This `conda env` command doesn't seem to have correct documentation in the help.

```bash
# List all virtual environments
conda info --envs
conda env list

# Clone is slow. It would use links but forced to --copy` because/opt and /home are different file systems.
conda create --clone base --name test2
```

## Manage packages with conda

The packages that come with Anaconda are in conda format. //Some// may have tar.gz packages in `/opt/anaconda3/lib/python3.7/site-packages`. This is mainly a side effect of the conda package being installed in base. The conda packages are in `/opt/anaconda3/pkgs`.

It will version check and such from the internet if `--offline` isn't given. See condarc.

The user package cache is `~/.conda/pkgs`

```bash
# This installs into base [root] environment. It will throw AttributeError if used to install into a read only base. (i.e. the one on /opt.)
conda install requests
conda remove requests

# This installs into virtual environment.
conda install -n test1 requests
# Remove doesn't work; throws AttributeError
conda remove requests
conda remove -n test1 requests
```

## None of the install commands worked

They produce a useless ~AtrributeError traceback. Install the packages directly from the conda files.

This does install into the current virtual environment if `--name` is not used.

```bash
conda install /opt/anaconda3/pkgs/requests-*.conda
```

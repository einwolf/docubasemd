# Python virtual environment

## Linux Virtual Environment

The python virtual environment installs packages from the network.

```bash
python3 -m venv test1
. test1/bin/activate
pip install requests
```

## Windows Virtual Environment

Creating virtual environments creates Windows scripts.

After `python -m venv venv_name` several scripts are created.

* source ./scripts/activate (claims for bash but is broken)
* & ./Scripts/Activate.ps1
* ./Scripts/activate.bat
* ./Scripts/deactivate.bat

Note lack of powershell deactivate.

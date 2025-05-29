# Autovot (Linux-Compatible Fork)

This repository is a fork of the original [Autovot](https://github.com/mlml/autovot) repository, modified to run on Linux.

It is also part of the work for our paper submitted to the [TSD 2025 Conference](https://www.kiv.zcu.cz/tsd2025/index.php).

## Adapting for Italian and French

We trained a custom model to adapt Autovot for Italian and French.  
The trained model is located at:

- `Linux/plugin_autovot/models/italian_french.model.pos`
- `Linux/plugin_autovot/models/italian_french.model.neg`

You can immediately use it within the Autovot plugin for Praat.

The inputs used for training are located at:

- `Linux/train/train_data`

---

## Installation (Linux)

### 1) Install system dependencies

```bash
sudo apt install libc++-dev
```

### 2) Prepare binaries

This fork includes precompiled binaries: `VotDecode`, `VotFrontEnd2`, `VotTrain`.  
However, if you're on a different architecture, you may need to recompile them.

To test if the binaries work:

```bash
cd Linux/plugin_autovot
./VotDecode
```

You should see the help message of `VotDecode`.  
If not, recompile and copy the binaries:

```bash
cd autovot/code
make
cd ../..
cp autovot/bin/{VotDecode,VotFrontEnd2,VotTrain} Linux/plugin_autovot
cp autovot/bin/{VotDecode,VotFrontEnd2,VotTrain} Linux/train
```

### 3) Set up Python environment

To avoid modifying your system Python, we use the modern Python manager [uv](https://github.com/astral-sh/uv):

Install `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Add `~/.local/bin` to your `$PATH`:

```bash
export PATH="$PATH:$HOME/.local/bin"
```

*(Add this to `.bashrc`, `.zshrc`, etc. to make it permanent.)*

Create and activate a Python 3.11 virtual environment:

```bash
uv venv python3.11 --python 3.11
source python3.11/bin/activate
uv pip install -r requirements.txt
```

Update the shebang in all Python scripts to point to your virtual environment interpreter. Example:

```python
#! /home/gab/pyVenv/python3.11/bin/python
```

You can update the shebang lines in all relevant scripts using:

```bash
find Linux/plugin_autovot -name "*.py" -exec sed -i '1s|^#!.*python.*$|#!/path/to/your/venv/bin/python|' {} +
find Linux/train -name "*.py" -exec sed -i '1s|^#!.*python.*$|#!/path/to/your/venv/bin/python|' {} +
```

Replace `/path/to/your/venv/bin/python` with the actual path to your virtualenv Python.

### 4) Install the plugin

```bash
cp -r plugin_autovot $HOME/.praat_dir/
```

When using the AutoVOT plugin in Praat, uncheck "test", and select the desired model, i.e: `italian_french.model`.

---

## Training Your Own Model

### 1) Prepare your data

Edit the following configuration files:
- `config/wavFiles.txt` – list of `.wav` file paths
- `config/textGridFiles.txt` – list of corresponding TextGrid paths

### 2) Run training

```bash
./auto_vot_train.py --vot_tier vot --vot_mark vot config/wavFiles.txt config/textGridFiles.txt output_model/customModel.model
```

## Credits

All credit for the original algorithm and implementation goes to the authors of [Autovot](https://github.com/mlml/autovot).

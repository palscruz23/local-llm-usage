# Local LLM Maintenance Work Order Analysis

This workspace contains a sample maintenance work order dataset and a Jupyter notebook that uses a local Ollama model to extract:

- Equipment failed
- Failure mode
- LLM confidence
- Evidence phrase

## Files

- `data/maintenance_work_orders.csv`: sample maintenance work orders in CSV format.
- `notebooks/analyse_work_orders_ollama.ipynb`: notebook that reads the CSV and calls Ollama.
- `requirements.txt`: Python packages required by the notebook.

## Install Ollama

Start by installing `zstd`, which the Ollama Linux installer needs to extract the download:

```bash
sudo apt-get update
sudo apt-get install -y zstd
```

Then install Ollama with the official Linux installer:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Verify the install:

```bash
ollama --version
```

## Recommended Ollama model

Pull the recommended local model:

```bash
ollama pull qwen2.5:3b
```

To try a different model, pull it and update the `MODEL` value in the notebook. You can also set `OLLAMA_MODEL` before starting Jupyter:

```bash
export OLLAMA_MODEL=llama3.2:3b
```

Examples:

```bash
ollama pull qwen2.5:7b
ollama pull llama3.2:3b
ollama pull gemma3:1b
```

## Run

Start Ollama in one terminal:

```bash
ollama serve
```

If `ollama serve` says the address is already in use, Ollama is probably already running in the background and you can continue.

In a second terminal, install the Python dependencies and start Jupyter:

```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install -r requirements.txt
python3 -m ipykernel install --user --name local-llm-test --display-name "local-llm-test (.venv)"
jupyter notebook notebooks/analyse_work_orders_ollama.ipynb
```

In Jupyter, select the `local-llm-test (.venv)` kernel before running the notebook.

Run the notebook cells from top to bottom. The notebook reads `data/maintenance_work_orders.csv`, calls the local Ollama model for each work order, parses the JSON response, and adds these dataframe columns:

- `equipment_failed`
- `failure_mode`

It also saves the enriched output to `data/maintenance_work_orders_analysed.csv`.

## Troubleshooting

If the first import cell spins forever, the notebook kernel is likely stuck or using the wrong Python environment. Stop Jupyter, start it again from the activated `.venv`, and select the project kernel:

```bash
cd /home/palscruz23/local-llm-test
source .venv/bin/activate
python3 -m ipykernel install --user --name local-llm-test --display-name "local-llm-test (.venv)"
jupyter notebook notebooks/analyse_work_orders_ollama.ipynb
```

Then in Jupyter use `Kernel > Change Kernel > local-llm-test (.venv)`, followed by `Kernel > Restart Kernel and Run All Cells`.

You can confirm the imports work outside Jupyter with:

```bash
source .venv/bin/activate
python3 -c 'from pathlib import Path; import json; import re; import requests; import pandas as pd; print(pd.__version__)'
```

If the notebook raises this error:

```text
model 'qwen2.5:3b' not found
```

Ollama is running, but that model has not been pulled into the local Ollama instance. Pull it, then restart the Jupyter kernel and run the notebook again from the top:

```bash
ollama pull qwen2.5:3b
```

If you want to use a model that is already installed, check the available models and set `MODEL` in the notebook to one of those names:

```bash
ollama list
```

The notebook calls Ollama's `/api/generate` endpoint:

```text
http://localhost:11434/api/generate
```

You can confirm Ollama is responding with:

```bash
curl http://localhost:11434/api/tags
```

You can also test the exact endpoint used by the notebook:

```bash
curl http://localhost:11434/api/generate \
  -d '{"model":"qwen2.5:3b","prompt":"Return JSON with a hello key.","format":"json","stream":false}'
```

If `/api/tags` or `/api/generate` still returns 404, stop the running Ollama service and restart it:

```bash
sudo systemctl stop ollama
sudo systemctl start ollama
```

Then check the version and model:

```bash
ollama --version
ollama list
```

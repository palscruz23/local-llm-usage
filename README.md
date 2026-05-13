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

If you previously installed Ollama with Snap and see an internal Snap error, remove the broken Snap package before using the official installer:

```bash
sudo snap remove ollama
sudo apt-get update
sudo apt-get install -y zstd
curl -fsSL https://ollama.com/install.sh | sh
ollama --version
```

## Recommended Ollama model

Pull the recommended local model:

```bash
ollama pull qwen2.5:3b
```

For a Dell Inspiron 15 5510, this is a practical default because many configurations are CPU-only or have limited shared graphics memory. If you have 16 GB RAM and want better extraction quality, try `qwen2.5:7b`. If the laptop feels slow or has 8 GB RAM, use `llama3.2:3b` or `gemma3:1b`.

To try a different model, pull it and update the `MODEL` value in the notebook.

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
jupyter notebook notebooks/analyse_work_orders_ollama.ipynb
```

Run the notebook cells from top to bottom. The notebook reads `data/maintenance_work_orders.csv`, calls the local Ollama model for each work order, parses the JSON response, and adds these dataframe columns:

- `equipment_failed`
- `failure_mode`

It also saves the enriched output to `data/maintenance_work_orders_analysed.csv`.

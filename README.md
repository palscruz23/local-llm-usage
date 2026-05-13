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

## Recommended Ollama model

Start with:

```bash
ollama pull qwen2.5:3b
```

For a Dell Inspiron 15 5510, this is a practical default because many configurations are CPU-only or have limited shared graphics memory. If you have 16 GB RAM and want better extraction quality, try `qwen2.5:7b`. If the laptop feels slow or has 8 GB RAM, use `llama3.2:3b` or `gemma3:1b`.

## Install Ollama

If this command fails:

```bash
ollama pull qwen2.5:3b
```

with:

```text
Command 'ollama' not found
```

install Ollama first. On Ubuntu or WSL where Snap is available, run:

```bash
sudo snap install ollama
```

Then verify the install:

```bash
ollama --version
```

After Ollama is installed, pull the recommended model:

```bash
ollama pull qwen2.5:3b
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

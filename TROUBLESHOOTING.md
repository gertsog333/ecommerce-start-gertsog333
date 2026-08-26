# Troubleshooting Guide

## Common Issues

### Services Won't Start

**Problem:** `docker-compose up -d` fails

**Solutions:**
```bash
# Check Docker is running
docker info

# Check ports are free
lsof -i :8001,8002,8003,5000

# View detailed errors
docker-compose logs
```

### Model File Not Found

**Problem:** ML service can't load model

**Solution:**
```bash
# Pull models from DVC
dvc pull

# Or regenerate dummy model
python scripts/setup/init_project.sh
```

... (more issues)
### README references a non-existent script path

**Problem:** README's Quick Start says to run `python scripts/generate_data.py`, but this file does not exist.

**Solution:** The actual data generator lives at `scripts/data/generate_ecommerce_data.py`. Run:
```bash
python3 scripts/data/generate_ecommerce_data.py --products 100 --customers 500 --orders 1000 --conversations 200
```

### `ModuleNotFoundError: No module named 'pandas'` when generating data

**Problem:** Running the data generator directly with `python3` fails if no virtual environment is active — the script depends on `pandas`, `numpy`, and `faker`, which aren't part of the base system Python.

**Solution:** Activate a venv with these packages installed before running the script:
```bash
source /home/vagrant/mlops-venv/bin/activate   # or your own venv
pip install pandas numpy faker                 # if not already installed
python3 scripts/data/generate_ecommerce_data.py ...
```

### Health checks for ML/RAG/Agent services fail at Module 01 stage

**Problem:** `scripts/module-01/verify_setup.py` and the README's service table list endpoints on ports 8001 (ML), 8002 (RAG), 8003 (Agent), 5000 (MLflow) — but these all fail to connect right now.

**Solution:** This is expected at Module 01. `services/*/Dockerfile` and `docker/docker-compose.yml` (the full stack) are empty placeholders — they get implemented in later modules. Only the root `docker-compose.yml` (single `chat-agent` service, port 8501) is functional at this stage. Use `docker compose ps` (root compose file) to check status, not the Module-01 verify script.

### Vagrant VM doesn't expose a new service's port by default

**Problem:** After adding a new service (Jupyter, then this Streamlit app) inside the shared VM, it wasn't reachable from the Windows host browser.

**Solution:** Each new port needs an explicit forwarded_port entry in the `Vagrantfile`, followed by `vagrant reload` (or `vagrant up` if the VM was halted) to apply it. Forgetting this is an easy gotcha when running multiple course projects in one shared VM.

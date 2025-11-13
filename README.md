Horde AI LLM Worker — CPU-Only (GitHub Codespaces Edition)

This repository runs a CPU-only AI Horde LLM (Scribe) worker fully inside GitHub Codespaces, with no GPU, no Docker-in-Docker, and no image pipelines.

All configuration is handled through:

.devcontainer/Dockerfile — builds the worker environment

.devcontainer/devcontainer.json — automates setup & auto-start

GitHub Codespaces — the only runtime environment required

This setup creates a lightweight, reliable CPU text-worker for the AI Horde network.

Features

✔ CPU-only LLM (Scribe) worker

✔ Runs automatically when Codespace starts

✔ Uses 8 threads for inference

✔ No GPU, no Torch CUDA, no image backend

✔ Fully Docker-free inside the Codespace (GitHub builds the container externally)

✔ Auto-install of scribe runtime

✔ Works even on free-tier Codespaces

File Structure

After moving the Dockerfile, your repository now looks like:

horde-worker-llm-cpu-gh-codespaces/
├── LICENSE
├── PROMPTS
└── .devcontainer/
    ├── Dockerfile           # container build instructions
    └── devcontainer.json    # Codespaces automation (runtime install + auto-start)


The root folder is clean — everything the Codespace needs is inside .devcontainer/.

Requirements Before Launch

You must configure your Horde API key.

Go to:

GitHub → Repository → Settings → Variables → New repository variable

Add:

Name	Value
HORDE_API_KEY	Your AI Horde worker key

Optional:

Name	Purpose
HORDE_WORKER_NAME	Override name shown on the Horde dashboard
HORDE_MAX_THREADS	Override CPU threads (default: 8)

Codespaces injects these into the devcontainer at runtime.

Starting the Worker in Codespaces
1. Create a Codespace

Go to your repo → Code → Create Codespace on main

GitHub will:

Build the image using .devcontainer/Dockerfile

Create the container environment

Run update-runtime.sh --scribe

Auto-start the worker in the background

2. Confirm the Worker is Running

Inside the Codespace terminal:

tail -f /var/log/horde-scribe.log


You should see connection logs like:

[Scribe] Connecting to Horde...
[Scribe] Online. Waiting for jobs...

3. Verify Online in Dashboard

Visit:

https://stablehorde.net/

Your worker should appear under your account with the configured name.

Manual Worker Start (Optional)

If you want full manual control:

cd /app
./update-runtime.sh --scribe
./horde-scribe-bridge.sh

What This Devcontainer Does
.devcontainer/Dockerfile

Installs Ubuntu base deps

Clones AI-Horde-Worker into /app

Leaves image and GPU toolchains out

Only prepares for scribe runtime

.devcontainer/devcontainer.json

Runs update-runtime.sh --scribe on creation

Starts the worker automatically on every boot

Requests 8 CPU cores from Codespaces

Loads your env/secret variables

Logs worker output to /var/log/horde-scribe.log

Troubleshooting
Issue: ModuleNotFoundError: requests

Cause: runtime not installed
Fix:

cd /app
./update-runtime.sh --scribe

Issue: “failed to connect; is docker installed?”

Cause: an image pipeline (img2img/SD/upscale) was mistakenly enabled
Fix: Ensure your bridgeData.yaml contains:

allow_img: false
allow_img2img: false
allow_post_processing: false
allow_upscale: false
allow_kohya: false
allow_text: true
allow_caption: false

Issue: Worker won’t start on Codespace boot

Check logs:

cat /var/log/horde-scribe.log

Credits

AI Horde Worker:
https://github.com/Haidra-Org/AI-Horde-Worker

This repo configures it specifically for CPU-only LLM execution inside GitHub Codespaces.

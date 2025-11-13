# Horde AI LLM Worker — CPU-Only (GitHub Codespaces)

This repository runs a CPU-only AI Horde LLM (Scribe) worker inside GitHub Codespaces.
No GPU is required, and no Docker-in-Docker is used — Codespaces builds the container from the files in `.devcontainer/`.

The worker automatically installs the scribe runtime and starts when the Codespace boots.

## Features

- CPU-only text generation worker
- Uses the AI Horde Scribe backend
- Automatically installs runtime on first boot
- Automatically starts the worker in the background
- 8 CPU threads by default
- Compatible with free or paid Codespaces
- No image pipelines, no GPU requirements

## Repository Structure

horde-worker-llm-cpu-gh-codespaces/
├── LICENSE
├── PROMPTS
└── .devcontainer/
    ├── Dockerfile
    └── devcontainer.json

Everything needed for Codespaces is inside `.devcontainer/`.

## Required Configuration

Before launching a Codespace, add your Horde API key:

GitHub → Repository → Settings → Variables → New repository variable

| Name            | Value                      |
|-----------------|----------------------------|
| HORDE_API_KEY   | Your AI Horde worker key   |

Optional overrides:

| Name              | Purpose                     |
|-------------------|-----------------------------|
| HORDE_WORKER_NAME | Custom worker name          |
| HORDE_MAX_THREADS | Number of CPU threads (8)   |

Codespaces injects these automatically when building the devcontainer.

## Starting the Worker

1. Create a Codespace  
From GitHub: Code → Create Codespace on Main

Codespaces will:
- Build the container from `.devcontainer/Dockerfile`
- Install the scribe runtime
- Start the worker automatically

2. Check worker logs

```
tail -f /var/log/horde-scribe.log
```

You should see:
[Scribe] Connecting to Horde...
[Scribe] Online

## Manual Worker Start (Optional)

```
cd /app
./update-runtime.sh --scribe
./horde-scribe-bridge.sh
```

## Troubleshooting

### Missing Python dependencies
ModuleNotFoundError: No module named 'requests'

Fix:
```
cd /app
./update-runtime.sh --scribe
```

### Worker tries to run image pipelines
Disable image tasks in `bridgeData.yaml`:

```
allow_img: false
allow_img2img: false
allow_post_processing: false
allow_upscale: false
allow_kohya: false
allow_text: true
allow_caption: false
```

## Credits

AI Horde Worker:  
https://github.com/Haidra-Org/AI-Horde-Worker

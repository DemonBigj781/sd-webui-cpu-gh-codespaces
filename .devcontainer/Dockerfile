# CPU-only AI Horde LLM (scribe) worker
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# Basic system deps for Horde worker & micromamba runtime
RUN apt-get update && apt-get install -y \
    git \
    curl \
    ca-certificates \
    ffmpeg \
    libsm6 \
    libxext6 \
    bzip2 \
    wget \
 && rm -rf /var/lib/apt/lists/*

# ---- AI Horde Worker ----
WORKDIR /worker

# Clone the official AI-Horde-Worker (text/alchemy worker) 1
RUN git clone --depth=1 https://github.com/Haidra-Org/AI-Horde-Worker.git . 

# Prepare CPU-only scribe (LLM) environment
# --scribe = text/alchemy only, much lighter than full image stack 2
RUN chmod +x ./update-runtime.sh \
 && ./update-runtime.sh --scribe \
 && bin/micromamba run -r conda -n linux python -s -m pip cache purge || true

# Default config: if bridgeData.yaml doesn't exist, seed it from template
RUN if [ ! -f bridgeData.yaml ] && [ -f bridgeData_template.yaml ]; then \
      cp bridgeData_template.yaml bridgeData.yaml; \
    fi

# ---- Runtime configuration via env vars ----
# These *should* be overridden in your GitHub Space / Codespace / deployment secrets
ENV HORDE_API_KEY="" \
    HORDE_URL="https://stablehorde.net" \
    HORDE_WORKER_NAME="cpu-llm-worker" \
    HORDE_MAX_THREADS="2"

# Optional: tweak threads at runtime with: -e HORDE_MAX_THREADS=4, etc.

# The worker only calls out to the Horde API, so no ports required.
# If you want to access the web UI inside the container, you can expose 7860 or similar
# and run `./bridge-webui.sh` in a separate container/command instead.

# Start the LLM/scribe bridge on container start 3
ENTRYPOINT ["./horde-scribe-bridge.sh"]

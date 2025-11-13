# CPU-only AI Horde LLM worker + JupyterLab + SSH + FTP
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    ENV PIP_ROOT_USER_ACTION=ignore


WORKDIR /app

# Base tools + Python + SSH
RUN apt-get update && apt-get install -y \
    git \
    curl \
    wget \
    bzip2 \
    ca-certificates \
    ffmpeg \
    libsm6 \
    libxext6 \
    lshw \
    python3 \
    python3-pip \
    openssh-server \
    && rm -rf /var/lib/apt/lists/*

# Clone AI Horde worker
RUN git clone --depth=1 https://github.com/Haidra-Org/AI-Horde-Worker.git /app

# Install scribe runtime (CPU-only LLM)
RUN cd /app && \
    chmod +x ./update-runtime.sh && \
    ./update-runtime.sh --scribe

# JupyterLab + simple FTP server
RUN pip3 install --no-cache-dir jupyterlab pyftpdlib

# SSH server basic setup
RUN mkdir -p /var/run/sshd

# Copy startup script (relative to build context root)
COPY .devcontainer/start-all.sh /usr/local/bin/start-all.sh
RUN chmod +x /usr/local/bin/start-all.sh

# Environment variables for Horde worker (overridden by Codespaces variables if set)
ENV HORDE_URL="https://stablehorde.net" \
    HORDE_WORKER_NAME="cpu-llm-space-worker" \
    HORDE_MAX_THREADS="8"

# Devcontainer overrides CMD, so just default to a shell
CMD ["/bin/bash"]

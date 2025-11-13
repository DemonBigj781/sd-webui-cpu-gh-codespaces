FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_ROOT_USER_ACTION=ignore

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
    python-is-python3 \
    openssh-server \
    && rm -rf /var/lib/apt/lists/*

# Clone AI Horde worker (scribe/alchemy worker)
RUN git clone --depth=1 https://github.com/Haidra-Org/AI-Horde-Worker.git /app

# Install scribe runtime (CPU-only text worker)
RUN cd /app && \
    chmod +x ./update-runtime.sh && \
    ./update-runtime.sh --scribe

# Install full Jupyter stack + FTP server
RUN pip3 install --no-cache-dir jupyterlab notebook jupyter pyftpdlib

# SSH server basic setup
RUN mkdir -p /var/run/sshd

# Workspace directory for Codespaces
RUN mkdir -p /workspace

# Startup script
COPY .devcontainer/start-all.sh /usr/local/bin/start-all.sh
RUN chmod +x /usr/local/bin/start-all.sh

# Default Horde env (overridable via Codespaces variables)
ENV HORDE_URL="https://stablehorde.net" \
    HORDE_WORKER_NAME="cpu-llm-space-worker" \
    HORDE_MAX_THREADS="8"

CMD ["/bin/bash"]

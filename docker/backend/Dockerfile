FROM ubuntu:24.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV TZ="UTC"

# ==========================
# 1. Base OS + Tools (Run as Root)
# ==========================
# We install all system tools FIRST, before switching users
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        wget net-tools curl build-essential neovim ssh gcc openssh-server \
        mc tmux lsof sudo python3 python3-pip git python3.12-venv && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# ==========================
# 2. User Setup (Ubuntu)
# ==========================
RUN id -u ubuntu &>/dev/null || adduser --disabled-password --gecos '' ubuntu && \
    usermod -aG sudo ubuntu && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    chmod a+rwx /home/ubuntu/

# Create the backend folder structure
RUN mkdir -p /opt/envs && chown ubuntu:ubuntu /opt/envs
RUN mkdir -p /home/ubuntu/backend && chown -R ubuntu:ubuntu /home/ubuntu/backend

# ==========================
# 3. Switch to User 'ubuntu'
# ==========================
USER ubuntu
WORKDIR /home/ubuntu

# ==========================
# 4. Python Environment
# ==========================
# Create the virtual environment
RUN python3 -m venv /opt/envs/neuralops_backend

# Activate venv for all future commands (Path update)
ENV PATH="/opt/envs/neuralops_backend/bin:${PATH}"

# ==========================
# 5. Application Dependencies
# ==========================
WORKDIR /home/ubuntu/backend/

# Copy requirements from the context
COPY backend/requirements.txt .

# Install Python packages
RUN pip install --no-cache-dir -r requirements.txt

# ==========================
# 6. Runtime Configuration
# ==========================
# EXPOSE 8000

# # Default command (overridden by docker-compose)
# CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
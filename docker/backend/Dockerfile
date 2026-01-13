FROM ubuntu:24.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV TZ="UTC"

# ==========================
# 1. Base OS + Tools
# ==========================
# Added 'git' and 'python3-venv' explicitly to ensure 3.12 venv works
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

# Prepare directories
RUN mkdir -p /opt/envs && chown ubuntu:ubuntu /opt/envs
RUN mkdir -p /home/ubuntu/neuralops_backend && chown -R ubuntu:ubuntu /home/ubuntu/neuralops_backend

USER ubuntu
WORKDIR /home/ubuntu

# ==========================
# 3. Python Environment
# ==========================
# Create the venv
RUN python3 -m venv /opt/envs/neuralops_backend

# Activate venv for all future commands
ENV PATH="/opt/envs/neuralops_backend/bin:${PATH}"

# ==========================
# 4. Dependencies (The Fix)
# ==========================
WORKDIR /home/ubuntu/neuralops_backend/

# 🔴 FIX: Point to the symlink you created inside docker/backend/
# The context is root (.), so we path down to docker/backend/requirements.txt
COPY backend/requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

# # ==========================
# # 5. Runtime
# # ==========================
# # We expose the port typically used by FastAPI
# EXPOSE 8000

# # Default command if docker-compose doesn't override it
# # (Though your docker-compose command will likely take precedence)
# CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
FROM python:3.11-slim

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
        git \
        curl \
        ca-certificates \
        build-essential \
 && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir \
        mlflow==2.14.3 \
        ipykernel \
        jupyter \
        scikit-learn \
        pandas \
        numpy

RUN groupadd -g 1000 dev \
 && useradd -u 1000 -g 1000 -m -s /bin/bash dev

USER 1000:1000
WORKDIR /workspace

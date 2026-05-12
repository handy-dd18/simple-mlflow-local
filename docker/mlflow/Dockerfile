FROM python:3.11-slim

RUN pip install --no-cache-dir mlflow==2.14.3

RUN groupadd -g 1000 mlflow \
 && useradd -u 1000 -g 1000 -m -s /bin/bash mlflow

USER 1000:1000
WORKDIR /app
EXPOSE 5000

CMD ["mlflow", "server", \
     "--host", "0.0.0.0", \
     "--port", "5000", \
     "--backend-store-uri", "sqlite:///mlflow.db", \
     "--serve-artifacts", \
     "--artifacts-destination", "/mlruns"]

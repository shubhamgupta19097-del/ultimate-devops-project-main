FROM python:3.12-slim-bookworm AS base

WORKDIR /usr/src/app

RUN apt-get update && apt-get install -y \
    gcc \
    python3-dev \
    build-essential \
    curl

COPY ./src/recommendation/requirements.txt ./

RUN pip install --upgrade pip setuptools wheel

RUN pip install opentelemetry-distro

RUN pip install -r requirements.txt

RUN pip install --upgrade pip setuptools wheel && \
    python -m opentelemetry.distro.bootstrap -a install || true

COPY ./src/recommendation/ ./

ENV RECOMMENDATION_PORT=1010

ENTRYPOINT ["python", "recommendation_server.py"]

#Dockerfile

FROM python:3.10.12-slim-bullseye as builder

RUN apt-get update && \
    apt-get upgrade --yes

RUN useradd --create-home dansdockerpy
USER dansdockerpy
WORKDIR /home/dansdockerpy

ENV VIRTUALENV=/home/dansdockerpy/venv
RUN python3 -m venv $VIRTUALENV
ENV PATH="$VIRTUALENV/bin:$PATH"

COPY --chown=dansdockerpy pyproject.toml constraints.txt ./
RUN python -m pip install --upgrade pip setuptools 
RUN python -m pip install --no-cache-dir -c constraints.txt ".[dev]"

COPY --chown=dansdockerpy src/ src/
COPY --chown=dansdockerpy test/ test/

RUN python -m pip install . -c constraints.txt && \
    python -m pytest test/unit/ && \
    python -m flake8 src/ && \
    python -m isort src/ --check && \
    python -m black src/ --check --quiet && \
    python -m pylint src/ --exit-zero --disable=C0114,C0116,R1705 && \
    python -m bandit -r src/ --quiet && \
    python -m pip wheel --wheel-dir dist/ . -c constraints.txt

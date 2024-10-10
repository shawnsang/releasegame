# Use a more specific base image
FROM docker.m.daocloud.io/library/python:3.11-slim-buster as build

WORKDIR /app

ENV PYTHONPATH=/app

# Install system dependencies and Poetry in one layer
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    chromium \
    chromium-driver \
    && curl -sSL https://install.python-poetry.org | POETRY_HOME=/opt/poetry python \
    && ln -s /opt/poetry/bin/poetry /usr/local/bin/poetry \
    && poetry config virtualenvs.create false \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Copy only dependency files
COPY pyproject.toml poetry.lock* ./

# Install dependencies
RUN poetry install --no-root --no-cache --only main

# Use multi-stage build to reduce final image size
FROM docker.m.daocloud.io/library/python:3.11-slim-buster as release

WORKDIR /app

# Copy installed packages from build stage
COPY --from=build /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY --from=build /usr/local/bin/poetry /usr/local/bin/poetry

# Copy Chromium and its driver
COPY --from=build /usr/bin/chromium /usr/bin/chromium
COPY --from=build /usr/bin/chromedriver /usr/bin/chromedriver

# Copy application code
COPY . .

CMD ["python", "main.py"]
FROM docker.m.daocloud.io/library/python:3.11 as build

WORKDIR /app

ENV PYTHONPATH=/app \
    POETRY_HOME=/opt/poetry \
    PATH=$POETRY_HOME/bin:$PATH

# Install Poetry
COPY pyproject.toml poetry.lock /app/
RUN curl -sSL https://install.python-poetry.org | python3 && \
    mv ~/.poetry /opt/poetry && \
    echo 'export POETRY_HOME="/opt/poetry"' >> ~/.bashrc && \
    echo 'export PATH="$POETRY_HOME/bin:$PATH"' >> ~/.bashrc && \
    poetry config virtualenvs.create false

# Install Chromium for web loader
RUN apt-get update && apt-get install -y chromium-driver && \
rm -rf /var/lib/apt/lists/*

# Install dependencies
COPY ./pyproject.toml ./poetry.lock* /app/
RUN poetry install --no-root --no-cache --only main

# Clean up Poetry cache
RUN rm -rf $POETRY_HOME/cache

# ====================================
FROM build as release

COPY . .

CMD ["python", "main.py"]
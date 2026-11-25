# Étape commune : image Python officielle et légère
FROM python:3.11-slim AS base

# curl sert au healthcheck (Docker vérifie que la page répond)
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /usr/local/app

# Dépendances d'abord : mises en cache tant que requirements.txt ne change pas
COPY requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Étape développement : redémarre tout seul quand le code change
FROM base AS dev
RUN pip install --no-cache-dir watchdog
ENV FLASK_ENV=development
CMD ["python", "app.py"]

# Étape production
FROM base AS final
COPY . .

# Sécurité : création d'un utilisateur sans privilèges, l'application ne tourne pas en root
RUN useradd --create-home --uid 10001 appuser && \
    chown -R appuser /usr/local/app
USER appuser

EXPOSE 80
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:80", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]

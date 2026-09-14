# Базовый образ
FROM python:3.9-slim
WORKDIR /app

# Системные пакеты
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl vim && \
    rm -rf /var/lib/apt/lists/*

# Python-зависимости
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

# Непривилегированный пользователь
RUN useradd -u 1000 -m appuser && chown -R appuser:appuser /app
USER appuser

EXPOSE 5000
ENV FLASK_ENV=production
CMD ["python", "app.py"]

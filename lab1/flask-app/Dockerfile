# Базовый образ
FROM python:3.9-slim

# Рабочая директория
WORKDIR /app

# Системные пакеты
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl vim && \
    rm -rf /var/lib/apt/lists/*

# Python-зависимости
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Исходный код приложения
COPY app.py .

# Непривилегированный пользователь
RUN useradd -u 1000 -m appuser && chown -R appuser:appuser /app
USER appuser

# Порт приложения
EXPOSE 5000

# Переменная окружения
ENV FLASK_ENV=production

# Запуск приложения
CMD ["python", "app.py"]

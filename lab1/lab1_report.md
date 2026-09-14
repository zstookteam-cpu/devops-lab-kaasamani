University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: Введение в веб-технологии
Year: 2025/2026
Group: U4225
Author: Kaasamani Roni Bakhaaevich
Lab: Lab1
Date of create: 07.09.2026
Date of finished: 07.09.2026

# Лабораторная работа №1. Основы работы с Docker

## Цель работы

Научиться работать с Docker: устанавливать Docker, создавать Dockerfile, собирать образы, запускать контейнеры и управлять ими.

## Ход работы

### 1. Изучение основ Docker

#### 1.1. Установка Docker


Для Windows/macOS был установлен Docker Desktop, для Linux — Docker Engine (см. [официальную инструкцию](https://docs.docker.com/get-docker/)).

Проверка версии установленного Docker:

```bash
docker --version
```

Пример вывода:

```
Docker version 29.4.3, build 055a478
```

*(вставьте сюда скриншот вывода команды на своей машине)*

Запуск тестового контейнера:

```bash
docker run hello-world
```

Эта команда скачивает образ `hello-world` из Docker Hub (если его ещё нет локально), создаёт из него контейнер, запускает его — контейнер выводит приветственное сообщение и завершает работу. Успешный вывод подтверждает, что Docker Engine установлен и настроен корректно, клиент может общаться с демоном, а демон способен скачивать образы и запускать контейнеры.

*(вставьте сюда скриншот вывода команды)*

Изучение базовых команд:

```bash
docker images   # список локально скачанных образов
docker ps        # список запущенных контейнеров
docker ps -a      # список всех контейнеров, включая остановленные
```

*(вставьте сюда скриншот вывода команд)*

#### 1.2. Работа с готовыми образами

Скачивание образа Ubuntu:

```bash
docker pull ubuntu:latest
```

Запуск интерактивного контейнера:

```bash
docker run -it ubuntu bash
```

Флаг `-it` объединяет два флага: `-i` (interactive, оставляет открытым stdin) и `-t` (tty, выделяет псевдотерминал) — вместе они дают интерактивную сессию в терминале контейнера.

Внутри контейнера установка пакета curl:

```bash
apt update && apt install -y curl
curl --version
```

*(вставьте сюда скриншот установки и вывода curl --version)*

Выход из контейнера:

```bash
exit
```

При выходе из интерактивной сессии с единственным процессом (bash) контейнер останавливается, но не удаляется — он остаётся в списке `docker ps -a`.

#### 1.3. Запуск веб-сервера

Запуск контейнера с nginx в фоновом режиме:

```bash
docker run -d -p 8080:80 --name web-server nginx:alpine
```

Расшифровка флагов:
- `-d` — detached, запуск в фоновом режиме;
- `-p 8080:80` — проброс порта: порт 8080 хоста сопоставляется с портом 80 внутри контейнера, на котором слушает nginx;
- `--name web-server` — присвоение контейнеру понятного имени вместо случайного;
- `nginx:alpine` — используемый образ (облегчённая сборка nginx на базе Alpine Linux).

Проверка в браузере: [http://localhost:8080](http://localhost:8080) — должна открыться приветственная страница nginx.

*(вставьте сюда скриншот страницы в браузере)*

Просмотр логов контейнера:

```bash
docker logs web-server
```

*(вставьте сюда скриншот логов — в них видны HTTP-запросы, которые сделал браузер)*

Подключение к работающему контейнеру:

```bash
docker exec -it web-server sh
```

Команда `docker exec` запускает дополнительный процесс (здесь — shell `sh`, так как в Alpine нет bash по умолчанию) внутри уже работающего контейнера, в отличие от `docker run`, который создаёт новый контейнер.

#### 1.4. Управление контейнерами

```bash
docker ps            # запущенные контейнеры
docker ps -a          # все контейнеры
docker stop web-server    # остановить контейнер (SIGTERM, затем SIGKILL по таймауту)
docker start web-server   # запустить ранее остановленный контейнер
docker rm web-server      # удалить контейнер (должен быть предварительно остановлен)
docker rmi nginx:alpine   # удалить образ из локального хранилища
```

*(вставьте сюда скриншоты вывода каждой команды)*

#### 1.5. Работа с томами (volumes)

Тома (volumes) — это способ хранения данных, который не привязан к жизненному циклу конкретного контейнера: данные сохраняются на хосте (в области, управляемой Docker) и могут быть переиспользованы другими контейнерами.

Создание тома:

```bash
docker volume create my-volume
```

Запуск контейнера с подключённым томом:

```bash
docker run -it --name volume-test -d -v my-volume:/data ubuntu bash
```

Флаг `-v my-volume:/data` монтирует том `my-volume` в директорию `/data` внутри контейнера.

Подключение к контейнеру и запись файла в том:

```bash
docker exec -it volume-test bash
echo "Hello from volume" > /data/test.txt
exit
```

Удаление контейнера и создание нового с тем же томом:

```bash
docker rm -f volume-test
docker run -it --name volume-test2 -d -v my-volume:/data ubuntu bash
docker exec -it volume-test2 cat /data/test.txt
```

Ожидаемый результат: несмотря на то что первый контейнер был удалён, файл `test.txt` сохранился, так как данные хранились не в файловой системе контейнера, а в томе `my-volume`, существующем независимо от контейнеров.

*(вставьте сюда скриншот, подтверждающий, что содержимое файла — "Hello from volume")*

### 2. Лабораторная работа со звёздочкой — создание Dockerfile по заданным условиям

#### 2.1. Файлы проекта

Структура проекта:

```
flask-app/
├── app.py
├── requirements.txt
└── Dockerfile
```

`app.py`:

```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello():
    return "Hello from Docker!"
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

`requirements.txt`:

```
Flask==2.0.1
```

#### 2.2. Dockerfile

```dockerfile
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
```

Каждая инструкция Dockerfile соответствует одному из заданных требований:

| Требование | Инструкция |
|---|---|
| Базовый образ python:3.9-slim | `FROM python:3.9-slim` |
| Рабочая директория /app | `WORKDIR /app` |
| Системные пакеты curl, vim | `RUN apt-get install -y curl vim` |
| Python-зависимости из requirements.txt | `COPY requirements.txt .` + `RUN pip install -r requirements.txt` |
| Копирование app.py | `COPY app.py .` |
| Пользователь appuser с UID 1000 | `RUN useradd -u 1000 -m appuser` |
| Переключение на appuser | `USER appuser` |
| Порт 5000 | `EXPOSE 5000` |
| Переменная FLASK_ENV=production | `ENV FLASK_ENV=production` |
| Запуск приложения | `CMD ["python", "app.py"]` |

#### 2.3. Сборка и проверка

```bash
docker build -t my-flask-app .
docker run -d -p 5000:5000 --name flask-container my-flask-app
curl http://localhost:5000
```

Ожидаемый результат — в терминале выводится:

```
Hello from Docker!
```

*(вставьте сюда скриншот сборки образа и вывода curl)*

Дополнительная проверка, что процесс внутри контейнера действительно выполняется от имени `appuser`, а не `root`:

```bash
docker exec -it flask-container whoami
docker exec -it flask-container id
```

## Результаты и анализ

В ходе работы были изучены и опробованы:
- основные команды жизненного цикла контейнера (`run`, `ps`, `stop`, `start`, `rm`);
- работа с готовыми образами из Docker Hub и их модификация изнутри контейнера;
- проброс портов и запуск изолированного веб-сервера (nginx);
- механизм томов (volumes) для хранения данных, переживающих удаление контейнера;
- написание Dockerfile «с нуля» по заданным техническим требованиям: выбор базового образа, установка системных и Python-зависимостей, создание непривилегированного пользователя (что является хорошей практикой безопасности — приложение не должно работать от root без необходимости), проброс порта и настройка переменных окружения.

## Выводы

Docker позволяет упаковывать приложение вместе со всем окружением (системными и языковыми зависимостями) в переносимый, воспроизводимый и изолированный образ. Это упрощает развёртывание — один и тот же образ гарантированно ведёт себя одинаково на любой машине с установленным Docker, устраняя проблему «у меня работает, а на сервере — нет». Работа с томами решает проблему эфемерности файловой системы контейнера, позволяя сохранять данные между пересозданиями контейнеров. Создание образов по собственному Dockerfile — базовый навык для контейнеризации любого приложения и последующего использования в CI/CD и оркестраторах (Docker Compose, Kubernetes).

## Полезные ссылки

- [Docker Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Docker Commands Reference](https://docs.docker.com/reference/cli/docker/)
- [Docker Images](https://docs.docker.com/reference/cli/docker/image/ls/)
- [Docker Containers](https://docs.docker.com/reference/cli/docker/container/)
- [Правила оформления лабораторных работ](https://github.com/itmo-ict-faculty/introduction-in-web-tech/blob/main/docs/education/labs2025-2026/reportdesign.md)

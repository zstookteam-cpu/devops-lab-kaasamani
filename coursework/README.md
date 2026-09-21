# Персональный сайт — курсовая работа

Курсовая работа по курсу «Введение в веб-технологии» (ИТМО, группа U4225): персональный сайт, собранный на [MkDocs](https://www.mkdocs.org/) с темой [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

**Автор:** Каасамани Рони Бахааевич

## Локальный запуск

```bash
pip install mkdocs mkdocs-material
mkdocs serve
```

Сайт будет доступен на `http://127.0.0.1:8000`.

## Публикация на GitHub Pages

```bash
mkdocs gh-deploy --force
```

## Структура

- `docs/index.md` — Главная
- `docs/about.md` — О себе
- `docs/projects.md` — Проекты
- `docs/contacts.md` — Контакты
- `docs/images/` — логотип и favicon
- `mkdocs.yml` — конфигурация сайта
- `report.md` — отчёт о выполнении курсовой работы

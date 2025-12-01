# Students API

## Описание

FastAPI-проект для управления студентами и группами. Реализованы все эндпоинты из задания. База данных PostgreSQL в Docker. Слоистая архитектура: API → Services → Repositories → Models.

## Требования

* Python 3.11+
* Docker и Docker Compose
* Библиотеки из `requirements.txt`

## Быстрый старт
```bash
# 1. Создать .env файл
cp .env.example .env

# 2. Запустить приложение
docker-compose up --build

# 3. Открыть документацию
# http://localhost:8000/docs
```

**Остановка:**
```bash
docker-compose down -v
```

## API Эндпоинты

Базовый URL: `/api/v1`

### Студенты

* `POST /students` — Создать студента
* `GET /students` — Список всех студентов
* `GET /students/{id}` — Получить студента по ID
* `DELETE /students/{id}` — Удалить студента

### Группы

* `POST /groups` — Создать группу
* `GET /groups` — Список всех групп
* `GET /groups/{id}` — Получить группу по ID
* `DELETE /groups/{id}` — Удалить группу
* `GET /groups/{id}/students` — Студенты группы
* `POST /groups/add-student` — Добавить студента в группу
* `POST /groups/remove-student` — Удалить студента из группы
* `POST /groups/transfer-student` — Перевести студента между группами

## Структура проекта
```
students-api/
├── src/
│   ├── api/routers/          # API эндпоинты
│   │   ├── students.py
│   │   └── groups.py
│   ├── services/             # Бизнес-логика
│   ├── repositories/         # Работа с БД
│   ├── schemas/              # Pydantic схемы
│   ├── models/               # ORM модели
│   ├── database/             # Настройка БД
│   └── config.py             # Конфигурация
├── main.py                   # Точка входа
├── docker-compose.yml        # Docker Compose
├── Dockerfile
├── requirements.txt
└── .env.example
```

## Примеры использования

### Создать студента
```bash
curl -X POST "http://localhost:8000/api/v1/students" \
  -H "Content-Type: application/json" \
  -d '{
    "first_name": "Иван",
    "last_name": "Иванов",
    "email": "ivan@example.com"
  }'
```

### Создать группу
```bash
curl -X POST "http://localhost:8000/api/v1/groups" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Группа 101",
    "description": "Первая группа"
  }'
```

### Добавить студента в группу
```bash
curl -X POST "http://localhost:8000/api/v1/groups/add-student" \
  -H "Content-Type: application/json" \
  -d '{
    "student_id": 1,
    "group_id": 1
  }'
```

### Перевести студента
```bash
curl -X POST "http://localhost:8000/api/v1/groups/transfer-student" \
  -H "Content-Type: application/json" \
  -d '{
    "student_id": 1,
    "from_group_id": 1,
    "to_group_id": 2
  }'
```

## Переменные окружения
```bash
POSTGRES_USER=student_user
POSTGRES_PASSWORD=student_password
POSTGRES_DB=students_db
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
```

## Технологии

* FastAPI 0.115.5 — веб-фреймворк
* SQLAlchemy 2.0.36 — ORM
* PostgreSQL 15 — база данных
* Pydantic 2.10.3 — валидация данных
* asyncpg 0.30.0 — асинхронный драйвер PostgreSQL
* Docker — контейнеризация

## Тестирование

Откройте Swagger UI для интерактивного тестирования:
```
http://localhost:8000/docs
```

## Модель данных

**students** — студенты
* id, first_name, last_name, email (unique)

**groups** — группы
* id, name (unique), description

**student_group_association** — связь M2M
* student_id, group_id

## Архитектура
```
┌─────────────┐
│  API Layer  │ ← Роутеры (students.py, groups.py)
├─────────────┤
│   Service   │ ← Бизнес-логика
├─────────────┤
│ Repository  │ ← Работа с БД
├─────────────┤
│   Models    │ ← ORM модели SQLAlchemy
└─────────────┘
```

## Решение проблем

**База данных не подключается:**
```bash
docker-compose down -v
docker-compose up --build
```

**Проверить логи:**
```bash
docker-compose logs app
docker-compose logs db
```

**Подключиться к БД:**
```bash
docker exec -it students_db psql -U student_user -d students_db
```
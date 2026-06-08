# Image Hosting

Веб-додаток для завантаження, зберігання та керування зображеннями з підтримкою метаданих та зручним інтерфейсом.

## Огляд

Image Hosting — це full-stack рішення для хостингу зображень, що складається з Python backend API, PostgreSQL бази даних, Nginx веб-сервера та сучасного frontend на чистому JavaScript. Проєкт повністю контейнеризований за допомогою Docker Compose для простого розгортання.

## Основні можливості

### Завантаження та керування
- **Drag & Drop завантаження** — перетягніть файли прямо в браузер
- **Множинне завантаження** — завантажуйте декілька зображень одночасно
- **Валідація файлів** — автоматична перевірка розміру та формату
- **Підтримувані формати**: JPEG, PNG, GIF, WebP, BMP

### Перегляд та організація
- **Галерея зображень** — зручний перегляд всіх завантажених файлів
- **Пагінація** — налаштовувана кількість елементів на сторінці
- **Сортування** — по даті додавання (ascending/descending)
- **Детальна інформація** — розмір, дата завантаження, розширення, розміри в пікселях

### Управління
- **Видалення файлів** — з підтвердженням дії
- **Автоматичні бекапи** — регулярне резервне копіювання бази даних
- **Логування** — повна історія операцій

## Архітектура

### Stack технологій

**Backend**
- Python 3.x (http.server для HTTP обробки)
- PostgreSQL 17 (реляційна база даних)
- aiohttp-style handlers

**Frontend**
- Vanilla JavaScript (ES6+)
- HTML5 / CSS3
- Axios для HTTP запитів
- Font Awesome icons

**Infrastructure**
- Docker & Docker Compose
- Nginx (reverse proxy + static files)
- Automated PostgreSQL backups

### Компоненти системи

```
┌─────────────┐
│   Browser   │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│    Nginx    │ ← Static files (frontend/)
│  (Port 80)  │ ← Images (images/)
└──────┬──────┘
       │ /api/* → reverse proxy
       ↓
┌─────────────┐
│   Backend   │ ← Python API server
│ (Port 8000) │ ← Business logic
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  PostgreSQL │ ← Metadata storage
│ (Port 5432) │ ← Transactional DB
└─────────────┘
```

## Встановлення та запуск

### Передумови

- Docker Desktop (Windows/Mac) або Docker Engine + Docker Compose (Linux)
- Git для клонування репозиторію

### Кроки встановлення

1. **Клонуйте репозиторій**
```bash
git clone <repository-url>
cd image-hosting
```

2. **Створіть файл конфігурації `.env`**

Створіть файл `.env` в корені проєкту з наступними змінними:

```env
# Database Configuration
DB_NAME=imagehosting
DB_USER=postgres
DB_PASSWORD=your_secure_password_here
DB_HOST=postgres
DB_PORT=5432
```

**Важливо:** Змініть `DB_PASSWORD` на власний безпечний пароль. Файл `.env` автоматично ігнорується git'ом.

3. **Запустіть Docker Compose**

```bash
docker-compose up -d --build
```

Ця команда:
- Побудує всі необхідні Docker образи
- Створить та запустить контейнери
- Ініціалізує базу даних
- Налаштує volumes для persistent storage

4. **Перевірте статус**

```bash
docker-compose ps
```

Всі сервіси повинні мати статус "Up".

5. **Відкрийте додаток**

Перейдіть у браузері на `http://localhost`

## Структура проєкту

```
image-hosting/
│
├── backend/                  # Backend application
│   ├── app.py               # Main application entry point
│   ├── config.py            # Configuration management
│   ├── database.py          # Database layer (Repository pattern)
│   ├── handlers.py          # HTTP request handlers
│   ├── logger.py            # Logging configuration
│   ├── utils.py             # Helper functions
│   ├── Dockerfile           # Backend container definition
│   └── requirements.txt     # Python dependencies
│
├── frontend/                 # Frontend application
│   ├── css/
│   │   ├── upload.css       # Upload page styles
│   │   └── styles.css       # Global styles
│   ├── js/
│   │   ├── config.js        # API configuration
│   │   ├── upload.js        # Upload logic
│   │   ├── image_detail.js  # Detail page logic
│   │   └── tabs.js          # Tab navigation
│   ├── assets/              # Static assets (icons, images)
│   ├── upload.html          # Main page (upload & gallery)
│   └── image_detail.html    # Image details page
│
├── db/
│   └── 1-init.sql           # Database initialization script
│
├── backups/                  # Database backups (auto-generated)
│   └── auto_backup.sh       # Backup automation script
│
├── images/                   # Uploaded images storage (volume)
├── logs/                     # Application logs (volume)
│
├── nginx.conf               # Nginx configuration
├── docker-compose.yml       # Docker Compose orchestration
├── Makefile                 # Build automation shortcuts
├── .env                     # Environment variables (not in git)
├── .gitignore              # Git ignore rules
└── README.md               # This file
```

## API Documentation

### Endpoints

#### `GET /api/images`
Отримати список зображень з пагінацією.

**Query Parameters:**
- `page` (int, default: 1) — номер сторінки
- `limit` (int, default: 10) — кількість елементів на сторінці
- `order` (string, default: "desc") — порядок сортування ("asc" | "desc")

**Response:**
```json
{
  "items": [
    {
      "id": 1,
      "filename": "image.jpg",
      "original_name": "my_photo.jpg",
      "size": 245678,
      "extension": "jpg",
      "uploaded_at": "2026-06-08T10:30:00Z"
    }
  ],
  "pagination": {
    "total": 42,
    "pages": 5,
    "page": 1,
    "limit": 10
  }
}
```

#### `GET /api/images/{filename}`
Отримати інформацію про конкретне зображення.

**Response:**
```json
{
  "id": 1,
  "filename": "abc123.jpg",
  "original_name": "sunset.jpg",
  "size": 245678,
  "extension": "jpg",
  "uploaded_at": "2026-06-08T10:30:00Z"
}
```

#### `POST /api/images`
Завантажити нове зображення.

**Request:**
- Content-Type: `multipart/form-data`
- Body: form field `image` з файлом

**Response:**
```json
{
  "filename": "abc123.jpg",
  "message": "Image uploaded successfully"
}
```

#### `DELETE /api/images/{filename}`
Видалити зображення.

**Response:**
```json
{
  "message": "Image deleted successfully"
}
```

### Error Responses

Всі помилки повертаються у форматі:
```json
{
  "error": "Error description"
}
```

**HTTP статус коди:**
- `200` — Success
- `400` — Bad Request (валідація, неправильний формат)
- `404` — Not Found (зображення не знайдено)
- `500` — Internal Server Error

## Робота з проєктом

### Команди для розробки

#### Запуск та перезапуск

```bash
# Запустити всі сервіси
docker-compose up -d

# Запустити з rebuild
docker-compose up -d --build

# Перезапустити всі сервіси
docker-compose restart

# Перезапустити конкретний сервіс
docker-compose restart nginx     # Frontend changes
docker-compose restart backend   # Backend changes
docker-compose restart postgres  # Database changes
```

#### Зупинка та очищення

```bash
# Зупинити всі сервіси
docker-compose stop

# Зупинити та видалити контейнери
docker-compose down

# Видалити контейнери та volumes (УВАГА: видалить дані!)
docker-compose down -v
```

#### Логи та моніторинг

```bash
# Переглянути логи всіх сервісів
docker-compose logs -f

# Логи конкретного сервісу
docker-compose logs -f backend
docker-compose logs -f nginx
docker-compose logs -f postgres

# Останні N рядків
docker-compose logs --tail=100 backend
```

#### Робота з базою даних

```bash
# Підключитись до PostgreSQL CLI
docker exec -it postgres psql -U <DB_USER> -d <DB_NAME>

# Виконати SQL файл
docker exec -i postgres psql -U <DB_USER> -d <DB_NAME> < script.sql

# Створити backup вручну
docker exec postgres pg_dump -U <DB_USER> <DB_NAME> > backup_$(date +%Y%m%d_%H%M%S).sql

# Відновити з backup
docker exec -i postgres psql -U <DB_USER> -d <DB_NAME> < backup.sql
```

#### Перегляд стану

```bash
# Статус контейнерів
docker-compose ps

# Використання ресурсів
docker stats

# Інспекція контейнера
docker inspect backend
```

### Оновлення після змін

**Frontend зміни (HTML/CSS/JS):**
```bash
docker-compose restart nginx
# Або просто Ctrl+Shift+R в браузері (hard refresh)
```

**Backend зміни (Python):**
```bash
docker-compose restart backend
```

**Database schema зміни:**
```bash
# Застосувати міграцію
docker exec -i postgres psql -U <DB_USER> -d <DB_NAME> < db/migration.sql
```

**Docker configuration зміни:**
```bash
docker-compose down
docker-compose up -d --build
```

### Дебаг та troubleshooting

#### Проблема: Frontend зміни не відображаються

**Рішення:**
1. Hard refresh в браузері: `Ctrl+Shift+R` (Windows/Linux) або `Cmd+Shift+R` (Mac)
2. Перевірити чи volume правильно змонтований:
```bash
docker exec nginx ls -la /usr/share/nginx/html
```
3. Перезапустити nginx: `docker-compose restart nginx`

#### Проблема: Backend не відповідає

**Рішення:**
1. Перевірити логи:
```bash
docker-compose logs backend
```
2. Перевірити чи порт доступний:
```bash
docker-compose ps backend
```
3. Перезапустити: `docker-compose restart backend`

#### Проблема: Database connection failed

**Рішення:**
1. Перевірити чи PostgreSQL запущений:
```bash
docker-compose ps postgres
```
2. Перевірити credentials в `.env`
3. Перевірити чи база ініціалізована:
```bash
docker exec -it postgres psql -U <DB_USER> -d <DB_NAME> -c "\dt"
```

#### Проблема: Port already in use

**Рішення:**
```bash
# Windows
netstat -ano | findstr :80
taskkill /PID <PID> /F

# Linux/Mac
lsof -ti:80 | xargs kill -9
```

## Конфігурація

### Environment Variables (.env)

| Змінна | Опис | За замовчуванням |
|--------|------|------------------|
| `DB_NAME` | Назва бази даних | `imagehosting` |
| `DB_USER` | PostgreSQL користувач | `postgres` |
| `DB_PASSWORD` | PostgreSQL пароль | *(обов'язково)* |
| `DB_HOST` | Хост БД | `postgres` |
| `DB_PORT` | Порт БД | `5432` |

### Docker Volumes

- `postgres_data` — дані PostgreSQL (persistent)
- `./images` — завантажені зображення (bind mount)
- `./logs` — логи додатку (bind mount)
- `./backups` — бекапи БД (bind mount)

### Ports

- `80` — Nginx (HTTP)
- `8000-8009` — Backend API (internal)
- `5432` — PostgreSQL (internal, exposed для дебагу)

## Розробка

### Додавання нових функцій

1. **Backend API endpoint:**
   - Додати метод в `backend/handlers.py`
   - Додати роутинг в `backend/app.py`
   - Оновити `backend/database.py` якщо потрібна робота з БД

2. **Frontend feature:**
   - Додати HTML в `frontend/*.html`
   - Додати стилі в `frontend/css/*.css`
   - Додати логіку в `frontend/js/*.js`

3. **Database schema:**
   - Створити міграційний SQL файл в `db/`
   - Застосувати міграцію в running контейнері

### Code Style

**Python:**
- PEP 8 style guide
- Type hints рекомендовано
- Docstrings для публічних функцій

**JavaScript:**
- ES6+ features
- Async/await для асинхронних операцій
- Descriptive variable names

**SQL:**
- Snake_case для таблиць та колонок
- Explicit naming для constraints

## Бекапи та відновлення

### Автоматичні бекапи

Сервіс `backup` автоматично створює бекапи бази даних згідно з налаштуваннями в `backups/auto_backup.sh`.

### Ручний бекап

```bash
# Повний бекап
docker exec postgres pg_dump -U <DB_USER> <DB_NAME> > backup.sql

# Тільки схема
docker exec postgres pg_dump -U <DB_USER> --schema-only <DB_NAME> > schema.sql

# Тільки дані
docker exec postgres pg_dump -U <DB_USER> --data-only <DB_NAME> > data.sql
```

### Відновлення

```bash
# З SQL файлу
docker exec -i postgres psql -U <DB_USER> -d <DB_NAME> < backup.sql

# З directory-format backup
docker exec postgres pg_restore -U <DB_USER> -d <DB_NAME> /backups/backup_dir
```

## Deployment

### Production considerations

1. **Environment variables:**
   - Використовуйте сильні паролі
   - Не комітьте `.env` в репозиторій

2. **Nginx:**
   - Додайте SSL/TLS (Let's Encrypt)
   - Налаштуйте rate limiting
   - Додайте gzip compression

3. **PostgreSQL:**
   - Регулярні бекапи на зовнішнє сховище
   - Monitoring та alerting
   - Connection pooling

4. **Application:**
   - Використовуйте production WSGI server (gunicorn, uvicorn)
   - Налаштуйте proper logging level
   - Додайте health check endpoints

5. **Docker:**
   - Використовуйте конкретні версії образів (не `latest`)
   - Multi-stage builds для меншого розміру
   - Security scanning

## Troubleshooting

### Часті проблеми

**Q: Зображення не завантажуються**
A: Перевірте права доступу до папки `images/` та логи backend

**Q: База даних не ініціалізується**
A: Видаліть volume та пересоздайте: `docker-compose down -v && docker-compose up -d`

**Q: Nginx 502 Bad Gateway**
A: Backend не відповідає, перевірте чи контейнер backend запущений

**Q: Повільна робота**
A: Перевірте Docker Desktop ресурси (CPU/Memory limits)

## Ліцензія

MIT License - використовуйте вільно для особистих та комерційних проєктів.

## Контакти та підтримка

Для питань та багрепортів створюйте issues в репозиторії проєкту.

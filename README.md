# 📸 Image Hosting

Веб-додаток для завантаження, зберігання та перегляду зображень з підтримкою метаданих.

## ✨ Можливості

- 📤 Завантаження зображень через drag-and-drop або вибір файлів
- 🖼️ Перегляд галереї завантажених зображень
- 📊 Пагінація та сортування
- 🔍 Детальна інформація про кожне зображення
- 🗑️ Видалення зображень
- 📱 Адаптивний дизайн

## 🛠️ Технології

- **Backend**: Python 3.x з http.server
- **Frontend**: Vanilla JavaScript, HTML5, CSS3
- **Database**: PostgreSQL 17
- **Web Server**: Nginx (stable-alpine)
- **Containerization**: Docker Compose

## 🚀 Швидкий старт

### Вимоги

- Docker
- Docker Compose

### Встановлення

1. Клонуйте репозиторій:
```bash
git clone <repository-url>
cd image-hosting
```

2. Створіть файл `.env` з необхідними змінними:
```env
DB_NAME=your_db_name
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_HOST=postgres
DB_PORT=5432
```

3. Запустіть проєкт:
```bash
docker-compose up -d --build
```

4. Відкрийте браузер за адресою: `http://localhost`

## 📁 Структура проєкту

```
image-hosting/
├── backend/              # Backend API
│   ├── app.py           # Основний серверний файл
│   ├── config.py        # Конфігурація
│   ├── database.py      # Робота з базою даних
│   ├── handlers.py      # HTTP обробники
│   ├── logger.py        # Логування
│   └── utils.py         # Утиліти
├── frontend/            # Frontend статичні файли
│   ├── css/            # Стилі
│   ├── js/             # JavaScript
│   │   ├── config.js   # Конфігурація API
│   │   ├── upload.js   # Логіка завантаження
│   │   └── tabs.js     # Перемикання вкладок
│   ├── upload.html     # Головна сторінка
│   └── image_detail.html # Сторінка деталей зображення
├── db/                  # SQL скрипти
│   └── 1-init.sql      # Ініціалізація БД
├── nginx.conf          # Конфігурація Nginx
├── docker-compose.yml  # Docker Compose конфігурація
└── .env               # Змінні оточення (не в git)
```

## 🔧 Команди для розробки

### Перезапуск сервісів

```bash
# Перезапуск всього проєкту
docker-compose restart

# Перезапуск тільки frontend (nginx)
docker-compose restart nginx

# Перезапуск тільки backend
docker-compose restart backend

# Повна перебудова
docker-compose down
docker-compose up -d --build
```

### Перегляд логів

```bash
# Всі сервіси
docker-compose logs -f

# Тільки backend
docker-compose logs -f backend

# Тільки nginx
docker-compose logs -f nginx
```

### Робота з базою даних

```bash
# Підключення до PostgreSQL
docker exec -it postgres psql -U <DB_USER> -d <DB_NAME>

# Створення бекапу
docker exec -it postgres pg_dump -U <DB_USER> <DB_NAME> > backup.sql
```

## 🌐 API Endpoints

- `GET /api/images?page=1&limit=10&order=desc` - Отримати список зображень
- `GET /api/images/{filename}` - Отримати інформацію про зображення
- `POST /api/images` - Завантажити зображення
- `DELETE /api/images/{filename}` - Видалити зображення

## 📝 Примітки

- Завантажені зображення зберігаються в папці `images/`
- Логи додатку зберігаються в папці `logs/`
- Бекапи бази даних створюються автоматично в папці `backups/`
- Всі папки з даними додані в `.gitignore`

## 🔒 Безпека

- Файл `.env` не включений в репозиторій
- Nginx налаштований для віддачі статичних файлів
- Backend працює за проксі через Nginx
- PostgreSQL недоступна ззовні (тільки через expose)

## 📄 Ліцензія

MIT

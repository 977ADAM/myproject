# myproject

Интернет-магазин одежды на Django 5.2 с каталогом товаров, корзиной на основе сессий и кастомной моделью пользователя.

## Возможности

- **Каталог** (`main`) — категории, товары с несколькими изображениями, размерами и учётом остатков на складе (`ProductSize.stock`). Фильтрация по категориям через slug.
- **Корзина** (`cart`) — привязана к ключу сессии (покупки без обязательной регистрации), управляется через middleware и доступна в шаблонах через context processor. Добавление, обновление количества, удаление и очистка.
- **Пользователи** (`users`) — кастомная модель `CustomUser` с авторизацией по email вместо username, профиль с адресами доставки, регистрация/вход/выход и редактирование данных аккаунта.

## Стек

- Python 3.11
- Django 5.2
- PostgreSQL (`django.db.backends.postgresql`, драйвер psycopg 3)
- Pillow — обработка изображений товаров
- python-dotenv — загрузка переменных окружения
- [uv](https://docs.astral.sh/uv/) — управление зависимостями и окружением

## Структура проекта

```
myproject/
├── myproject/        # настройки, корневые URL, WSGI/ASGI
├── main/             # каталог: Category, Size, Product, ProductSize, ProductImage
├── cart/             # корзина: Cart, CartItem, middleware, context processor, template tags
├── users/            # кастомный пользователь CustomUser и личный кабинет
└── manage.py
```

## Установка и запуск

### 1. Клонировать репозиторий

```bash
git clone https://github.com/977ADAM/myproject.git
cd myproject
```

### 2. Установить зависимости

Проект использует [uv](https://docs.astral.sh/uv/). Установите его, если ещё не установлен:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh    # Windows: см. документацию uv
```

Затем установите зависимости — uv сам создаст виртуальное окружение `.venv` и установит точные версии из `uv.lock`:

```bash
uv sync
```

### 3. Настроить переменные окружения

Создайте файл `.env` в корне проекта:

```env
SECRET_KEY=your-secret-key

POSTGRES_DB=myproject
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your-password
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
```

### 4. Применить миграции и запустить сервер

Команды запускаются через `uv run` — активировать окружение вручную не нужно:

```bash
uv run python manage.py migrate
uv run python manage.py createsuperuser
uv run python manage.py runserver
```

Приложение будет доступно по адресу http://127.0.0.1:8000/, админка — http://127.0.0.1:8000/admin/.

## Основные маршруты

| URL                          | Назначение                       |
| ---------------------------- | -------------------------------- |
| `/`                          | Главная страница                 |
| `/catalog/`                  | Полный каталог                   |
| `/catalog/<category_slug>/`  | Каталог по категории             |
| `/product/<slug>`            | Карточка товара                  |
| `/cart/`                     | Корзина (модальное окно)         |
| `/users/register/`           | Регистрация                      |
| `/users/login/`              | Вход                             |
| `/users/profile/`            | Личный кабинет                   |
| `/admin/`                    | Админ-панель Django              |

## Заметки

- `DEBUG = True` в `settings.py` — перед деплоем обязательно отключите и заполните `ALLOWED_HOSTS`.
- Медиафайлы (`media/`) раздаются Django только при `DEBUG = True`; в продакшене настройте раздачу через веб-сервер.
- Авторизация выполняется по email — `USERNAME_FIELD = 'email'`.

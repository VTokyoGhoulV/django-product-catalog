# Django Product Catalog

Учебный каталог товаров с блогом. Пользователь регистрируется по email, заполняет профиль и добавляет товары. Владелец может редактировать свои записи, а модератор — управлять публикациями в пределах выданных прав.

**Стек:** Python 3.14, Django 6, PostgreSQL, Redis, Pillow, Bootstrap, Poetry.

## Возможности

- Товары и категории: создание, редактирование, удаление, фильтрация и постраничный вывод.
- Проверка цены, запрещённых слов и изображений JPEG/PNG до 5 МБ в форме товара.
- Профиль пользователя с аватаром.
- Блог с правами доступа, счётчиком просмотров и письмом на 100-м просмотре статьи.
- Кэширование товаров по категориям и страниц товаров на 15 минут.

## Запуск

Нужны Python 3.14, Poetry, PostgreSQL и Redis. Команды ниже — для PowerShell.

```powershell
git clone https://github.com/VTokyoGhoulV/django-product-catalog.git
cd django-product-catalog
poetry install --no-root
Copy-Item .env_sample .env
```

Заполните `.env`: `NAME`, `USER`, `PASSWORD`, `HOST`, `PORT` для PostgreSQL, `DEBUG=True` для локального запуска и свой `SECRET_KEY`. Создать ключ можно так:

```powershell
poetry run python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Создайте базу с именем из `NAME`. Для имени из шаблона:

```powershell
psql -U postgres -h localhost -c "CREATE DATABASE catalog_demo;"
```

Запустите Redis на `127.0.0.1:6379`. В `config/settings.py` выбрана база Redis № 1.

Для писем заполните `EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_HOST_USER`, `EMAIL_HOST_PASSWORD` и `EMAIL_RECIPIENT`. Последний адрес получает уведомления о просмотрах статей. Подключение использует TLS.

```powershell
poetry run python manage.py migrate
poetry run python manage.py createsuperuser
poetry run python manage.py runserver
```

Приложение: http://127.0.0.1:8000/. Каталог доступен после входа.
Регистрация — `/accounts/register/`, блог — `/blogs/posts_list/`, административная панель — `/admin/`.

## Пример данных

В пустую демонстрационную базу можно загрузить две категории и пять товаров:

```powershell
poetry run python manage.py loaddata Category_fixture.json Product_fixture.json
```

В административной панели назначьте товарам владельцев и включите публикацию. Изображения в репозиторий не входят — их можно добавить через форму товара.

Команда проекта `load_fixtures` удаляет существующие товары и категории. Для знакомства с проектом используйте стандартную `loaddata`, как в примере выше.

Проверка настроек: `poetry run python manage.py check`. Автоматических тестов пока нет.

## Ограничения

- Кэш не очищается после изменения, удаления или снятия товара с публикации. В категории или карточке могут оставаться старые данные до 15 минут, включая скрытый товар.
- Если письмо на 100-м просмотре статьи не отправится из-за ошибки SMTP, страница вернёт ошибку, а новое значение счётчика не сохранится.
- Форма контактов показывает подтверждение, но не сохраняет и не отправляет сообщение.
- Корзины, заказов и оплаты нет.
- Настройки предназначены для локальной разработки.

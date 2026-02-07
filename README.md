# Sonata JWT Auth

JWT-модуль для Sonata Framework: авторизация, refresh-токены и базовая таблица пользователей.

## Установка
```bash
composer require sonata/jwt-auth
```

## Требования
- `sonata/framework`
- `firebase/php-jwt`
- `ext-pdo`
- Переменная `JWT_SECRET`

## Миграции
Модуль использует таблицы `users` и `refresh_tokens`.

Если в приложении доступна команда:
```bash
php bin/console jwt:install
```

Или выполните SQL вручную из файла:
```
migrations/001_create_users_and_refresh_tokens.sql
```

## Эндпоинты
Контроллер `Sonata\JwtAuth\Controllers\AuthController` регистрируется автоматически:
- `POST /api/login` — логин (email, password)
- `POST /api/registration` — регистрация
- `POST /api/refresh` — обновление токена
- `POST /api/logout` — логаут
- `GET /api/me` — профиль по access token

Пример логина:
```http
POST /api/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

Ответ:
```json
{
  "access_token": "...",
  "refresh_token": "...",
  "token_type": "Bearer",
  "expires_in": 900
}
```

## Логика
- **Login**: проверяет `password_hash`, выдает access + refresh токены.
- **Refresh**: хранит хэш refresh-токена в `refresh_tokens`, отзывает старый при обновлении.
- **Logout**: отзывает все refresh-токены пользователя.
- **Me**: читает `Authorization: Bearer <token>` и валидирует JWT.

## Переменные окружения
- `JWT_SECRET` — ключ подписи JWT.

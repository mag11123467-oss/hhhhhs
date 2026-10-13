# Подсистема безопасности AccessEvent

## 1. Аутентификация

### Регистрация
- Пользователь указывает: username, email, password
- Пароль хешируется через Werkzeug (pbkdf2:sha256)
- Хранится только password_hash

### Вход (login)
- Пользователь вводит email и пароль
- Сервер проверяет хеш через check_password_hash()
- При успехе генерируется JWT-токен (access token, срок 1 час)
- Выдаётся refresh token (срок 30 дней)

### Проверка токена
- Каждый запрос: заголовок Authorization: Bearer <token>
- Сервер декодирует JWT, проверяет подпись и срок
- При истечении access token – обновление через refresh token

## 2. Хеширование паролей
- Метод: pbkdf2:sha256 (Werkzeug)
- generate_password_hash("pass123", method='pbkdf2:sha256')
- check_password_hash(stored_hash, "pass123")

## 3. Авторизация (RBAC)

| Роль | Права |
|------|-------|
| guest | Просмотр событий и категорий |
| user | Регистрация на события, чат, уведомления, профиль |
| organizer | Создание и управление событиями |
| admin | Управление пользователями, модерация |

Наследование: guest -> user -> organizer -> admin

## 4. JWT (JSON Web Token)
- Header: {"alg": "HS256", "typ": "JWT"}
- Payload: {"user_id": 1, "role": "organizer", "exp": 1700000000}
- Signature: HMAC-SHA256

## 5. Инклюзивные функции
- Поле is_deaf в users – адаптация интерфейса
- Визуальные уведомления вместо звуковых
- Текстовый чат вместо голосового
- Цветовая индикация статусов

# Создать запись на регистрацию

|  |  |
|------|------|
| URL | /api/v1/registrations |
| Service Method | POST |
| Description | Создает запись на регистрацию пользователя на курс |
| Return Type | JSON |
| Third-party service or system name | — |

## 1. List of changes

| Date | Responsible SA | JIRA | What was changed |
|------|------|------|------|
| 7.10.2026 | | | Первичное создание |

## 2. Input parameters

### 2.1 Header parameters

| Name | Type | Required | Description |
|------|------|------|------|
| Authorization | STRING | Yes | Bearer access-токен авторизации (`Bearer <access_token>`) |
| Content-Type | STRING | Yes | `application/json` |

### 2.2 Query params

Отсутствуют.

### 2.3 Path params

Отсутствуют.

### 2.4 Request body

| Name | Type | Max length | Required | Description |
|------|------|------|------|------|
| course_id | STRING | 36 | Yes | Уникальный идентификатор курса |
| user_id | STRING | 36 | Yes | Уникальный идентификатор пользователя |

## 3. Response parameters

| Name | Type | Max length | Description |
|------|------|------|------|
| success | BOOLEAN | — | `true` — запрос выполнен успешно, `false` — ошибка |
| errorMessage | STRING | — | Сообщение об ошибке (при `success: false`) |
| data | OBJECT | — | Объект с данными |
| data.id | STRING | 36 | Уникальный идентификатор |
| data.course_id | STRING | 36 | Уникальный идентификатор курса |
| data.user_id | STRING | 36 | Уникальный идентификатор пользователя |

## 4. Security requirements of method

- **Authentication**: обязательна — JWT Bearer access-токен в заголовке `Authorization`.
- **Rate limiting**: ограничение на количество запросов — не более 60 запросов в минуту с одного IP или токена.

## 5. How the service works

1. Клиент отправляет `POST /api/v1/registrations`.
2. Сервер валидирует входные параметры и права доступа.
3. Сервер выполняет операцию и формирует ответ.

### 5.1 Sequence diagram

Не требуется: метод не содержит сложных интеграционных сценариев.

### 5.2 Mapping

| Request body | DB column / value |
|------|------|
| id | `registrations.id` |
| course_id | `courses.id` |
| user_id | `users.id` |

## 6. Logging requirements

- Логировать: `user_id`, временную метку запроса, параметры запроса, HTTP-статус ответа.
- Логировать внутренние ошибки сервера (500) с уровнем `ERROR`.

## 7. Data model used by method

### 7.1 Table `registrations`

| Field | Type | Constraint | Description |
|------|------|------|------|
| `id` | `UUID` | PK, not null | Уникальный идентификатор |
| `course_id` | `UUID` | not null | Уникальный идентификатор курса |
| `user_id` | `UUID` | not null | Уникальный идентификатор пользователя |
| `start_date` |`DATE`| not null | Дата старта курса |

## 8. HTTP status codes

| HTTP status code | Description | Success | errorCode | errorMessage |
|------|------|------|------|------|
| 201 | Ресурс успешно создан | true | — | — |
| 400 | Некорректные параметры запроса | false | `bad_request` | "Описание ошибки валидации" |
| 403 | Доступ запрещён | false | `forbidden` | "Access denied" |
| 404 | Ресурс не найден | false | `not_found` | "Related entity not found" |
| 429 | Превышен лимит запросов | false | `rate_limit_exceeded` | "Too many requests" |
| 500 | Внутренняя ошибка сервера | false | `internal_server_error` | "An internal server error occurred" |

## 9. Example

### 9.1 Response 201 Created

**Request:**

```http
POST /api/v1/registrations
Authorization: Bearer <access_token>
Content-Type: application/json

{
    "course_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "990e8400-e29b-41d4-a716-446655440011",
    "start_date": "2026-10-07"
}
```

**Response (201):**

```json
{
  "success": true,
  "data": {
    "course_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "990e8400-e29b-41d4-a716-446655440011",
    "start_date": "2026-10-07"
  },
  "errorMessage": null
}
```

### 9.2 Response 400 Bad Request

**Request:**

```http
POST /api/v1/registrations
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "course_id": "550e8400-e29b-41d4-a716-446655440000"
}
```

**Response (400):**

```json
{
  "success": false,
  "data": null,
  "errorMessage": "Описание ошибки валидации"
}
```

### 9.3 Response 403 Forbidden

**Request:**

```http
POST /api/v1/registrations
Authorization: Bearer <access_token>
Content-Type: application/json

{
    "course_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "990e8400-e29b-41d4-a716-446655440011"
}
```

**Response (403):**

```json
{
  "success": false,
  "data": null,
  "errorMessage": "Access denied"
}
```

### 9.4 Response 404 Not Found

**Request:**

```http
POST /api/v1/registrations
Authorization: Bearer <access_token>
Content-Type: application/json

{
    "course_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "990e8400-e29b-41d4-a716-446655440011"
}
```

**Response (404):**

```json
{
  "success": false,
  "data": null,
  "errorMessage": "Related entity not found"
}
```

### 9.5 Response 429 Too Many Requests

**Request:**

```http
POST /api/v1/registrations
Authorization: Bearer <access_token>
Content-Type: application/json

{
    "course_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "990e8400-e29b-41d4-a716-446655440011"
}
```

**Response (429):**

```json
{
  "success": false,
  "data": null,
  "errorMessage": "Too many requests"
}
```

### 9.6 Response 500 Internal Server Error

**Request:**

```http
POST /api/v1/registrations
Authorization: Bearer <access_token>
Content-Type: application/json

{
    "course_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "990e8400-e29b-41d4-a716-446655440011"
}
```

**Response (500):**

```json
{
  "success": false,
  "data": null,
  "errorMessage": "An internal server error occurred"
}
```

## 10. Links

| Descriptions | Link |
|------|------|
| Swagger |  |
| Postman |  |
| Storage/table | `teams` |

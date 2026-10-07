# Удалить запись регистрации пользователя на курс

|  |  |
|------|------|
| URL | /api/v1/registrations/{registration_id}|
| Service Method | DELETE |
| Description | Удаляет запись регистрации пользователя на курс |
| Return Type | No Content |
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

### 2.2 Query params

Отсутствуют.

### 2.3 Path params

| Name | Type | Max length | Required | Description |
|------|------|------|------|------|
| registration_id | STRING | 36 | Yes | Уникальный идентификатор записи регистрации |

### 2.4 Request body

Отсутствует (метод DELETE).

## 3. Response parameters

Для успешного ответа `204 No Content` тело ответа отсутствует.

Для ошибок возвращается JSON:

| Name | Type | Max length | Description |
|------|------|------|------|
| success | BOOLEAN | — | `false` — ошибка |
| data | OBJECT | — | `null` |
| errorMessage | STRING | — | Сообщение об ошибке |

## 4. Security requirements of method

- **Authentication**: обязательна — JWT Bearer access-токен в заголовке `Authorization`.
- **Authorization**: Только администратор может удалить запись регистрации сотрудника на курс
- **Rate limiting**: ограничение на количество запросов — не более 20 запросов в минуту с одного IP или токена.

## 5. How the service works

1. Клиент отправляет `DELETE /api/v1/registrations/{registration_id}`.
2. Сервер валидирует входные параметры и права доступа.
3. Сервер выполняет операцию и формирует ответ.

### 5.1 Sequence diagram

Не требуется: метод не содержит сложных интеграционных сценариев.

### 5.2 Mapping

| Path param | DB column / value |
|------|------|
| project_id | `registration_id` |

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

## 8. HTTP status codes

| HTTP status code | Description | Success | errorCode | errorMessage |
|------|------|------|------|------|
| 204 | Операция успешно выполнена | true | — | — |
| 400 | Некорректные параметры запроса | false | `bad_request` | "Описание ошибки валидации" |
| 403 | Доступ запрещён | false | `forbidden` | "Access denied" |
| 404 | Ресурс не найден | false | `not_found` | "Resource not found" |
| 429 | Превышен лимит запросов | false | `rate_limit_exceeded` | "Too many requests" |
| 500 | Внутренняя ошибка сервера | false | `internal_server_error` | "An internal server error occurred" |

## 9. Example

### 9.1 Response 204 No Content

**Request:**

```http
DELETE /api/v1/registrations/{registration_id}
Authorization: Bearer <access_token>
```

**Response (204):**

```http
HTTP/1.1 204 No Content
```

### 9.2 Response 400 Bad Request

**Request:**

```http
DELETE /api/v1/registrations/invalid-id
Authorization: Bearer <access_token>
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
DELETE /api/v1/registrations/{registration_id}
Authorization: Bearer <access_token>
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
DELETE /api/v1/registrations/99999999-9999-9999-9999-999999999999
Authorization: Bearer <access_token>
```

**Response (404):**

```json
{
  "success": false,
  "data": null,
  "errorMessage": "Resource not found"
}
```

### 9.5 Response 429 Too Many Requests

**Request:**

```http
DELETE /api/v1/registrations/{registration_id}
Authorization: Bearer <access_token>
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
DELETE /api/v1/registrations/{registration_id}
Authorization: Bearer <access_token>
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
| Storage/table | `projects` |

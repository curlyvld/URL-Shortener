# URL Shortener

Сервис для сокращения URL-адресов на языке Go.

## Описание

URL Shortener — это веб-сервис, который позволяет создавать короткие ссылки для длинных URL-адресов. Сервис сохраняет соответствие между коротким алиасом и оригинальным URL, а затем перенаправляет пользователей на оригинальный адрес при обращении к короткой ссылке.

## Возможности

- Создание коротких ссылок для длинных URL
- Автоматическая генерация алиаса или использование пользовательского
- Перенаправление на оригинальный URL по короткой ссылке
- Базовая аутентификация для создания ссылок
- Хранение данных в SQLite
- Структурированное логирование
- Поддержка различных окружений (local, dev, prod)

## Требования

- Go 1.24 или выше
- SQLite3

## Установка

1. Клонируйте репозиторий:
```bash
git clone <repository-url>
cd url-shorteneer
```

2. Установите зависимости:
```bash
go mod download
```

3. Настройте конфигурацию (см. раздел "Конфигурация")

## Конфигурация

Сервис использует YAML-файл для конфигурации. Пример конфигурации находится в `config/local.yaml`:

```yaml
env: "local"
storage_path: "./storage/storage.db"
http_server:
  address: "localhost:8082"
  timeout: 4s
  idle_timeout: 60s
  user: "myuser"
  password: "mypass"
```

## API

### Создание короткой ссылки

**POST** `/url`

Создает новую короткую ссылку. Требуется базовая аутентификация.

**Заголовки:**
- `Authorization: Basic <base64(user:password)>`

**Тело запроса:**
```json
{
  "url": "https://example.com/very/long/url",
  "alias": "example"  // опционально, если не указан - будет сгенерирован автоматически
}
```

**Пример ответа:**
```json
{
  "status": "OK",
  "alias": "example"
}
```

**Пример с curl:**
```bash
curl -X POST http://localhost:8082/url \
  -u myuser:mypass \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/very/long/url", "alias": "example"}'
```

### Перенаправление по короткой ссылке

**GET** `/{alias}`

Перенаправляет на оригинальный URL по короткому алиасу.

**Пример:**
```bash
curl -L http://localhost:8082/example
```

Или просто откройте в браузере: `http://localhost:8082/example`


## Логирование

Сервис использует структурированное логирование с помощью `slog`:

- **local** — красивое форматирование в консоли с уровнем Debug
- **dev** — JSON-формат с уровнем Debug
- **prod** — JSON-формат с уровнем Info

## Зависимости

Основные зависимости:

- `github.com/go-chi/chi/v5` — HTTP роутер
- `github.com/go-chi/render` — рендеринг JSON ответов
- `github.com/go-playground/validator/v10` — валидация данных
- `github.com/ilyakaznacheev/cleanenv` — загрузка конфигурации
- `github.com/mattn/go-sqlite3` — драйвер SQLite
- `golang.org/x/exp/slog` — структурированное логирование




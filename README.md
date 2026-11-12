## Шаблон для третьего ДЗ по ИПР

Условие дз доступно по [ссылке](https://github.com/Nikfilk2030/missing_semester_hse_cs/blob/main/2026/amcs/year_1/hw_3/README.md)
# HW 3 — Docker

## Запуск

```bash
docker compose --env-file .env.example up -d --build
```

В `.env.example` можно изменить `HOST_PORT` (порт на компьютере), `PORT` (порт приложения) и `GREETING` (приветствие).

## Проверка приложения

```bash
curl http://localhost:8080/
curl -X POST http://localhost:8080/increment
docker compose ps
```

## Хранение данных

Приложение использует Redis. Данные сохраняются в именованном volume `redisdata`. Для сохранения данных Redis включён AOF.

Проверка сохранения счётчика:

```bash
docker compose --env-file .env.example down
docker compose --env-file .env.example up -d
curl http://localhost:8080/
```

После пересоздания контейнеров счётчик сохранил значение.

## Проверка отказа Redis

```bash
docker compose stop redis
curl -i http://localhost:8080/healthz
curl -i http://localhost:8080/readyz
curl -i -X POST http://localhost:8080/increment
docker compose start redis
```

При остановленном Redis:
- `/healthz` возвращает 200;
- `/readyz` возвращает 503;
- `POST /increment` возвращает 503.

После запуска Redis приложение восстанавливает работу без перезапуска.

---
name: pm-data-ingestion-architecture
description: >-
  Архитектура, конфигурация и APScheduler скеджулър на фоновата Python услуга PersonalManager-Data-Ingestion.
  Използвайте този скил при поддръжка, разширяване или диагностика на фоновата обработка на данни.
---

# PersonalManager Data Ingestion Architecture

## Общ Преглед

`PersonalManager-Data-Ingestion` е централизирана фонова услуга на Python, управлявана от **APScheduler** (`BlockingScheduler`). Тя консолидира всички периодични фонови процеси в екосистемата на PersonalManager: извличане на пазарни данни, валутни курсове от БНБ, автоматизирано генериране на AI отчети чрез Google Gemini, финансови преизчисления (депозити, регулярни разходи, абонаменти) и синхронизация на телематични логове от Google Cloud Storage.

```text
PersonalManager-Data-Ingestion/
├── app.py                     # Входна точка, BlockingScheduler и safe_execute обвивка
├── core/
│   ├── config.py              # Зареждане на .env, CRON графици и глобални настройки
│   └── utils.py               # Singleton MongoClient, UTC хелпъри, JobStatuses логване и SMTP
├── modules/
│   ├── export_bnb.py          # Теглене на валутни курсове от БНБ с weekend fallback
│   ├── market_exporter.py     # Извличане на фундаментални данни от StockAnalysis.com
│   ├── ai_data.py             # AI темпо/пробег на автомобили и 30-дневен валутен анализ
│   ├── stock_ai_data.py       # AI досиета за акции и ETF-и с runtime кеширане
│   ├── daily_automation.py    # Лихви по депозити, регулярни разходи, абонаменти и горива
│   └── gcp_processor.py      # Обработка на Excel телематични логове от GCS bucket
└── prompts/                   # Текстови шаблони за Google Gemini LLM
```

---

## Архитектурни Принципи и Оптимизации

### 1. Планировчик на Задачи (`BlockingScheduler`)
- Използва се `apscheduler.schedulers.blocking.BlockingScheduler` вместо фонов цикъл (`while True`). Това елиминира излишното процесорно натоварване (busy waiting).
- **Graceful Termination**: Регистрирани са `SIGINT` и `SIGTERM` сигнални хендлъри, осигуряващи чисто изключване на контейнера (`scheduler.shutdown()`) при деплой или рестарт в Docker.

### 2. Singleton MongoDB Connection Pool (`_mongo_client`)
- В `core/utils.py` инстанцията на `MongoClient` се кешира глобално (`_mongo_client`).
- Предотвратява изчерпване на сокетите и connection pool лимитите в **MongoDB Atlas** при чести стартирания на задачи.

### 3. UTC Нормализация (`ensure_utc`)
- Всички дати, четени от MongoDB или външни източници, се нормализират до UTC timezone-aware обекти чрез помощната функция `ensure_utc(dt)`.
- Предотвратява грешки от тип `can't compare offset-naive and offset-aware datetimes`.

### 4. Мрежова Защита & Timeouts
- **SMTP клиент**: Защитен с експлицитен таймаут (`smtplib.SMTP(..., timeout=20)`), предотвратяващ зависване на фоновите нишки при проблем с пощенския сървър.
- **HTTP заявки (Web Scraping)**: Ограничени с 15-секунден таймаут и ротация на реални Chrome `User-Agent` хедъри за преодоляване на 403 Forbidden защити.

---

## Безопасно Изпълнение и Логване (`safe_execute`)

Всяка периодична задача се извиква през централната обвивка `safe_execute(task_name, func)` в `app.py`:
1. Измерва точното време на изпълнение (`DurationSeconds`).
2. Прихваща всички неизловени изключения и ги маркира като `Status = "FAILED"`.
3. Записва структуриран запис в колекция `JobStatuses` в MongoDB:
   - `JobName`: Име на задачата
   - `Status`: `"SUCCESS"` или `"FAILED"`
   - `DurationSeconds`: Време за изпълнение
   - `Error`: Описание на грешката (при неуспех)
   - `ExecutedAt`: UTC време на стартиране
4. При критична грешка незабавно изпраща алармено имейл известие чрез `core.utils.send_email`.

---

## ⚙️ CRON Графици и Настройки в `.env`

Всички периодичности са конфигурируеми през стандартен Cron синтаксис (5 полета):

| Задача | ENV Променлива | По подразбиране (UTC) | Описание |
|---|---|---|---|
| **AI Data** | `CRON_AI_DATA` | `0 6 * * *` | Всяка сутрин в 06:00 (Коли и Валути) |
| **BNB Export** | `CRON_BNB_EXPORT` | `0 17 * * *` | Всеки ден в 17:00 (Валутни курсове от БНБ) |
| **Market Exporter** | `CRON_MARKET_EXPORT` | `0 1 * * *` | Всяка нощ в 01:00 (Фундаменти от StockAnalysis) |
| **Stock AI** | `CRON_STOCK_AI` | `0 4 * * 0` | Всяка неделя в 04:00 (AI досиета за акции) |
| **Daily Automation** | `CRON_DAILY_AUTOMATION` | `0 2 * * *` | Всяка нощ в 02:00 (Депозити, разходи, баланси) |
| **GCP Processor** | `CRON_GCP_PROCESSOR` | `0 3 * * *` | Всяка нощ в 03:00 (GCS телематика и пробег) |

### Допълнителни Променливи в `.env`

| Променлива | Описание | Примерна стойност |
|---|---|---|
| `MONGO_URI` | MongoDB Atlas Connection String | `mongodb+srv://user:pass@cluster.mongodb.net/?retryWrites=true&w=majority` |
| `DB_NAME` | Име на базата данни | `PersonalManager` |
| `GEMINI_API_KEY` | API ключ за Google Gemini | `AIzaSy...` |
| `AI_MODEL` | Модел за анализи на коли и валути | `gemini-2.5-flash` |
| `AI_STOCK_MODEL` | Модел за подробни досиета на акции | `gemini-2.5-flash` / `gemini-3-flash-preview` |
| `ENABLE_AI_SEARCH_GROUNDING` | Включва Google Search Grounding за търсене на живи борсови данни и дивиденти | `True` |
| `RATE_LIMIT_DELAY` | Пауза между AI / Scraping заявки (сек.) | `3` |
| `EXCHANGE_RATE_DAYS` | Дни за анализ на валутни трендове | `30` |

---

## 🚀 Продукционен Деплой & Инфраструктура

- **Хост машина**: GCP Compute Engine VM `papmgrsrv01` (`34.28.65.74`)
- **Потребител**: `papun`
- **Docker контейнер**: `papmgr2-data-ingestion`
- **Docker мрежа**: `papunov`
- **Конфигурационен файл**: `/home/papun/papmgr02/env.env`
- **CI/CD Build**: Автоматичен GitHub Actions build на Docker образ `ghcr.io/papun/personalmanager-data-ingestion:latest`.
- **Команда за стартиране в Production**:
  ```bash
  docker run -d \
    --name papmgr2-data-ingestion \
    --network papunov \
    --restart unless-stopped \
    --env-file /home/papun/papmgr02/env.env \
    ghcr.io/papun/personalmanager-data-ingestion:latest
  ```

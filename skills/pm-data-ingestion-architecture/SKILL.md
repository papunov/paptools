---
name: pm-data-ingestion-architecture
description: >-
  Архитектура, конфигурация и APScheduler скеджулър на фоновата Python услуга PersonalManager-Data-Ingestion.
  Използвайте този скил при поддръжка, разширяване или диагностика на фоновата обработка на данни.
---

# PersonalManager Data Ingestion Architecture

## Общ Преглед

`PersonalManager-Data-Ingestion` е централизирана фонова услуга на Python, управлявана от **APScheduler** (BackgroundScheduler). Тя консолидира фоновите процеси по извличане на пазарни данни, валутни курсове, автоматизирано генериране на AI отчети и OCR обработка на касови бележки.

```text
PersonalManager-Data-Ingestion/
├── app.py                # Входна точка и APScheduler конфигурация
├── core/
│   ├── config.py         # Зареждане на .env и глобални настройки
│   └── utils.py          # База данни (MongoDB), логване в JobStatuses и имейл известия
├── modules/
│   ├── ai_data.py        # AI анализ на автомобили и валути
│   ├── export_bnb.py     # Теглене на валутни курсове от БНБ
│   ├── market_exporter.py# Извличане на фундаментални данни от StockAnalysis.com
│   ├── stock_ai_data.py  # AI досиета и отчети за акции
│   ├── daily_automation.py # Повтарящи се разходи и автоматизации
│   └── gcp_processor.py # OCR и GCP обработка на документи
└── prompts/              # Текстови шаблони за OpenAI/LLM
```

## Безопасно Изпълнение и Логване (`safe_execute`)

Всяка планирана задача се изпълнява през `safe_execute(task_name, func)` в `app.py`:
- Измерва времето на изпълнение (`DurationSeconds`).
- Записва резултата (`Success` / `Failed`) в колекция `JobStatuses` в MongoDB.
- При възникване на критична грешка изпраща имейл известие чрез `core.utils.send_email`.

## CRON Настройки в `.env`

| Задача | ENV Променлива | По подразбиране | Описание |
|---|---|---|---|
| AI Data | `CRON_AI_DATA` | `0 6 * * *` | Всяка сутрин в 06:00 |
| BNB Export | `CRON_BNB_EXPORT` | `0 17 * * *` | Всеки ден в 17:00 |
| Market Exporter | `CRON_MARKET_EXPORT` | `0 1 * * *` | Всяка нощ в 01:00 |
| Stock AI | `CRON_STOCK_AI` | `0 4 * * 0` | Всяка неделя в 04:00 |

## Инфраструктура & Docker

Приложението е контейнеризирано с `Dockerfile` и разчита на променливите от `.env` за връзка с MongoDB (`MONGO_URI`) и OpenAI/GCP API ключове.

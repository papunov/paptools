# paptools - Antigravity Skills & Plugin Module

Модул със скилове, база знания и MCP инструменти за Antigravity AI, обслужващ екосистемата **PersonalManager**.

## 📚 База Знания (Knowledge Base)

- **[Централна База Знания (KNOWLEDGEBASE.md)](./KNOWLEDGEBASE.md)** - Пълен пътеводител, системна архитектура, 51 MongoDB Atlas колекции, домейни, стандарти и навигация.

## 📁 Структура

```text
paptools/
├── plugin.json               # Манифест на модула
├── README.md                 # Описание на модула
├── KNOWLEDGEBASE.md          # Централна база знания (Knowledge Base)
├── .gitignore                # Git игнорирани файлове
├── mcp_config.json.example   # Примерен темплейт за MongoDB Atlas MCP сървър
├── mcp_config.json           # Локална конфигурация с реални секрети (в .gitignore)
└── skills/                   # Папка със скилове (13 бр.)
    ├── pm-knowledgebase/     # Главна база знания & индекс
    ├── pm-architecture/      # .NET Blazor, Web API & SysFunctions архитектура
    ├── pm-mongodb-database/  # MongoDB Atlas схема (51 колекции) & MCP
    ├── pm-blazor-ui/         # Blazor Server UI стандарти & компоненти
    ├── pm-finance-core/      # Транзакции, заплати, спестявания
    ├── pm-investments/       # Акции, дивиденти, взаимни фондове, БНБ курсове
    ├── pm-car-maintenance/   # Автомобили & сервизна поддръжка
    ├── pm-telematics-fuel/   # Телематични логове, горива & лизинг
    ├── pm-data-ingestion-architecture/ # Python фонова услуга & APScheduler
    ├── pm-data-ingestion-modules/      # Модули за обработка (BNB, Market, OCR)
    ├── pm-data-ingestion-prompts/      # AI промптове & шаблони за анализ
    ├── pm-ai-analytics/      # AI анализи за акции/валути & JobStatuses
    └── pm-db-migrations/     # Скриптове & миграции на база данни
```

## Как да инсталирате на нов компютър

При клониране на `paptools` на нов компютър:

1. Клонирайте репозиторията в глобалната папка с плъгини:
   ```bash
   git clone https://github.com/ВАШИЯ-ПОТРЕБИТЕЛ/paptools.git ~/.gemini/config/plugins/paptools
   ```
2. Копирайте темплейта за MCP сървъра:
   ```bash
   cp mcp_config.json.example mcp_config.json
   ```
3. Отворете `mcp_config.json` и попълнете вашия съществуващ **MongoDB Atlas connection string**.


---
name: pm-knowledgebase
description: >-
  Централна база знания (Knowledge Base) и главен индекс за екосистемата PersonalManager и модула paptools: системна архитектура, 51 MongoDB Atlas колекции, домейни (финанси, инвестиции, автомобили, data ingestion, AI анализи), пълна карта на всички 13 скила, MCP инструменти и стандарти за разработка.
---

# PersonalManager & paptools Knowledge Base (Централен Справочник)

Тази база знания служи като **главен индекс** и **Single Source of Truth** за екосистемата **PersonalManager** и Antigravity плъгина **paptools**.

> 📌 Пълният документ на базата знания е достъпен тук: [`KNOWLEDGEBASE.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/KNOWLEDGEBASE.md)

---

## 🗺️ Карта на Всички Скилове в paptools

Модулът `paptools` съдържа 13 специализирани скила, обхващащи всяка част от системата:

```text
paptools Skills Map
├── 🧠 pm-knowledgebase              [Централна база знания и главен индекс]
├── 🏗️ pm-architecture               [Архитектура: .NET Blazor Server, Web API, SysFunctions, Secrets]
├── 🗄️ pm-mongodb-database          [MongoDB Atlas: 51 колекции, 5 домейни, BSON типове, MCP, индекси]
├── 🎨 pm-blazor-ui                  [Blazor UI: Code-behind, цветова палитра, Bootstrap компоненти]
├── 💳 pm-finance-core               [Финанси: Транзакции, Заплати, Спестявания, Рекурентни разходи]
├── 📊 pm-investments                [Инвестиции: Акции, Брокери, Дивиденти, БНБ курсове, Фондове]
├── 🔧 pm-car-maintenance            [Автомобили: Профили, Планови обслужвания, Извънредни ремонти]
├── ⛽ pm-telematics-fuel            [Телематика: Дневници от пътувания, Горива, Разход, Лизинги]
├── ⚙️ pm-data-ingestion-architecture [Data Ingestion: Python услуга, APScheduler, Docker/Systemd]
├── 🧩 pm-data-ingestion-modules     [Data Ingestion: Модули - BNB, Market, Stock AI, GCP OCR]
├── 📝 pm-data-ingestion-prompts     [Data Ingestion: AI Промптове и шаблони за анализ]
├── 🤖 pm-ai-analytics               [AI Анализи: Stock AI, BNB AI, Receipt OCR, JobStatuses]
└── 🛠️ pm-db-migrations              [MongoDB Миграции: Скриптове, преименуване, рефакториране]
```

---

## 📐 Бърз Справочник по Архитектура

- **Blazor UI & API (`.NET`)**:
  - `Web.ManagerV2`: Blazor Server UI (използва Code-Behind модел `.razor` + `.razor.cs`).
  - `Web.API`: REST контролери за мобилни приложения и външни интеграции.
  - `SysFunctions`: Валидатори, математически конвертори, БНБ изчисления.
  - `DataModels`: MongoDB BSON модели.
- **Data Ingestion Engine (`Python 3.11+`)**:
  - Услуга `PersonalManager-Data-Ingestion`.
  - Използва `APScheduler` за фонови планирани задачи.
  - Интеграция с `Google Document AI` (OCR) и `Google Gemini API` (AI Stock & BNB Analysis).
- **База Данни (`MongoDB Atlas Cloud`)**:
  - Сървър: `papcl01-wqwkg.azure.mongodb.net`
  - База: `PersonalManager` (51 колекции).

---

## 🗄️ Списък на Колекциите по Домейни (51 Колекции)

### 💰 1. Финанси (16)
`Transactions`, `Salaries`, `SumSalaries`, `Categories`, `PlannedExpenses`, `RecurringExpenses`, `Subscriptions`, `ConvertedTransactions`, `ConvertedSalaries`, `Savings`, `SavingTransactions`, `FrequentComments`, `TransactionAutoCompletes`, `TransactionsAutoCompletes`, `Notes`, `Reminders`, `Calculations`.

### 📈 2. Инвестиции & AI (17)
`Stocks`, `InvestStockOrders`, `StockPrices`, `StockFundaments`, `InvestmentBrokers`, `BrokerFees`, `Cashbacks`, `CashInterests`, `CashCurrencies`, `Dividends`, `Funds`, `FundOrders`, `Deposits`, `DepositPayments`, `BNBRates`, `AIStocksInformations`, `AIBNBRates`.

### 🚗 3. Автомобили & Телематика (8)
`Cars`, `CarServices`, `CarRepairs`, `CarDrivingStats`, `FuelRecords`, `FuelConsumptionAnalysis`, `LeaseContracts`, `UserSettings`.

### 🧾 4. OCR & Бележки (3)
`Receipts`, `ReceiptMaps`, `ReceiptExpressions`.

### ⚙️ 5. Системен & Логове (5)
`Users`, `UserMappings`, `Settings`, `JobStatuses`, `Logs`.

---

## 🔌 MongoDB Atlas MCP Tools Quick Reference

Агентът разполага с 31 MCP инструмента през `mongodb-mcp-server`:
- `find`: Заявки по филтър (`filter`, `sort`, `limit`).
- `count`: Преброяване на документи.
- `aggregate`: Изпълнение на агрегиращи пайплайни (`$match`, `$group`, `$sort`).
- `collection-schema`: Търсене на типовете и полетата в дадена колекция.
- `list-collections`: Списък на всички 51 колекции.

*Всички заявки ползват `"connectionId": "preconfigured"` и `"database": "PersonalManager"`.*

---

## 🚀 Навигация към Специализираните Скилове

- За архитектурни въпроси и `.NET` структури ➔ [`pm-architecture`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-architecture/SKILL.md)
- За базата данни, схеми и MCP заявки ➔ [`pm-mongodb-database`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-mongodb-database/SKILL.md)
- За Blazor Server UI ➔ [`pm-blazor-ui`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-blazor-ui/SKILL.md)
- За финансовата логика ➔ [`pm-finance-core`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-finance-core/SKILL.md)
- За акции, дивиденти и БНБ ➔ [`pm-investments`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-investments/SKILL.md)
- За автомобили, горива и лизинг ➔ [`pm-car-maintenance`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-car-maintenance/SKILL.md) & [`pm-telematics-fuel`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-telematics-fuel/SKILL.md)
- За Python фонови задачи и AI анализи ➔ [`pm-data-ingestion-architecture`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-data-ingestion-architecture/SKILL.md) & [`pm-ai-analytics`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-ai-analytics/SKILL.md)

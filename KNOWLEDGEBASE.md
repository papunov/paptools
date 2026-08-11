# PersonalManager & paptools Knowledge Base (Централна База Знания)

Добре дошли в официалната база знания за **paptools** – централният модул със скилове, архитектурни шаблони, схеми на данни и MCP интеграции за **PersonalManager**.

---

## 📚 Съдържание

1. [Общ Преглед на paptools & PersonalManager](#1-общ-преглед-на-paptools--personalmanager)
2. [Карта на Скиловете (Skills Registry)](#2-карта-на-скиловете-skills-registry)
3. [Системна Архитектура & Технологичен Стек](#3-системна-архитектура--технологичен-стек)
4. [База Данни MongoDB Atlas & Схеми (51 Колекции)](#4-база-данни-mongodb-atlas--схеми-51-колекции)
5. [Справочник по Функционални Домейни](#5-справочник-по-функционални-домейни)
   - [💰 5.1 Финанси, Бюджети & Спестявания](#-51-финанси-бюджети--спестявания)
   - [📈 5.2 Инвестиции, Брокери & AI Анализи](#-52-инвестиции-брокери--ai-анализи)
   - [🚗 5.3 Автомобили, Телематика & Горива](#-53-автомобили-телематика--горива)
   - [🧾 5.4 OCR Scan & Касови Бележки](#-54-ocr-scan--касови-бележки)
   - [⚙️ 5.5 Data Ingestion & Фонова Обработка (Python)](#️-55-data-ingestion--фонова-обработка-python)
6. [Интеграция с MongoDB Atlas MCP Server](#6-интеграция-с-mongodb-atlas-mcp-server)
7. [Стандарти за Разработка & UI (Blazor Server & Python)](#7-стандарти-за-разработка--ui-blazor-server--python)
8. [Миграции и Поддръжка на Данни](#8-миграции-и-поддръжка-на-данни)

---

## 1. Общ Преглед на paptools & PersonalManager

**PersonalManager** е комплексна екосистема за пълно управление на лични финанси, инвестиционни портфейли, сервизна поддръжка и телематика на автомобили, автоматизиран OCR анализ на бележки и AI прогнози на фондовите и валутните пазари.

**paptools** е Antigravity плъгин/модул, осигуряващ специализирани AI скилове (`skills`), конфигурация за **MongoDB Atlas MCP Server** и стандартизирани правила за работа на AI агенти в системата.

---

## 2. Карта на Скиловете (Skills Registry)

Всички скилове се намират в папка `skills/` на модула `paptools` и предоставят контекст при конкретни задачи:

| Скил | Описание & Предназначение | Файл |
| :--- | :--- | :--- |
| 🧠 **`pm-knowledgebase`** | Главна база знания и индекс на цялата екосистема `PersonalManager` и `paptools`. | [`skills/pm-knowledgebase/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-knowledgebase/SKILL.md) |
| 🏗️ **`pm-architecture`** | Архитектура на .NET Blazor Server, Web API, SysFunctions и защитена конфигурация. | [`skills/pm-architecture/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-architecture/SKILL.md) |
| 🗄️ **`pm-mongodb-database`** | Схема на production MongoDB Atlas базата (51 колекции, индекси, BSON типове, MCP). | [`skills/pm-mongodb-database/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-mongodb-database/SKILL.md) |
| 🎨 **`pm-blazor-ui`** | Стандарти за Blazor UI компоненти, цветова палитра, карти, форми и Code-Behind модел. | [`skills/pm-blazor-ui/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-blazor-ui/SKILL.md) |
| 💳 **`pm-finance-core`** | Бизнес правила за транзакции, заплати, планирани/периодични разходи и спестявания. | [`skills/pm-finance-core/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-finance-core/SKILL.md) |
| 📊 **`pm-investments`** | Модели за акции, взаимни фондове, дивиденти, БНБ валутни курсове и брокерски поръчки. | [`skills/pm-investments/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-investments/SKILL.md) |
| 🔧 **`pm-car-maintenance`** | Управление на автомобили, планирани обслужвания и извънредни ремонти. | [`skills/pm-car-maintenance/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-car-maintenance/SKILL.md) |
| ⛽ **`pm-telematics-fuel`** | Телематични логове (`CarDrivingStats`), зареждания (`FuelRecords`), разход и лизинги. | [`skills/pm-telematics-fuel/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-telematics-fuel/SKILL.md) |
| ⚙️ **`pm-data-ingestion-architecture`** | Архитектура и APScheduler на фоновата Python услуга `PersonalManager-Data-Ingestion`. | [`skills/pm-data-ingestion-architecture/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-data-ingestion-architecture/SKILL.md) |
| 🧩 **`pm-data-ingestion-modules`** | Подробно ръководство за модулите в Data-Ingestion (BNB, Market, Stock AI, GCP OCR). | [`skills/pm-data-ingestion-modules/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-data-ingestion-modules/SKILL.md) |
| 📝 **`pm-data-ingestion-prompts`** | Управление и конфигуриране на AI промптовете и шаблоните за анализ. | [`skills/pm-data-ingestion-prompts/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-data-ingestion-prompts/SKILL.md) |
| 🤖 **`pm-ai-analytics`** | AI анализи за акции (`AIStocksInformations`), валути (`AIBNBRates`), OCR rules & JobStatuses. | [`skills/pm-ai-analytics/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-ai-analytics/SKILL.md) |
| 🛠️ **`pm-db-migrations`** | Скриптове за миграция на данни, рефакториране на MongoDB схеми и поддръжка. | [`skills/pm-db-migrations/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-db-migrations/SKILL.md) |

---

## 3. Системна Архитектура & Технологичен Стек

Екосистемата на PersonalManager е изградена от два основни компонента:

```text
┌─────────────────────────────────────────────────────────────────┐
│                    PersonalManager Ecosystem                    │
└─────────────────────────────────────────────────────────────────┘
                                │
        ┌───────────────────────┴───────────────────────┐
        ▼                                               ▼
┌───────────────────────────────┐               ┌───────────────────────────────┐
│     PersonalManager (.NET)    │               │ Data Ingestion Engine (Python)│
│  Blazor Server UI & Web API   │               │   APScheduler & AI Analytics  │
└───────────────┬───────────────┘               └───────────────┬───────────────┘
                │                                               │
                └───────────────────────┬───────────────────────┘
                                        ▼
                        ┌───────────────────────────────┐
                        │   MongoDB Atlas (Cloud DB)    │
                        │    Database: PersonalManager  │
                        │        (51 Collections)       │
                        └───────────────────────────────┘
```

### Основен стек:
- **Уеб интерфейс & API**: `.NET` / `Blazor Server` (`Web.ManagerV2`), `Web.API`, `SysFunctions`, `DataModels`.
- **База данни**: `MongoDB Atlas` (`papcl01-wqwkg.azure.mongodb.net`, DB: `PersonalManager`).
- **Фонова обработка & AI**: `Python 3.11+`, `APScheduler`, `pymongo`, `Google Document AI`, `Google Gemini API`.
- **MCP Сървър**: `mongodb-mcp-server` с преконфигуриран Atlas Connection String.

---

## 4. База Данни MongoDB Atlas & Схеми (51 Колекции)

Базата данни **PersonalManager** съдържа **51 колекции**, разпределени в 5 основни домейни:

```text
PersonalManager Database (51 Collections)
├── 💰 Финанси & Спестявания (16)  : Transactions, Salaries, SumSalaries, Categories, PlannedExpenses...
├── 📈 Инвестиции & AI (17)       : Stocks, InvestStockOrders, Dividends, StockPrices, AIStocksInformations...
├── 🚗 Автомобили & Телематика (8) : Cars, CarServices, CarRepairs, CarDrivingStats, FuelRecords...
├── 🧾 OCR Scan & Бележки (3)      : Receipts, ReceiptMaps, ReceiptExpressions
└── ⚙️ Системни & Логове (5)       : Users, UserMappings, Settings, JobStatuses, Logs
```

### Важни правила за работа с типовете:
- **`ObjectId`**: Primary key (`_id`) и Foreign Keys (`UserId`, `SalaryId`, `CarId`, `BrokerId`) са BSON `ObjectId`.
- **`Decimal128`**: Финансови суми и цени на акции се записват като `Decimal128` в MongoDB, а в C# са `decimal` / Python `Decimal128`.
- **`DateTime`**: Всички дати се съхраняват в UTC format (`BsonDateTime`).

---

## 5. Справочник по Функционални Домейни

### 💰 5.1 Финанси, Бюджети & Спестявания
- **`Transactions`**: Източник на истината за всички разходи и приходи (`UserId`, `SalaryId`, `CategoryId`, `Sum`, `Expense`, `AddedToSavings`, `AddedOn`).
- **`Salaries`**: Месечни бюджетни рамки (`UserId`, `Name`, `Month`, `Year`, `Sum`, `Locked`).
- **`Savings` & `SavingTransactions`**: Спестовни сметки и движения по тях.
- **`RecurringExpenses` & `PlannedExpenses`**: Периодични и планирани разходи.

### 📈 5.2 Инвестиции, Брокери & AI Анализи
- **`Stocks` & `InvestStockOrders`**: Портфейлни позиции и транзакции при брокери (Trading 212, Interactive Brokers).
- **`Dividends`, `BrokerFees`, `CashInterests`, `Cashbacks`**: Допълнителни финансови потоци от инвестиции.
- **`BNBRates` & `AIBNBRates`**: Курсове на БНБ и AI прогнозни анализи.
- **`AIStocksInformations`**: Автоматично генерирани AI фундаментални и технически доклади за всяка притежавана акция.

### 🚗 5.3 Автомобили, Телематика & Горива
- **`Cars`, `CarServices`, `CarRepairs`**: Автомобилен парк, планирано обслужване и извънредни ремонти.
- **`CarDrivingStats`**: Автоматично импортирани телематични логове от пътувания (`DistanceKm`, `TimeMinutes`, `IgnitionTime`).
- **`FuelRecords` & `FuelConsumptionAnalysis`**: Зареждания на гориво и изчисление на реалния разход за 100 км.
- **`LeaseContracts`**: Лизингови договори и потвърдени вноски.

### 🧾 5.4 OCR Scan & Касови Бележки
- **`Receipts`**: Резултати от сканиране на бележки чрез Google Document AI.
- **`ReceiptMaps` & `ReceiptExpressions`**: Мапинг правила и Regular Expressions за автоматично разпознаване на търговци и категории.

### ⚙️ 5.5 Data Ingestion & Фонова Обработка (Python)
- **Услуга**: `PersonalManager-Data-Ingestion` (Python 3.11+ + `APScheduler`).
- **Модули**:
  - `bnb_exporter`: Ежедневен извличащ модул за валутни курсове от БНБ.
  - `market_exporter`: Извличане на цени и фундаментални показатели на акции от Financial APIs / Yahoo Finance.
  - `stock_ai`: AI анализ на акции чрез Google Gemini API.
  - `gcp_document_ocr`: Обработка на сканирани касови бележки чрез OCR.
  - `daily_automation`: Изпълнение на периодични задачи и проверка на статусите.
- **`JobStatuses`**: Мониторинг лог за всяко стартиране на фонова задача (`JobName`, `Status`, `DurationSeconds`, `Error`, `ExecutedAt`).

---

## 6. Интеграция с MongoDB Atlas MCP Server

За директна инспекция и манипулация на данните чрез Antigravity се използва официалният `mongodb-mcp-server`.

- **`connectionId`**: `"preconfigured"`
- **`database`**: `"PersonalManager"`

### Примерни MCP команди:
```json
// Търсене на последни 5 транзакции
{
  "connectionId": "preconfigured",
  "database": "PersonalManager",
  "collection": "Transactions",
  "filter": { "Expense": true },
  "sort": { "AddedOn": -1 },
  "limit": 5
}
```

---

## 7. Стандарти за Разработка & UI (Blazor Server & Python)

1. **Blazor UI (`Web.ManagerV2`)**:
   - Задължително използване на **Code-Behind pattern** (`.razor` + `.razor.cs`).
   - Спазване на тъмна/светла цветова палитра, съгласувана в `pm-blazor-ui`.
   - Изолирани CSS компоненти и унифицирани Bootstrap карти.
2. **Python (`PersonalManager-Data-Ingestion`)**:
   - `APScheduler` за периодични задачи.
   - Използване на `pymongo` с изрично обработване на `Decimal128` и `ObjectId`.
   - Логиране на всички грешки в колекцията `JobStatuses`.

---

## 8. Миграции и Поддръжка на Данни

При рефакториране на схеми или преименуване на полета в MongoDB:
- Прегледайте скила [`pm-db-migrations`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-db-migrations/SKILL.md).
- Използвайте миграционни скриптове, запазени в `PersonalManager-Supported-Scripts`.
- Винаги тествайте агрегациите първо през MCP инструмента `explain` или `find`.

---

> 💡 **Съвет**: При работа по конкретна подсистема, активирайте съответния специализиран скил от [Картата на Скиловете](#2-карта-на-скиловете-skills-registry).

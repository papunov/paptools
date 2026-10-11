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
| 🧩 **`pm-data-ingestion-modules`** | Подробно ръководство за 6-те модула в Data-Ingestion (BNB, Market, Car/Currency AI, Stock AI, Daily Automation, GCP Telematics). | [`skills/pm-data-ingestion-modules/SKILL.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-data-ingestion-modules/SKILL.md) |
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
- **Услуга**: `PersonalManager-Data-Ingestion` (Python 3.11+ + `BlockingScheduler`).
- **Модули**:
  - `export_bnb.py`: Ежедневен експорт на валутни курсове от БНБ с автоматичен weekend/holiday fallback към последния работен ден.
  - `market_exporter.py`: Скрейпър за фундаментални показатели на акции и ETF-и от StockAnalysis.com (`StockFundaments`), защитен с Rate Limiter, Chrome User-Agent и филтрация на ETF.
  - `ai_data.py`: AI анализи с Google Gemini – изчисляване на темпо и прогнозен пробег спрямо `AnnualTarget` за автомобили (`UserSettings.Cars`) и 30-дневен трендов анализ на основни валути (`AIBNBRates`).
  - `stock_ai_data.py`: Автоматизирано генериране на подробни AI инвестиционни досиета (`AIStocksInformations`) с runtime кеширане срещу дублирани заявки.
  - `daily_automation.py`: Ежедневни финансови автоматизации: начисляване на лихви по депозити, падежиране на регулярни разходи (с коректна логика за края на месеца), конвертиране на мултивалутни абонаменти, оптимизирано $O(N)$ обобщение на баланси и анализ на горивната ефективност (`FuelConsumptionAnalysis`).
  - `gcp_processor.py`: Парсър за телематични Excel логове от Google Cloud Storage (GCS bucket), синхронизация на километри (`Cars.CurrentKM`) и записи в `CarDrivingStats`.
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
   - **Code-Behind Pattern**: Задължително отделяне на логиката в `.razor.cs` файлове (чист Razor само за markup).
   - **Dark Mode & Семантични CSS Токени**: Всички карти, таблици, панели и форми използват CSS променливи:
     - `var(--card-bg)`: фон на карти, панели и таблици.
     - `var(--text-primary)` / `var(--text-secondary)`: заглавия и четлив текст.
     - `var(--bg-primary)` / `var(--bg-secondary)` / `var(--bg-tertiary)`: системни фонове.
     - `var(--border-color)`: граници и разделители.
     - *Строго забранени са твърдо кодирани светли класове (`bg-white`, `bg-light`, `text-dark`) и тъмни стойности като `#1e293b`.*
   - **Защита срещу безкрайно въртене на спинъра**: Забранено е обвързването на индикатора за зареждане с броя елементи (`IsLoading="Model.Count == 0"`). Винаги се използва булев флаг `isLoading = true` преди извикване на сервиз и `isLoading = false` в `finally` блок.
   - **Задължителни Празни Състояния**: Всеки `RadzenDataGrid` трябва да дефинира ясен локализиран текст `EmptyText="Няма намерени записи"`.
   - **Защита на Данните (Потвърждение при изтриване)**: Всички деструктивни действия (изтриване на коли, обслужвания, ремонти, гориво, лизинг, депозити, кеш, регулярни разходи) изискват потвърждение с `DialogService.Confirm`.
   - **Мобилна Адаптивност & Viewport (Android & iOS)**:
     - *Защита от Layout Viewport разтягане*: `html, body, app, .main, .content` задължително с `overflow-x: hidden !important; max-width: 100vw !important; min-width: 0 !important;`.
     - *Плаващи бутони (FAB)*: Форсиран кръг `border-radius: 50% !important;` и отстояния със Safe Area (`env(safe-area-inset-bottom)`, `env(safe-area-inset-right)`) срещу препокриване от Android жестови ленти.
     - *Таблици (`RadzenDataGrid`)*: Без `overflow: hidden` върху грида; плавен вътрешен touch скрол (`overflow-x: auto !important`); колоните Дата/Категория/Сума оптимизирани до `<= 350px`, за да са видими без скролване на малки екрани (360px–384px).
     - *Компактен хедър*: Скриване на потребителския имейл под 576px с `display: none !important;` в `LoginDisplay`.
     - *Viewport Meta Tag*: `viewport-fit=cover` и `maximum-scale=1.0, user-scalable=no` в `_Host.cshtml`.
   - **Регистрация в Breadcrumbs (`_mapUrl`)**: Всеки нов маршрут (`@page`) **задължително** трябва да има регистриран съответен URL сегмент в речника `_mapUrl` в [`MainLayout.razor`](file:///c:/Users/papun/RiderProjects/PersonalManager/PersonalManager/Web.ManagerV2/Shared/MainLayout.razor) с българско име (напр. `{"documents", "Документи"}`), за да не се показва суровият английски URL път в навигационните хлябни трохи.
2. **Python (`PersonalManager-Data-Ingestion`)**:
   - `BlockingScheduler` за периодични задачи с graceful shutdown (`SIGTERM`/`SIGINT`).
   - Сингълтон `MongoClient` за ефикасно управление на връзките към MongoDB Atlas.
   - Задължителна нормализация на всички времеви маркери в UTC (`ensure_utc()`).
   - Използване на `pymongo` с изрично обработване на `Decimal128` и `ObjectId`.
   - Логиране на резултатите от всяка задача в колекцията `JobStatuses` през централната обвивка `safe_execute`.

---

## 8. Миграции и Поддръжка на Данни

При рефакториране на схеми или преименуване на полета в MongoDB:
- Прегледайте скила [`pm-db-migrations`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-db-migrations/SKILL.md).
- Използвайте миграционни скриптове, запазени в `PersonalManager-Supported-Scripts`.
- Винаги тествайте агрегациите първо през MCP инструмента `explain` или `find`.

---

> 💡 **Съвет**: При работа по конкретна подсистема, активирайте съответния специализиран скил от [Картата на Скиловете](#2-карта-на-скиловете-skills-registry).

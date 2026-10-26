---
name: pm-ai-analytics
description: >-
  Управление на AI анализи за акции (`AIStocksInformations`), AI прогнози за валути (`AIBNBRates`), OCR мапинг правила за касови бележки (`ReceiptExpressions`, `ReceiptMaps`) и мониторинг на фонови задачи (`JobStatuses`) в PersonalManager.
---

# PersonalManager AI Analytics, OCR & Job Monitoring Guide

Този скил описва структурите и правилата за работа с **AI модулите**, **OCR обработката на бележки** и **фоновата автоматизация** в **PersonalManager**. Данните се генерират автоматично от фоновата Python услуга `PersonalManager-Data-Ingestion` и се съхраняват в MongoDB.

---

## 1. AI Анализи за Акции, Валути и Автомобили

### 📈 1. AI Анализи на Акции (`AIStocksInformations`)
Съдържа AI-генерирани фундаментални и технически доклади за даден тикер (акция/ETF):
- `_id`: ObjectId
- `StockId`: ObjectId (връзка към колекция `Stocks`)
- `Ticker`: string (напр. `"AAPL"`, `"VWCE"`)
- `StockExchangeCode`: string (напр. `"NASDAQ"`, `"XETRA"`)
- `UserId`: ObjectId
- `AIInformation`: string (Подробен структуриран профил от Gemini с Google Search Grounding в реално време, съдържащ описание, актуална борсова цена, дивидентна доходност, гласуван дивидент за текущата година, история на изплащанията и актуални тенденции/ръст)
- `LastUpdated`: DateTime

### 💱 2. AI Прогнози за Валутни Курсове (`AIBNBRates`)
Съдържа AI анализи и прогнози за тенденциите на основните валути спрямо BGN/EUR:
- `_id`: ObjectId
- `Currency`: string (напр. `"USD"`, `"GBP"`)
- `AIInformation`: string (Анализ на инфлация, лихвени проценти от централни банки и валутни тенденции)
- `LastUpdated`: DateTime

### 🚗 3. AI Анализи на Автомобили (`UserSettings.Cars`)
Съдържа AI прогнози за годишния пробег, темпото на каране и сервизни съвети:
- `Cars.$.Name`: string (напр. `"Mazda 3"`)
- `Cars.$.AnnualTarget`: int (годишен таргет в км, напр. `15000`)
- `Cars.$.AIInformation`: string (AI оценка на дневно темпо, прогноз за края на 365-дневния цикъл и необходими км/ден)
- `Cars.$.AILastUpdated`: DateTime (UTC)

---

## 2. OCR Обработка и Мапинг на Касови Бележки

### 🧾 1. Регулярни Изрази за Магазини (`ReceiptExpressions`)
Дефинира Regex шаблони за парсване на текста от сканирани касови бележки:
- `_id`: ObjectId
- `UserId`: ObjectId
- `Pattern`: string (напр. `"(?i)KAUFLAND|LIDL|BILLA|FANTASTICO"`)
- `Comment`: string (Описание на търговската верига)

### 🗺️ 2. Мапинг и Правила (`ReceiptMaps` & `Receipts`)
- **`Receipts`**: Съхранява уникалния номер на касовата бележка, дата, сума и статус на обработка (`Processed`, `ReceiptUniqueNumber`, `ReceiptNumber`).
- **`ReceiptMaps`**: Свързва уникалния номер на бележката с определена категория и автоматично я маркира като обработена (`Proceeded`) или игнорирана (`Ignored`).

---

## 3. Мониторинг на Фонови Задачи (`JobStatuses`)

Услугата `PersonalManager-Data-Ingestion` периодично изпълнява 6 планирани задачи и записва статус в `JobStatuses`:
- `_id`: ObjectId
- `JobName`: string — едно от 6-те стандартни имена на задачи:
  - `"BNB Exchange Rates Exporter"` (експорт на БНБ фиксинги)
  - `"Stock Market Exporter"` (фундаменти от StockAnalysis.com)
  - `"AI Data Generator"` (AI анализи на автомобили и валути)
  - `"Stock AI Generator"` (AI инвестиционни досиета за акции)
  - `"Daily Automation"` (депозити, регулярни разходи, салда, разход на гориво)
  - `"GCP Bucket Processor"` (телематични Excel логове от GCS bucket)
- `Status`: string (`"SUCCESS"`, `"FAILED"`, `"RUNNING"`)
- `DurationSeconds`: double/int (време на изпълнение в секунди)
- `Error`: string (текст на грешката, ако `Status` е `"FAILED"`)
- `ExecutedAt`: DateTime (UTC)

---

## 4. Код Примери

### Python (`pymongo`) - Проверка на Статуса на Фоновите Задачи
```python
import pymongo

client = pymongo.MongoClient("mongodb+srv://<username>:<password>@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority")
db = client["PersonalManager"]

# Вземане на последните 5 изпълнени фонови задачи
jobs = db["JobStatuses"].find().sort("ExecutedAt", -1).limit(5)
for j in jobs:
    status_icon = "✅" if j.get("Status") == "SUCCESS" else "❌"
    print(f"{status_icon} [{j.get('ExecutedAt').strftime('%Y-%m-%d %H:%M')}] {j.get('JobName')}: {j.get('Status')} ({j.get('DurationSeconds', 0)}s)")
    if j.get("Error"):
        print(f"   Грешка: {j.get('Error')}")
```

### Python (`pymongo`) - Извличане на AI Анализ за Акция
```python
ai_info = db["AIStocksInformations"].find_one({"Ticker": "AAPL"})
if ai_info:
    print(f"Анализ за {ai_info['Ticker']} (Обновен на {ai_info['LastUpdated']}):")
    print(ai_info["AIInformation"])
```

---

## 5. Заявки през MongoDB Atlas MCP Server (`mongodb-mcp-server`)

### Търсене на грешки във фоновите задачи през MCP `find`:
```json
{
  "connectionId": "preconfigured",
  "database": "PersonalManager",
  "collection": "JobStatuses",
  "filter": { "Status": "FAILED" },
  "sort": { "ExecutedAt": -1 },
  "limit": 5
}
```

### Извличане на последния AI анализ за определена акция през MCP `find`:
```json
{
  "connectionId": "preconfigured",
  "database": "PersonalManager",
  "collection": "AIStocksInformations",
  "filter": { "Ticker": "AAPL" },
  "limit": 1
}
```

---

## 6. AI & Telegram Бот Интеграция (`Web.ManagerV2`)

Интерактивен финансов асистент за бързо въвеждане на разходи и обработка на касови бележки чрез Telegram и Google Gemini AI.

### ⚙️ 1. Настройки в базата данни (`Settings` колекция)
- `Gemini_API_KEY`: API ключ за Google Gemini (взема се от Google AI Studio).
- `Telegram_Bot_Token`: Токен на бота от `@BotFather`.
- `Telegram_Chat_ID`: Лично числово ID на потребителя (от `@userinfobot` или автоматично свързано).
- `Telegram_Webhook_URL`: Базов URL за Webhook (напр. `https://papmgr2.azurewebsites.net`).
- `Telegram_Bot_Enabled`: Булев флаг (`true`/`false`) за активиране на бота.

### 🧠 2. Gemini AI Сървиз (`IGeminiAiService`, `GeminiAiService`)
- **Модел**: `gemini-2.5-flash` (с автоматичен fallback към `gemini-1.5-flash`).
- **Свободен текст**: Парсва неструктурирани фрази на български език (напр. *„Кафе и кроасан 5.40 от Costa“*) към валиден JSON обект (`amount`, `category`, `comment`, `isExpense`).
- **Multimodal Vision**: Анализира директно снимка на касова бележка от Telegram (извлича обект, тотал и категория).
- **Финансови въпроси & RAG контекст**: 
  - Методът `IManagerService.GetFinancialContextSummaryAsync()` извлича актуална справка от MongoDB: текуща заплата/период, общо разходи, оставащ свободен баланс, топ 5 категории, днешни разходи, предходен месец и последни 5 транзакции.
  - Тези реални данни се инжектират динамично в промпта на `AskFinancialQuestionAsync`, позволявайки на Gemini да отговаря веднага с конкретни суми и финансови съвети, без да бави или да отговаря фиктивно.

### 🤖 3. Telegram Бот Архитектура: Webhook (Azure) срещу Polling (Local)
- **Споделена обработка (`ITelegramBotService`, `TelegramBotService`)**:
  - Цялата логика по обработка на съобщения, OCR снимки, Gemini класификация и запис в MongoDB е капсулирана в `TelegramBotService`.
  - Включва `SendSafeMessageAsync` за автоматичен fallback от Markdown към чист текст при грешки от невалидни символи.
- **Работа при заспиване в Azure Free Plan (F1) чрез Webhook**:
  - На безплатен план на Azure сайтът заспива след 20 минути неактивност, а опцията „Always On“ липсва.
  - Чрез **`TelegramWebhookController`** (`/api/telegram/webhook` с атрибут `[AllowAnonymous]`), всяко ново съобщение от телефона кара сървърите на Telegram да изпратят входяща HTTPS POST заявка към Azure.
  - **Тази заявка мигновено събужда заспалия сайт (Cold Start)**, транзакцията се обработва от Gemini и се записва в MongoDB, а потребителят получава обратен отговор в чата без забавяне и без нужда от платен Always On план!
- **Локален дебъг чрез Long-Polling (`TelegramBotHostedService`)**:
  - Когато `Telegram_Webhook_URL` е празен, ботът автоматично изтрива webhook регистрацията в Telegram и превключва на Long-Polling (`GetUpdates`).
  - Това позволява пълен локален дебъг в Rider без публични адреси или тунели.

### 🎛️ 4. Потребителски интерфейс (`Settings.razor` / `/settings`)
- Карта **„AI & Telegram Бот Интеграция“** с маскирани полета за въвеждане и запазване на ключовете.
- Конфигурация за **Webhook URL (Azure / Без заспиване)** с бутони:
  - 🌐 *Активирай Webhook (Azure)* — регистрира URL-а в Telegram и превключва режима.
  - 🔄 *Превключи на Polling (Local)* — изтрива Webhook-а от Telegram и активира локалния polling.
  - ℹ️ *Статус* — извлича информация за текущото състояние и чакащи съобщения от Telegram API.
- Вграден **AI Тестов Симулатор** за изпробване на категоризацията директно от браузъра.

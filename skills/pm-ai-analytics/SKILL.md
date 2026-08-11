---
name: pm-ai-analytics
description: >-
  Управление на AI анализи за акции (`AIStocksInformations`), AI прогнози за валути (`AIBNBRates`), OCR мапинг правила за касови бележки (`ReceiptExpressions`, `ReceiptMaps`) и мониторинг на фонови задачи (`JobStatuses`) в PersonalManager.
---

# PersonalManager AI Analytics, OCR & Job Monitoring Guide

Този скил описва структурите и правилата за работа с **AI модулите**, **OCR обработката на бележки** и **фоновата автоматизация** в **PersonalManager**. Данните се генерират автоматично от фоновата Python услуга `PersonalManager-Data-Ingestion` и се съхраняват в MongoDB.

---

## 1. AI Анализи за Акции и Валути

### 📈 1. AI Анализи на Акции (`AIStocksInformations`)
Съдържа AI-генерирани фундаментални и технически доклади за даден тикер (акция/ETF):
- `_id`: ObjectId
- `StockId`: ObjectId (връзка към колекция `Stocks`)
- `Ticker`: string (напр. `"AAPL"`, `"VWCE"`)
- `StockExchangeCode`: string (напр. `"NASDAQ"`, `"XETRA"`)
- `UserId`: ObjectId
- `AIInformation`: string (Подробен анализ от LLM съдържащ P/E съотношения, фундаментално здраве, технически нива на подкрепа/съпротива и изводи)
- `LastUpdated`: DateTime

### 💱 2. AI Прогнози за Валутни Курсове (`AIBNBRates`)
Съдържа AI анализи и прогнози за тенденциите на основните валути спрямо BGN/EUR:
- `_id`: ObjectId
- `Currency`: string (напр. `"USD"`, `"GBP"`)
- `AIInformation`: string (Анализ на инфлация, лихвени проценти от централни банки и валутни тенденции)
- `LastUpdated`: DateTime

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

Услугата `Data-Ingestion` периодично изпълнява задачи за извличане на пазарни данни, БНБ курсове, генерация на AI анализи и обновяване на разходи. Всяко изпълнение записва статус в `JobStatuses`:
- `_id`: ObjectId
- `JobName`: string (напр. `"BNB Exchange Rates Exporter"`, `"Stock AI Generator"`, `"Daily Automation"`)
- `Status`: string (напр. `"SUCCESS"`, `"FAILED"`, `"RUNNING"`)
- `DurationSeconds`: double/int (време на изпълнение в секунди)
- `Error`: string (текст на грешката, ако `Status` е `"FAILED"`)
- `ExecutedAt`: DateTime

---

## 4. Код Примери

### Python (`pymongo`) - Проверка на Статуса на Фоновите Задачи
```python
import pymongo

client = pymongo.MongoClient("mongodb+srv://papunov:PAPUNOV87papunov87@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority")
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

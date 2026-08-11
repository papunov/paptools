---
name: pm-mongodb-database
description: >-
  Пълно ръководство за MongoDB базата данни в PersonalManager: колекции, схеми, Atlas конекции, C# & PyMongo CRUD операции, BSON ObjectId и Decimal128 мапинг.
  Използвайте този скил при заявки към базата данни, създаване на нови колекции, индекси или оптимизация на MongoDB.
---

# PersonalManager MongoDB Database Guide

## 1. Списък на Колекциите в MongoDB

Базата данни на PersonalManager (`PersonalManager`) съдържа следните основни колекции:

### 💰 Финанси и Бюджети
- **`Transactions`**: Финансови транзакции (приходи и разходи).
- **`Categories`**: Категории на разходите/приходите.
- **`Salaries`**: Месечни бюджетни периоди (заплати).
- **`SumSalaries`**: Сумарни изчисления и спестявания по заплати.
- **`PlannedExpenses`**: Планирани еднократни разходи.
- **`RecurringExpenses`**: Автоматично повтарящи се разходи.
- **`Receipts`**, **`ReceiptMaps`**, **`ReceiptExpressions`**: Сканирани касови бележки и мапинг правила.

### 📈 Инвестиции и Пазари
- **`Stocks`**: Позиции в акции и ETF-и.
- **`Funds`**: Инвестиционни фондове.
- **`InvestStockOrders`**: Покупки/продажби на акции.
- **`FundOrders`**: Поръчки за фондове.
- **`Dividends`**: Получени дивиденти.
- **`Deposits`**, **`DepositPayments`**: Спестовни депозити.
- **`BrokerFees`**, **`Cashbacks`**, **`CashInterests`**: Брокерски такси, кешбек и лихви.
- **`BNBRates`**, **`StockPrices`**, **`StockFundaments`**: Валутни курсове и пазарни фундаментални данни.

### 🚗 Автомобили
- **`Cars`**: Профили на автомобили и пробег.
- **`CarMaintenances`**: Сервизни обслужвания.
- **`MaintenanceActions`**: Видове сервизни дейности.
- **`CarTransactionsProceeded`**: Свързани авто разходи.

### ⚙️ Системни и Логване
- **`JobStatuses`**: История на изпълнение на фоновите задачи от `Data-Ingestion`.
- **`Logs`**: Системни логове за грешки и събития.

---

## 2. Формати за Свързване (Local & Atlas Cloud)

- **Локален MongoDB**:
  ```text
  mongodb://127.0.0.1:27017/PersonalManager
  ```
- **MongoDB Atlas (Cloud)**:
  ```text
  mongodb+srv://<user>:<password>@<cluster>.mongodb.net/PersonalManager?retryWrites=true&w=majority
  ```

---

## 3. Обработка на Типове Данни (`ObjectId` & `Decimal128`)

### В C# (.NET / `MongoDB.Driver`)
```csharp
using MongoDB.Bson;
using MongoDB.Driver;

// Филтриране по ObjectId
var filter = Builders<Transaction>.Filter.Eq(x => x.UserId, new ObjectId("60d5ec49f1d2c20015f8a123"));

// Вземане на колекция
var collection = db.GetCollection<Transaction>("Transactions");
var results = await collection.Find(filter).ToListAsync();
```

### В Python (`pymongo`)
```python
from bson import ObjectId
from bson.decimal128 import Decimal128
from decimal import Decimal

# Вземане на колекция
collection = db["Dividends"]

# Търсене с ObjectId
doc = collection.find_one({"_id": ObjectId("60d5ec49f1d2c20015f8a123")})

# Преобразуване на Decimal128 към Python Decimal за прецизни изчисления
sum_val = doc.get("SumInCurrency")
dec_sum = sum_val.to_decimal() if isinstance(sum_val, Decimal128) else Decimal(str(sum_val))
```

---

## 4. Задвижване на Индекси за Бързина (Recommended Indexes)

За бързо филтриране се препоръчва наличието на следните индекси:

```javascript
// В MongoDB Shell или Atlas UI:
db.Transactions.createIndex({ "UserId": 1, "AddedOn": -1 });
db.Transactions.createIndex({ "SalaryId": 1 });
db.Dividends.createIndex({ "BrokerName": 1, "TaxAddedToSum": 1 });
db.StockPrices.createIndex({ "Ticker": 1, "Date": -1 });
```

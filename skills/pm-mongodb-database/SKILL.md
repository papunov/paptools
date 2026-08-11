---
name: pm-mongodb-database
description: >-
  Пълно ръководство и пълна схема на production MongoDB Atlas базата данни (`PersonalManager`): 51 колекции, полета, BSON типове, Atlas конекции, C# & PyMongo CRUD операции и индекси.
---

# PersonalManager MongoDB Production Database Guide

Базата данни на **PersonalManager** се гоства в **MongoDB Atlas** (Cluster: `papcl01-wqwkg.azure.mongodb.net`, Database: `PersonalManager`) и съдържа **51 реални колекции**, разпределени в 5 основни функционални домейна.

---

## 1. Пълен Списък на Колекциите по Домейни

### 💰 1. Финанси, Бюджети, Превалутиране и Спестявания (16 колекции)
- **`Transactions`** (12,118 документа): Основни финансови транзакции (`UserId`, `SalaryId`, `CategoryId`, `CategoryName`, `Sum`, `Comment`, `Expense`, `AddedToSavings`, `Marked`, `AddedOn`).
- **`Salaries`** (108 документа): Месечни бюджетни периоди (`UserId`, `Name`, `Month`, `Year`, `Sum`, `AddedOn`, `Locked`).
- **`SumSalaries`** (106 документа): Агрегирани баланси по заплати (`UserId`, `SalaryId`, `Name`, `Month`, `Year`, `SalarySum`, `IncomeSum`, `ExpenseSum`, `BalanceSum`, `SumCategories`).
- **`Categories`** (49 документа): Категории за разходи и приходи (`UserId`, `Name`).
- **`PlannedExpenses`** (10 документа): Планирани еднократни разходи (`UserId`, `CategoryId`, `CategoryName`, `Sum`, `Comment`, `SalaryName`, `SalaryMonth`, `SalaryYear`, `Completed`, `DateOfCompletion`).
- **`RecurringExpenses`** (9 документа): Периодични/повтарящи се разходи (`UserId`, `CategoryId`, `CategoryName`, `Sum`, `Comment`, `Active`, `AutomaticPayment`, `DayOfMonth`, `EveryXMonths`, `LastPaymentDate`).
- **`Subscriptions`** (13 документа): Абонаментни услуги (`UserId`, `Name`, `Currency`, `Price`, `Frequency`, `StartDate`, `IsActive`, `Payments`).
- **`ConvertedTransactions`** (11,091 документа): Кeширани превалутирани транзакции в BGN (`UserId`, `TransactionId`, `SumInBGN`).
- **`ConvertedSalaries`** (98 документа): Кeширани превалутирани заплати в BGN (`UserId`, `SalaryId`, `SumInBGN`).
- **`Savings`** (15 документа): Спестовни сметки/джабове (`UserId`, `Name`, `TotalSum`, `UsedSum`, `LastUpdate`).
- **`SavingTransactions`** (982 документа): Движения по спестовните сметки (`SavingId`, `SavingName`, `Sum`, `OnDate`, `IsAdded`).
- **`FrequentComments`** (107 документа): Статистика за често използвани коментари (`UserId`, `IsExpense`, `CategoryName`, `Comment`, `Count`, `Hide`).
- **`TransactionAutoCompletes`** / **`TransactionsAutoCompletes`** (703 / 742 документа): История за бързо дописване на транзакции.
- **`Notes`** (1 документ): Потребителски бележки (`UserId`, `Text`, `UpdatedOn`).
- **`Reminders`** (381 документа): Напомняния за плащания (`UserId`, `RelationId`, `Comment`, `Sum`, `Type`, `Date`, `Archived`, `ArchivedOn`).
- **`Calculations`** (1 документ): Съхранени калкулации (`UserId`, `Name`, `Entries`).

### 📈 2. Инвестиции, Брокери, Акции, Фондове, Дивиденти, БНБ и AI Анализи (17 колекции)
- **`Stocks`** (26 документа): Позиции в акции и ETF-и (`UserId`, `BrokerId`, `BrokerName`, `Ticker`, `Is_ETF`, `Name`, `Country`, `StockExchangeCode`, `Shares`, `AverageSharePrice`, `CurrentSharePrice`, `CurrentSharePriceInEUR`, `SumInCurrency`, `DividendsInCurrency`, `DividendTaxesInCurrency`, `Currency`, `SumInEUR`, `DividendsInEUR`, `DividendTaxesInEUR`, `FeeInCurrency`, `FeeInEUR`, `LastUpdated`).
- **`InvestStockOrders`** (505 документа): Покупки и продажби на акции (`UserId`, `BrokerId`, `BrokerName`, `Ticker`, `Name`, `Is_ETF`, `StockExchange`, `OrderID`, `SellOrder`, `Shares`, `SharePrice`, `SumInCurrency`, `Currency`, `CurrencyRateToEUR`, `SumInEUR`, `FeeInCurrency`, `FeeInEUR`, `Date`).
- **`StockPrices`** (103 документа): Исторически цени на акции (`Ticker`, `Exchange`, `Date`, `OpenPrice`, `ClosePrice`, `HighPrice`, `LowPrice`, `Volume`).
- **`StockFundaments`** (17 документа): Фундаментални показатели на компании (`Ticker`, `Beta`, `Buyback_Yield_Percentage`, `Current_Ratio`, `Debt_EBITDA`, `Debt_Equity`, `Dividend_Yield_Percentage`, `EPS`, `Earnings_Yield_Percentage`, `LastUpdated`, `PB`, `PE`, `PS`, `P_FCF`, `Payout_Ratio_Percentage`, `ROA_Percentage`, `ROE_Percentage`, `ROIC_Percentage`, `StockExchangeCode`).
- **`InvestmentBrokers`** (3 документа): Брокерски профили (Trading 212, Interactive Brokers и др.) (`UserId`, `Name`, `FeeInEUR`, `CachbackInEUR`, `InterestsInEUR`, `InvestedAmount`, `LastDataDate`).
- **`BrokerFees`** (48 документа): Такси платени към брокери (`UserId`, `BrokerId`, `BrokerName`, `OrderID`, `Type`, `Notes`, `SumInCurrency`, `Currency`, `SumInEUR`, `Date`).
- **`Cashbacks`** (726 документа): Получени кешбеци (`UserId`, `BrokerId`, `BrokerName`, `OrderID`, `SumInCurrency`, `Currency`, `SumInEUR`, `Date`).
- **`CashInterests`** (1,605 документа): Начислена лихва върху свободен капитал при брокери (`UserId`, `BrokerId`, `BrokerName`, `OrderID`, `SumInCurrency`, `Currency`, `SumInEUR`, `Date`).
- **`CashCurrencies`** (0 документа): Валутни наличности при брокери.
- **`Dividends`** (159 документа): Получени дивиденти (`UserId`, `BrokerId`, `BrokerName`, `Ticker`, `OrderID`, `SumInCurrency`, `TaxInCurrency`, `Currency`, `CurrencyRateToEUR`, `SumInEUR`, `TaxInEUR`, `Date`).
- **`Funds`** (1 документ) & **`FundOrders`** (31 документа): Взаимни фондове и поръчки за тях (`UserId`, `FundId`, `FundName`, `SellOrder`, `Shares`, `SharePrice`, `SumInCurrency`, `SumInEUR`, `FeeInCurrency`, `FeeInEUR`, `Date`).
- **`Deposits`** (4 документа) & **`DepositPayments`** (49 документа): Спестовни депозити и плащания по тях (`UserId`, `Name`, `DepositSum`, `Interests`, `AccomulatedInterest`, `PaidInterest`, `StartDate`).
- **`BNBRates`** (832 документа): Валутни курсове от БНБ (`Currency`, `Rate`, `Day`, `Month`, `Year`, `Date`).
- **`AIStocksInformations`** (26 документа): AI-генерирани фундаментални и технически анализи на акции (`StockId`, `Ticker`, `StockExchangeCode`, `AIInformation`, `LastUpdated`, `UserId`).
- **`AIBNBRates`** (3 документа): AI прогнози и анализи за валутни курсове (`Currency`, `AIInformation`, `LastUpdated`).

### 🚗 3. Автомобили, Поддръжка, Телематика, Горива и Лизинг (8 колекции)
- **`Cars`** (3 документа): Профили на автомобили (`UserId`, `Name`, `Year`, `Plate_Number`, `Brand`, `Model`, `InitialKM`, `CurrentKM`, `IsActive`).
- **`CarServices`** (6 документа): Планови сервизни обслужвания (`CarId`, `UserId`, `Date`, `OdometerKM`, `Name`, `Description`, `PartsCost`, `LaborCost`).
- **`CarRepairs`** (22 документа): Извънредни ремонти (`CarId`, `UserId`, `Date`, `OdometerKM`, `Name`, `Description`, `PartsCost`, `LaborCost`).
- **`CarDrivingStats`** (736 документа): Телематични логове от пътувания (`CarId`, `UserId`, `Date`, `IgnitionTime`, `EndTime`, `TimeMinutes`, `DistanceKm`, `CarName`, `OriginalFileName`).
- **`FuelRecords`** (66 документа): Зареждания на гориво (`CarId`, `UserId`, `Date`, `GasStation`, `Liters`, `PricePerLiter`, `TotalDiscount`, `OdometerKM`, `IsOdometerAccurate`, `CarDrivingStatId`).
- **`FuelConsumptionAnalysis`** (18 документа): Анализ на средния разход на гориво (`CarId`, `UserId`, `StartDate`, `EndDate`, `StartOdometer`, `EndOdometer`, `DistanceKm`, `Liters`, `AvgConsumption`, `TripsCount`).
- **`LeaseContracts`** (1 документ): Договори за лизинг (`CarId`, `UserId`, `ContractNumber`, `ProviderName`, `StartDate`, `EndDate`, `TotalLeaseAmount`, `ResidualValuePercentage`, `Entries`).
- **`UserSettings`** (1 документ): Настройки за автомобили на потребителя (`UserId`, `Cars`).

### 🧾 4. OCR & Сканирани Касови Бележки (3 колекции)
- **`Receipts`** (69 документа): Сканирани бележки (`UserId`, `TransactionId`, `Processed`, `ReceiptUniqueNumber`, `ReceiptNumber`, `Date`, `Sum`).
- **`ReceiptMaps`** (24 документа): Мапинг между бележка и категория (`UserId`, `ReceiptUniqueNumber`, `Comment`, `Proceeded`, `Ignored`).
- **`ReceiptExpressions`** (2 документа): Шаблони (Regex) за разпознаване на бележки (`UserId`, `Pattern`, `Comment`).

### ⚙️ 5. Системни, Логове, Фонови Задачи и Потребители (5 колекции)
- **`Users`** (2 документа): Потребителски акаунти (`Email`, `Password`, `FirstName`, `LastName`, `BadPasswordAttempts`, `Locked`, `RegisteredOn`, `LastLogon`).
- **`UserMappings`** (2 документа): Идентификационни мапинги (`email`).
- **`Settings`** (12 документа): Системни настройки (`UserId`, `Name`, `Value`).
- **`JobStatuses`** (551 документа): Логове за изпълнение на фонови задачи от `Data-Ingestion` (`JobName`, `Status`, `DurationSeconds`, `Error`, `ExecutedAt`).
- **`Logs`** (127 документа): Системни логове за грешки и събития (`Type`, `Name`, `Value`, `Date`).

---

## 2. Формати за Свързване (Local & Atlas Cloud)

- **MongoDB Atlas (Production Cloud)**:
  ```text
  mongodb+srv://papunov:PAPUNOV87papunov87@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority
  ```
- **Локален MongoDB**:
  ```text
  mongodb://127.0.0.1:27017/PersonalManager
  ```

---

## 3. Работа с Типове Данни (`ObjectId`, `Decimal128`, `DateTime`)

### В C# (.NET / `MongoDB.Driver`)
```csharp
using MongoDB.Bson;
using MongoDB.Driver;

var client = new MongoClient("mongodb+srv://papunov:PAPUNOV87papunov87@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority");
var db = client.GetDatabase("PersonalManager");

// Заявка към Transactions
var transactionsCol = db.GetCollection<BsonDocument>("Transactions");
var filter = Builders<BsonDocument>.Filter.Eq("UserId", new ObjectId("60d5ec49f1d2c20015f8a123"));
var recentTransactions = await transactionsCol.Find(filter).SortByDescending(x => x["AddedOn"]).Limit(10).ToListAsync();
```

### В Python (`pymongo`)
```python
import pymongo
from bson import ObjectId
from bson.decimal128 import Decimal128

uri = "mongodb+srv://papunov:PAPUNOV87papunov87@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority"
client = pymongo.MongoClient(uri)
db = client["PersonalManager"]

# Извличане на транзакции с преобразуване на сумите
for tx in db["Transactions"].find({"Expense": True}).limit(5):
    val = tx.get("Sum")
    amount = val.to_decimal() if isinstance(val, Decimal128) else float(val)
    print(f"{tx.get('AddedOn')}: {tx.get('CategoryName')} -> {amount} BGN ({tx.get('Comment')})")
```

---

## 4. Основни Индекси в Базата Данни (Production Indexes)

За гарантиране на висока производителност в Atlas са изградени следните индекси:

```javascript
// Transactions
db.Transactions.createIndex({ "UserId": 1, "Expense": 1, "SalaryId": -1 });
db.Transactions.createIndex({ "UserId": 1, "Comment": 1 });
db.Transactions.createIndex({ "UserId": 1, "CategoryId": 1 });

// Investments & Stocks
db.InvestStockOrders.createIndex({ "UserId": 1, "Ticker": 1, "BrokerId": 1 });
db.Stocks.createIndex({ "UserId": 1, "Ticker": 1, "BrokerId": 1 });
db.StockPrices.createIndex({ "Ticker": 1, "Exchange": 1, "Date": -1 });
db.StockFundaments.createIndex({ "Ticker": 1, "StockExchangeCode": 1 });

// Salaries & Recurring
db.Salaries.createIndex({ "UserId": 1, "Month": 1, "Year": 1, "_id": -1 });
db.RecurringExpenses.createIndex({ "AutomaticPayment": 1, "DayOfMonth": 1 });

// Telematics & Logs
db.CarDrivingStats.createIndex({ "UserId": 1, "CarId": 1, "Date": -1 });
db.Logs.createIndex({ "Date": -1 });
```

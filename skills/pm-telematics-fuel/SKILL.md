---
name: pm-telematics-fuel
description: >-
  Управление на телематични данни за шофиране (`CarDrivingStats`), разход на гориво (`FuelRecords`), автоматизиран анализ на ефективността (`FuelConsumptionAnalysis`) и лизингови договори (`LeaseContracts`) в PersonalManager.
---

# PersonalManager Vehicle Telematics & Fuel Analysis Guide

Скилът за телематика и горива управлява данните за шофиране, зареждания, разход на гориво и лизинг в **PersonalManager**. Данните се съхраняват в MongoDB базата данни в колекциите `CarDrivingStats`, `FuelRecords`, `FuelConsumptionAnalysis`, `LeaseContracts` и `Cars`.

---

## 1. Колекции и Модели

### 🚗 1. Профили на Автомобили (`Cars`)
Съхранява основна информация за автомобилите и техния текущ километричен пробег:
- `_id`: ObjectId
- `UserId`: ObjectId
- `Name`: string (напр. `"Audi A4"`)
- `Brand`: string (напр. `"Audi"`)
- `Model`: string (напр. `"A4 2.0 TDI"`)
- `Plate_Number`: string (напр. `"CB1234AB"`)
- `Year`: int
- `InitialKM`: int (начален километраж при въвеждане)
- `CurrentKM`: int (актуален километраж)
- `IsActive`: bool

### 📡 2. Телематика и Пътувания (`CarDrivingStats`)
Документира телематични трипове, качени от GPS/OBD логери или мобилно приложение:
- `_id`: ObjectId
- `CarId`: ObjectId
- `UserId`: ObjectId
- `CarName`: string
- `Date`: DateTime (дата на трипа)
- `IgnitionTime`: DateTime (стартиране на двигателя)
- `EndTime`: DateTime (изключване на двигателя)
- `TimeMinutes`: double/int (продължителност в минути)
- `DistanceKm`: double (изминато разстояние в км)
- `OriginalFileName`: string (име на оригиналния телематичен файл)
- `InsertedAt`: DateTime

### ⛽ 3. Зареждания на Гориво (`FuelRecords`)
Записи за зареждане на бензиностанция:
- `_id`: ObjectId
- `CarId`: ObjectId
- `UserId`: ObjectId
- `Date`: DateTime (дата и час на зареждането)
- `GasStation`: string (напр. `"OMV"`, `"Shell"`, `"EKO"`)
- `Liters`: double/decimal (количество гориво в литри)
- `PricePerLiter`: double/decimal (цена за литър в BGN)
- `TotalDiscount`: double/decimal (приложена отстъпка)
- `OdometerKM`: int (километраж при зареждането)
- `IsOdometerAccurate`: bool
- `CarDrivingStatId`: ObjectId (по избор: връзка към конкретно телематично пътуване)

### 📊 4. Автоматизиран Анализ на Разхода (`FuelConsumptionAnalysis`)
Изчислени агрегирани показатели между две зареждания "до горе":
- `_id`: ObjectId
- `CarId`: ObjectId
- `UserId`: ObjectId
- `StartFuelRecordId`: ObjectId
- `EndFuelRecordId`: ObjectId
- `StartDate`: DateTime
- `EndDate`: DateTime
- `StartOdometer`: int
- `EndOdometer`: int
- `DistanceKm`: double (изминати км = `EndOdometer - StartOdometer`)
- `Liters`: double (общо сипани литри за периода)
- `AvgConsumption`: double (среден разход в L/100km = `(Liters / DistanceKm) * 100`)
- `TripsCount`: int (брой телематични пътувания в периода)
- `ProcessedAt`: DateTime

### 📄 5. Лизингови Договори (`LeaseContracts`)
Договори за оперативен или финансов лизинг:
- `_id`: ObjectId
- `CarId`: ObjectId
- `UserId`: ObjectId
- `ContractNumber`: string
- `ProviderName`: string (напр. `"OTP Leasing"`, `"Unicredit Leasing"`)
- `StartDate`: DateTime
- `EndDate`: DateTime
- `TotalLeaseAmount`: decimal
- `ResidualValuePercentage`: decimal
- `Entries`: Array (погасителен план с вноски)

---

## 2. Код Примери

### Python (`pymongo`) - Изчисление на Среден Разход на Гориво
```python
import pymongo

client = pymongo.MongoClient("mongodb+srv://papunov:PAPUNOV87papunov87@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority")
db = client["PersonalManager"]

# Вземане на последните 5 анализа за разход
analyses = db["FuelConsumptionAnalysis"].find().sort("EndDate", -1).limit(5)
for a in analyses:
    print(f"Период: {a['StartDate'].strftime('%Y-%m-%d')} - {a['EndDate'].strftime('%Y-%m-%d')}")
    print(f"  Изминати км: {a['DistanceKm']} km | Изразходвани литри: {a['Liters']} L")
    print(f"  Среден разход: {a['AvgConsumption']:.2f} L/100km ({a.get('TripsCount', 0)} трипа)")
```

### C# (.NET) - Свързване на Телематично Пътуване със Зареждане
```csharp
var filter = Builders<BsonDocument>.Filter.And(
    Builders<BsonDocument>.Filter.Eq("CarId", carId),
    Builders<BsonDocument>.Filter.Gte("Date", startDate),
    Builders<BsonDocument>.Filter.Lte("Date", endDate)
);

var drivingStats = await db.GetCollection<BsonDocument>("CarDrivingStats")
    .Find(filter)
    .ToListAsync();

double totalKm = drivingStats.Sum(x => x["DistanceKm"].AsDouble);
int totalTrips = drivingStats.Count;
```

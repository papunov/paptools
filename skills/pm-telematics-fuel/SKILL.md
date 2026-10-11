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
- `IsFullTank`: bool (дали е зареждане "до горе", по подразбиране `true`)
- `TripDistanceKM`: int? (изминато разстояние в км спрямо предходно зареждане)
- `ConsumptionLitersPer100Km`: decimal? (изчислен реален среден разход л/100км по метода "пълен-до-пълен")
- `CostPerKM`: decimal? (себестойност на горивото в EUR/км за интервала)
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

### 💰 6. Пълна Себестойност и TCO Анализ (`CarTcoReport`)
Модел за изчисляване на пълната цена на притежание (Total Cost of Ownership):
- `CarId`, `CarName`, `PlateNumber`, `Brand`, `Model`
- `InitialKM`, `CurrentKM`, `TotalTrackedKM`
- `FuelCostEUR`, `MaintenanceCostEUR`, `RepairsCostEUR`, `DocumentsCostEUR`, `LeaseCostEUR`, `TotalCostEUR`
- `CostPerKM`: обща себестойност в EUR/км = `TotalCostEUR / TotalTrackedKM`
- `FuelCostPerKM`, `MaintenanceCostPerKM`, `FixedCostPerKM` (документи + лизинг)
- `AverageConsumptionL100Km`: среден реален разход на гориво по всички валидни пълни зареждания

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

client = pymongo.MongoClient("mongodb+srv://<username>:<password>@papcl01-wqwkg.azure.mongodb.net/PersonalManager?retryWrites=true&w=majority")
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

---

---

## 4. UI Компоненти & Диалози (`Web.ManagerV2/Components/Cars`)

### ⛽ 1. История на зарежданията (`FuelRecords.razor`):
- Таблица за зареждания на гориво с филтрация по автомобил, километраж, колони за измината дистанция (`км`), реален разход (`л/100км`) и себестойност (`€/км`).
- **Full-to-Full калкулация**: Хронологично изчисляване на реалния разход между зареждания до горе с акумулиране на междинни частични зареждания и синхронизиране на `CurrentKM` на автомобила.
- **KPI карти**: Обобщение на среден разход (л/100км), заредени литри, общи разходи и брой зареждания.
- **Бутон "Преизчисли Разхода"**: Преизчислява показателите за всички или за избрания автомобил.
- **Диалози**: `AddFuelDialog.razor` за въвеждане на ново или редактиране на зареждане, включва превключвател за пълен резервоар (`IsFullTank`).
- **Защита при изтриване**: `DeleteFuelRecord` задължително изисква потвърждение чрез:
  ```csharp
  var confirm = await DialogService.Confirm(
      "Сигурни ли сте, че искате да изтриете това зареждане?", 
      "Потвърждение за изтриване", 
      new ConfirmOptions { OkButtonText = "Да, изтрий", CancelButtonText = "Отказ" });
  if (confirm != true) return;
  ```
- **Тъмна тема**: Заглавията ползват `var(--text-primary)`, а картите – `var(--card-bg)`.

### 📊 2. Автомобилни Статистики & TCO Себестойност (`Statistics.razor` / `/cars/stats`):
- **Таб 1: Пълна Себестойност (TCO & €/км)**:
  - Обобщени KPI карти за целия автопарк: Общ регистриран пробег, Обща инвестиция (€), Средна себестойност (€/км), Брой автомобили.
  - Детайлни карти по автомобил с разбивка: Гориво, Сервиз, Ремонти, Документи & Такси, Лизинг.
  - Себестойност на километър (€/км) и среден реален разход (л/100км).
  - Интерактивен процентен прогрес-бар с цветова индикация за структурата на разходите.
- **Таб 2: Телематика & Пътувания**:
  - Регистър на телематични записи и графики за шофирането.

### 📄 3. Лизингови договори & вноски (`CarLeasing.razor`):
- Управление на лизингови договори, остатъчна стойност и погасителни вноски.
- **Диалози**: `AddLeaseContractDialog.razor` и `AddLeaseEntryDialog.razor`.
- **Защита при изтриване**:
  - `DeleteContract`: Потвърждение преди изтриване на целия договор и вноските към него.
  - `DeleteEntry`: Потвърждение преди изтриване на конкретна вноска.
- **Тематична съвместимост**: Лизинговите карти и графики са съобразени с тъмната цветова палитра.


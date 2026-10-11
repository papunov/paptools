---
name: pm-car-maintenance
description: >-
  Управление на автомобили, поддръжка, сервизни действия и разходи за автомобили в PersonalManager.
---

# PersonalManager Car Maintenance

## Модели за Автомобили (`DataModels.CarsModels`)

Модулът за автомобили проследява автомобилите на потребителя, техния пробег (километраж) и извършените сервизни обслужвания.

### 1. Автомобил (`Car.cs`)
```csharp
public class Car
{
    public ObjectId Id { get; set; }
    public ObjectId UserId { get; set; }
    public string Brand { get; set; }
    public string Model { get; set; }
    public string RegistrationPlate { get; set; }
    public int CurrentKilometers { get; set; }
}
```

### 2. Обслужване и Сервиз (`CarServiceRecord.cs`, `CarRepairRecord.cs`, `MaintenanceAction.cs`)
- **`CarServiceRecord`**: Документира редовно сервизно обслужване (смяна на масло, филтри, спирачки, ремъци), разходи за части, труд, дата и километраж (`OdometerKM`).
- **`CarRepairRecord`**: Документира извънредни ремонти на автомобила.
- **`CarTransactionsProceeded`**: Свързва разходите за автомобила с общата система за транзакции на PersonalManager.

### 3. Документи, Полици & Валидност (`CarDocument.cs`)
- **`CarDocument`**: Следи Гражданска отговорност (`CivilLiability`), Автокаско (`Casco`), ГТП (`Inspection`), Винетка (`Vignette`), Данък МПС (`Tax`) и други документи.
- **Полета**: `ExpiryDate`, `HasInstallments`, `NextInstallmentDueDate`, `CurrentInstallment`, `TotalInstallments`, `InsurerOrIssuer`, `PolicyNumber`, `CostEUR`, `EcoCategory`, `Notes`.
- **Статуси**: `Valid` (>30 дни), `ExpiringSoon` (8-30 дни), `Critical` (0-7 дни), `Expired` (<0 дни).

### 4. Сервизни Интервали & Превантивна Поддръжка (`CarServiceInterval.cs`)
- **`CarServiceInterval`**: Превантивно проследяване на консумативи и износване по километри и период от време.
- **Полета**: `UserId`, `CarId`, `Title`, `Category`, `IntervalKM`, `IntervalMonths`, `LastServiceKM`, `LastServiceDate`, `RecommendedSpec`, `Notes`, `IsActive`.
- **Категории (`ServiceIntervalCategory`)**: `OilAndFilters`, `AirAndCabin`, `FuelFilter`, `Brakes`, `Transmission`, `BeltsAndPumps`, `SparkPlugs`, `Fluids`, `Other`.
- **Здравен статус (`IntervalHealthStatus`)**: `Good` (изрядно), `DueSoon` (до 2500 км или 30 дни), `Urgent` (до 1000 км или 14 дни), `Overdue` (превишен пробег или срок).
- **Предиктивна прогноза**: Калкулира оставащи дни и прогнозна дата на обслужване на база среден дневен пробег $\text{EstimatedDays} = \text{RemainingKM} / \text{AvgDailyKM}$.

---

## UI Компоненти & Диалози (`Web.ManagerV2/Components/Cars`)

1. **`Cars.razor`**:
   - Главен екран за преглед на автомобилите, техния пробег и бързи връзки към сервиз, гориво и лизинг.
   - **Следене на документи (Watchdog)**: Колона и детайлен изглед с цветни баджове за изтичащи/критични полици и документи.
   - **Сервизен План & Консумативи**: Колона и детайлен изглед с баджове за състоянието на интервалите (спешни, наближаващи, изрядни).
   - **Защита при изтриване**: `DeleteCar` изисква потвърждение чрез `DialogService.Confirm("Сигурни ли сте, че искате да изтриете този автомобил и цялата му история?", "Потвърждение за изтриване", ...)` преди изтриване.
   - **Диалог за добавяне/редакция**: `AddCarDialog.razor` с поддръжка на тъмна тема (`var(--card-bg)`, `var(--border-color)`).

2. **`CarDocuments.razor` (`/cars/documents`)**:
   - Специализиран екран за управление и мониторинг на всички автомобилни документи, застраховки и срокове.
   - **KPI карти**: Общо документи, Валидни, Изтичащи скоро (8-30 дни), Спешни/Изтекли.
   - **Алерт банер**: Ярко червено предупреждение при наличие на изтекли или спешни полици/ГТП.
   - **Филтри**: По кола, тип документ и статус на валидност.
   - **Диалог за добавяне/редакция**: `AddCarDocumentDialog.razor` с поддръжка на разсрочени вноски и еко категории.

3. **`CarMaintenance.razor` (`/cars/maintenance`)**:
   - **Таб 1: Сервизен План & Прогноза (`pending_actions`)**: Картов изглед с прогрес барове за износване, оставащ пробег/дни, прогнозна дата на обслужване и бутон за еднокликово отбелязване на извършено обслужване (`CompleteIntervalDialog.razor`).
   - **Таб 2: История на обслужвания (`build_circle`)**: Пълен регистър на извършени периодични обслужвания.
   - **Таб 3: Ремонти (`car_crash`)**: Регистър на извънредни ремонти и аварии.
   - **Алерт банер**: Предупреждение при превишени или спешни интервали.
   - **Диалози**:
     - `AddCarServiceIntervalDialog.razor`: Създаване/редакция на интервали с готови бързи шаблони (масло, филтри, спирачки, ангренаж, антифриз).
     - `CompleteIntervalDialog.razor`: Бързо маркиране като извършено, автоматично нулиране на интервала и запис в `CarServices`.
     - `AddMaintenanceDialog.razor`: Добавяне/редакция на сервизни записи и ремонти.



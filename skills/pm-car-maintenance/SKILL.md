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

---

## UI Компоненти & Диалози (`Web.ManagerV2/Components/Cars`)

1. **`Cars.razor`**:
   - Главен екран за преглед на автомобилите, техния пробег и бързи връзки към сервиз, гориво и лизинг.
   - **Защита при изтриване**: `DeleteCar` изисква потвърждение чрез `DialogService.Confirm("Сигурни ли сте, че искате да изтриете този автомобил и цялата му история?", "Потвърждение за изтриване", ...)` преди изтриване.
   - **Диалог за добавяне/редакция**: `AddCarDialog.razor` с поддръжка на тъмна тема (`var(--card-bg)`, `var(--border-color)`).

2. **`CarMaintenance.razor`**:
   - Табличен изглед за сервизни обслужвания и извънредни ремонти.
   - **Защита при изтриване**: `DeleteService` и `DeleteRepair` изискват потвърждение през `DialogService.Confirm` преди перманентно премахване на записите.
   - **Диалог за добавяне/редакция**: `AddMaintenanceDialog.razor`.
   - **Тематична съвместимост**: Всички заглавия ползват `var(--text-primary)`, а картите използват `var(--card-bg)`.


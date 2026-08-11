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

### 2. Обслужване и Сервиз (`CarMaintenance.cs`, `MaintenanceAction.cs`)
- **`CarMaintenance`**: Документира извършено сервизно обслужване (смяна на масло, филтри, спирачки, гражданска отговорност, преглед и др.), цена, дата и километраж при обслужването.
- **`MaintenanceAction`**: Номенклатура на сервизните дейности (видове ремонти или поддръжка).
- **`CarTransactionsProceeded`**: Свързва разходите за автомобила с общата система за транзакции на PersonalManager.

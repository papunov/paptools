---
name: pm-architecture
description: >-
  Изисквания и архитектура на проекта PersonalManager (.NET Blazor Server, Web API, MongoDB, SysFunctions).
  Използвайте този скил при разработване, рефакториране или разширяване на архитектурни компоненти в PersonalManager.
---

# PersonalManager Architecture Guide

## Общ преглед на архитектурата

Проектът **PersonalManager** е система за управление на лични финанси, инвестиции, автомобилни разходи и заплати, изградена върху .NET с Blazor Server и MongoDB.

```text
PersonalManager/
├── DataModels/             # MongoDB Bson Data Models & Enums
├── SysFunctions/           # Валидатори, BNB курсове, конвертори, изчисления
├── Web.API/                # REST API контролери и хранилища
├── Web.ManagerV2/          # Blazor Server UI (Pages, Components, Shared)
└── DailyCronJob/           # Автоматизирани фонови задачи за периодични разходи
```

## Технологичен стек и зависимости

- **Framework**: .NET (Blazor Server)
- **База данни**: MongoDB (`MongoDB.Bson`, `ObjectId`)
- **UI фреймуърк**: Bootstrap + Open-Iconic графични икони
- **Мапинг**: AutoMapper за трансформиране между DataModels и ViewModels

## База Данни & Идентификатори (`MongoDB.Bson`)

Всички основни ентити в `DataModels` използват `ObjectId` от `MongoDB.Bson`:
```csharp
public class BaseEntity
{
    public ObjectId Id { get; set; }
    public ObjectId UserId { get; set; }
}
```

## Ключови правила при разработка

1. **Разделение на отговорностите**:
   - `DataModels`: Съдържа само структурите на данните. Без бизнес логика.
   - `SysFunctions`: Съдържа чисти помощни класове (`BNBDataQuery`, `Convertors`, `Validators`, `MonthYearValidator`).
   - `Web.ManagerV2`: Използва разделителния модел `Code-Behind` (отделни `.razor` и `.cs` файлове).

2. **Зависимости и Dependency Injection**:
   - Регистрирайте услугите в `Startup.cs` (`ConfigureServices`) на `Web.ManagerV2` или `Web.API`.

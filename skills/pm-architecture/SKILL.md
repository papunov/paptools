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
- **База данни**: MongoDB / MongoDB Atlas (`MongoDB.Driver`, `MongoDB.Bson`, `ObjectId`)
- **UI фреймуърк**: Bootstrap + Open-Iconic графични икони
- **Мапинг**: AutoMapper за трансформиране между DataModels и ViewModels

---

## 🌐 Свързване към MongoDB Atlas (Cloud Database)

Системата поддържа свързване както към локален MongoDB сървър, така и към **MongoDB Atlas** в облака чрез `mongodb+srv://` протокола.

### 1. Формат на Connection String
```text
mongodb+srv://<username>:<password>@<cluster-name>.mongodb.net/<dbname>?retryWrites=true&w=majority
```
*Забележка: Ако паролата съдържа специални символи като `@`, `:`, `/`, `#`, `%`, те трябва да бъдат URL-кодирани (напр. `@` става `%40`).*

### 2. Конфигурация в .NET (`Web.ManagerV2/appsettings.json`)
```json
{
  "ConnectionStrings": {
    "MongoDB": "mongodb+srv://user:password@cluster.mongodb.net/PersonalManager?retryWrites=true&w=majority"
  }
}
```
*Алтернативно през Environment Variable:*
`ConnectionStrings__MongoDB="mongodb+srv://..."`

### 3. Конфигурация в Python (`PersonalManager-Data-Ingestion / .env`)
Уверете се, че пакетите `pymongo` и `dnspython` са инсталирани в Python средата за поддръжка на SRV записите.
```env
MONGO_URI=mongodb+srv://user:password@cluster.mongodb.net/?retryWrites=true&w=majority
DB_NAME=PersonalManager
```

### 4. Изисквания за Сигурност в MongoDB Atlas
- **Network Access (IP Whitelist)**: Уверете се, че IP адресът на сървъра/компютъра е добавен в IP Access List на MongoDB Atlas (или `0.0.0.0/0` за достъп отвсякъде при защитени потребителски данни).
- **Database User**: Потребителят трябва да има права `readWrite` за базата `PersonalManager`.

---

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

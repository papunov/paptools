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

## 🔒 Защитена Конфигурация за Локална и PROD Среда (Best Practices)

Никога не записвайте тайни (пароли и connection strings за PROD) в Git репозиторията (`appsettings.json` или `.env`).

### 1. За .NET (`Web.ManagerV2` & `Web.API`)

.NET използва конфигурационен йерархичен модел:
- **`appsettings.json`** (Локални стойности по подразбиране, публично в Git):
  ```json
  {
    "ConnectionStrings": {
      "MongoDB": "mongodb://127.0.0.1:27017"
    }
  }
  ```
- **Локална разработка (`dotnet user-secrets`)**:
  Съхранява пароли локално извън сорс кода:
  ```bash
  cd Web.ManagerV2
  dotnet user-secrets init
  dotnet user-secrets set "ConnectionStrings:MongoDB" "mongodb+srv://dev_user:dev_pass@cluster-dev.mongodb.net/PersonalManager"
  ```
- **PROD Среда (Azure / Linux / Docker)**:
  В PROD среда .NET автоматично презаписва `appsettings.json` през **Environment Variables**:
  - Име на променливата: `ConnectionStrings__MongoDB` (обърнете внимание на двойния долна черта `__`).
  - Стойност: `mongodb+srv://prod_user:strong_password@cluster-prod.mongodb.net/PersonalManager?retryWrites=true&w=majority`

### 2. За Python (`PersonalManager-Data-Ingestion`)

- **`.env.example`** (Включен в Git, без реални пароли):
  ```env
  MONGO_URI=mongodb://localhost:27017/
  DB_NAME=PersonalManager
  ```
- **Локална среда (`.env`)**: Файлът `.env` се добавя в `.gitignore` и съдържа локалните ключове.
- **PROD Среда (Docker / Systemd)**:
  Подавайте променливите от секретен масив или през `docker run`:
  ```bash
  docker run -d --name data-ingestion \
    -e MONGO_URI="mongodb+srv://prod_user:strong_password@cluster-prod.mongodb.net/?retryWrites=true&w=majority" \
    -e DB_NAME="PersonalManager" \
    personalmanager-data-ingestion
  ```

---

## 🌐 Свързване към MongoDB Atlas (Cloud Database)

Системата поддържа свързване както към локален MongoDB сървър, така и към **MongoDB Atlas** в облака чрез `mongodb+srv://` протокола.

### 1. Формат на Connection String
```text
mongodb+srv://<username>:<password>@<cluster-name>.mongodb.net/<dbname>?retryWrites=true&w=majority
```
*Забележка: Ако паролата съдържа специални символи като `@`, `:`, `/`, `#`, `%`, те трябва да бъдат URL-кодирани (напр. `@` става `%40`).*

### 2. Изисквания за Сигурност в MongoDB Atlas
- **Ограничен потребител (Database User)**: Създайте отделен потребител за PROD с минимални права (`readWrite` само за базата `PersonalManager`).
- **IP Access List**: Добавете точното IP на PROD сървъра. За контейнери в Azure/AWS използвайте VPC Peering или Private Endpoint.

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

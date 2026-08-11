---
name: pm-blazor-ui
description: >-
  Стандарти за Blazor Server UI компоненти, стилизиране и интерфейс в PersonalManager (Web.ManagerV2).
---

# PersonalManager Blazor UI Guidelines

## 1. Разпределение на Файловете (Code-Behind Патерн)

Всички компоненти и страници в `Web.ManagerV2` задължително се разделят на два файла:
- `ComponentName.razor`: Съдържа само HTML/Blazor разписи, директиви `@page`, `@inject` и компоненти.
- `ComponentName.cs` (или `ComponentName.razor.cs`): `partial class` съдържащ C# кода, логиката, състоянията и инжекциите на услуги.

### Пример:
`Balance.razor`:
```razor
@page "/balance"
@inherits BalanceModel

<h3>Баланс</h3>
```

`Balance.cs`:
```csharp
public class BalanceModel : ComponentBase
{
    [Inject] public IRepository Repository { get; set; }
    // Логика на компонента...
}
```

---

## 2. Стилизиране и Графична Тема (`site.css`)

Проектът използва градиентна странична навигация и Bootstrap стилизация:

- **Странично меню (`sidebar`)**:
  ```css
  .sidebar {
      background-image: linear-gradient(180deg, rgb(5, 39, 103) 0%, #3a0647 70%);
  }
  ```
- **Основен бутон (`.btn-primary`)**:
  - Цвят: `#1b6ec2`
  - Бордер: `#1861ac`
- **Икони**: Използват се Open-Iconic Bootstrap икони (`<span class="oi oi-..." aria-hidden="true"></span>`).

---

## 3. Графика и Визуализации (`HomeDashboards`, `Investments/Graph`)

За графични визуализации на финансови отчети и месечни тенденции се използват графични компоненти капсулирани в:
- `Web.ManagerV2/Components/HomeDashboards/IncomeExpenseDashboard.razor`
- `Web.ManagerV2/Components/Investments/Graph/`

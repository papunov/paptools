---
name: pm-blazor-ui
description: >-
  Стандарти за Blazor Server UI компоненти, унифициран дизайн система, стилизиране, цветова палитра, карти и интерфейс в PersonalManager (Web.ManagerV2).
---

# PersonalManager Blazor UI & Unified Design System Guidelines

Този скил дефинира пълните **стандарти за унифициран дизайн, UI компоненти и архитектура на интерфейса** в **PersonalManager (`Web.ManagerV2`)**. 

---

## 1. Архитектура и Разделение на Кода (Code-Behind Pattern)

Всички страници и компоненти в `Web.ManagerV2` се разделят задължително на два файла:
- **`ComponentName.razor`**: Съдържа само HTML/Blazor разпис, UI структура, Radzen компоненти и `@page`, `@inherits` директиви.
- **`ComponentName.cs`** (или `ComponentName.razor.cs`): `partial class` или наследяващ клас от `ComponentBase`, съдържащ целия C# код, състояния, филтри, инжекции на услуги и обработчици на събития.

### Пример:
`Balance.razor`:
```razor
@page "/balance"
@inherits BalanceModel

<div class="salary-card">
    <div class="salary-title">
        <i class="rzi rzi-wallet"></i>
        <span>Месечен Баланс</span>
    </div>
    <!-- Разпис на компонента -->
</div>
```

`Balance.cs`:
```csharp
public class BalanceModel : ComponentBase
{
    [Inject] public IRepository Repository { get; set; }
    // C# логика и методи...
}
```

---

## 2. Цветова Палитра и Графична Тема (Color Tokens)

Дизайн системата комбинира **Radzen Blazor CSS променливи**, **Bootstrap 5** и персонализирани стилове в `site.css`:

### 🎨 Основни Цветове:
- **Основен син акцент (`--rz-primary` / `.btn-primary`)**: `#1b6ec2` (Hover: `#1861ac`)
- **Странична навигация (`sidebar`)**: Тъмен градиент:
  ```css
  background-image: linear-gradient(180deg, rgb(5, 39, 103) 0%, #3a0647 70%);
  ```
- **Заглавие на навигацията (`sidebar .top-row`)**: `rgba(0,0,0,0.4)`
- **Активен линк в менюто (`.sidebar .nav-item a.active`)**: `rgba(255,255,255,0.25)` с бял текст
- **Ховър на линк в менюто (`.sidebar .nav-item a:hover`)**: `rgba(255,255,255,0.1)` с бял текст
- **Фон на главната страница (`.main .top-row`)**: `#f7f7f7` с долна граница `1px solid #d6d5d5`

### 🟢🔴 Семантични Статуси и Цветове:
- **Приход / Успех (`.text-success`)**: `#198754` (Тъмно зелено, `font-weight: 600`)
- **Разход / Опасност (`.text-danger`)**: `#dc3545` (Червено, `font-weight: 600`)
- **Предупреждение (`.border-left-warning`)**: `#f6c23e` (Жълто/Оранжево)
- **Информационен акцент (`.border-left-info`)**: `#36b9cc` (Светло синьо)

### ⚪ Неутрални Сиви Тонoве (`.text-gray-*`):
- `100`: `#f8f9fc` | `200`: `#eaecf4` | `300`: `#dddfeb` | `400`: `#d1d3e2`
- `500`: `#b7b9cc` | `600`: `#858796` | `700`: `#6e707e` | `800`: `#5a5c69` | `900`: `#3a3b45`

---

## 3. Картова Система и Контейнери (Cards & Layout Containers)

За да се постигне унифициран вид на целия PersonalManager, всички основни модули (Заплати, Калкулатор, Инвестиции, Автомобили) използват **стандартизирани карти**:

### 📦 1. Главна Карта (`.salary-card`, `.calculator-card`)
- **Форма**: Закръглени ъгли `border-radius: 18px`
- **Сянка**: `box-shadow: 0 4px 18px rgba(0,0,0,0.07)` или `0 4px 24px rgba(0,0,0,0.08)`
- **Размери**: Централно подравняване `margin: 0 auto 40px auto`, `max-width: 950px` до `1200px`
- **Падинг**: `28px 22px 22px 22px`

```css
.salary-card {
    background: var(--rz-base-background-color);
    border-radius: 18px;
    box-shadow: 0 4px 18px rgba(0,0,0,0.07);
    padding: 28px 22px 22px 22px;
    max-width: 950px;
    margin: 0 auto 40px auto;
}
```

### 📋 2. Вътрешен Обобщаващ Панел (`.salary-summary`, `.calculator-form`)
- **Форма**: Закръглени ъгли `border-radius: 10px` – `14px`
- **Фон**: `var(--rz-secondary-lighter)` или `#fff`
- **Сянка**: `box-shadow: 0 2px 8px rgba(0,0,0,0.04)`

---

## 4. Типография и Заглавия (Typography Standards)

- **Основен шрифт**: `'Helvetica Neue', Helvetica, Arial, sans-serif`
- **Заглавие на Карта/Страница (`.salary-title`, `.calculator-title`)**:
  - `font-size: 2rem` (`font-weight: 700`), цвят `var(--rz-primary)` или `#fff`
  - Отстояние между икона и текст: `gap: 12px`
- **Подзаглавие на Блок (`.calculator-form-title`)**:
  - `font-size: 1.25rem` (`font-weight: 600`)
- **Заглавия на Таблици (`th`)**:
  - `font-weight: 600`, фон `var(--rz-base-200, #f9f9f9)`, цвят `var(--rz-primary)`

---

## 5. Таблици, Бутони и Икони

### 📊 1. Таблици (`.salary-table` & Radzen DataGrid)
- Подравняване на съдържанието: `text-align: center; padding: 10px 8px; font-size: 1.1rem;`
- Положителни стойности (приходи/спестявания): `.text-success` (`#198754`)
- Отрицателни стойности (разходи): `.text-danger` (`#dc3545`)

### 🔘 2. Бутони (`.salary-btn`, `.btn-primary`)
- Фиксирана ширина за основни действия: `width: 160px; font-size: 1rem;`
- Стандартен син бутон: `background-color: #1b6ec2; border-color: #1861ac;`

### 🖼️ 3. Икони
- **Странична навигация**: Open-Iconic Bootstrap (`<span class="oi oi-home" aria-hidden="true"></span>`)
- **Карти и Заглавия**: Radzen / FontAwesome икони (`<i class="rzi rzi-wallet"></i>` или `<i class="oi oi-graph"></i>`)

---

## 6. Адаптивност за Мобилни Устройства (Mobile Responsiveness)

За поддръжка на смартфони и таблети (включително Android устройства със специфика като Samsung Galaxy и iOS/iPhone) се прилагат следните задължителни архитектурни правила:

### 🛡️ 1. Защита от Разтягане на Екрана (Android Layout Viewport & Flexbox Overflow)
В Android (Chromium/Blink) елементи с `min-width` или разширен хедър/таблица разтягат Layout Viewport на страницата над физическата ширина на екрана (напр. 360–384px на Samsung Galaxy). За да се предотврати хоризонтален скрол и разместване на fixed елементи:
- `html, body { overflow-x: hidden !important; max-width: 100vw !important; width: 100%; }`
- `app, .main { min-width: 0 !important; max-width: 100vw !important; width: 100%; overflow-x: hidden !important; }`
- `.content { min-width: 0 !important; max-width: 100% !important; overflow-x: hidden !important; }`
- На мобилни екрани (`< 768px`) страничният `padding` на `.content` се намалява от десктопния `px-4` (24px) на `0.75rem` (12px), за да остава максимално полезно пространство за съдържанието.

### 🔴 2. Стандарти за Floating Action Button (FAB)
- **Перфектен кръг**: Винаги се форсира `border-radius: 50% !important;` (без разчитане на класове от типа `rz-border-radius-10`).
- **Safe Area поддръжка**: Отстоянията задължително отчитат долната Android жестова навигационна лента (gesture pill) и iOS home bar:
  ```css
  .fab-container {
      position: fixed !important;
      bottom: calc(24px + env(safe-area-inset-bottom, 0px)) !important;
      right: calc(24px + env(safe-area-inset-right, 0px)) !important;
      z-index: 1050 !important;
  }
  @media (max-width: 768px) {
      .fab-container {
          bottom: calc(18px + env(safe-area-inset-bottom, 0px)) !important;
          right: calc(18px + env(safe-area-inset-right, 0px)) !important;
      }
      .fab-main-btn {
          width: 56px !important;
          height: 56px !important;
          border-radius: 50% !important;
      }
  }
  ```

### 📊 3. RadzenDataGrid Мобилен Хоризонтален Скрол
- **Забрана за `overflow: hidden`**: Строго забранено е слагането на `overflow: hidden` върху класа `.custom-grid` или DataGrid контейнери, тъй като това скрива крайните колони и блокира хоризонталния скрол.
- **Вътрешен Touch скрол**: Задава се плавен touch скрол само за табличната част:
  ```css
  .custom-grid, .rz-datatable, .rz-data-grid,
  .rz-data-grid .rz-grid-table-wrapper, .rz-data-grid .rz-data-grid-data {
      overflow-x: auto !important;
      -webkit-overflow-scrolling: touch !important;
      max-width: 100% !important;
  }
  ```
- **Оптимизирани ширини на колоните**: Ключовите колони (напр. Дата 90px, Категория 140px, Сума 120px = 350px) трябва да се събират в <= 350px, за да са изцяло видими на дисплея на мобилни телефони (360px–384px) веднага без начално скролване. Вторичните данни (коментари, бутони за редакция/изтриване) се достъпват с плавно плъзгане.

### 👤 4. Компактен Мобилен Хедър (`LoginDisplay`)
- На екрани `<= 576px` текстът на потребителския имейл се скрива с `display: none !important;` (задължително `!important`, за да пребори Bootstrap `.d-flex`), оставяйки само аватара, бутона за тема и изход.
- На екран `<= 380px` текстът на бутона за изход се скрива (`.logout-btn-custom .rz-button-text { display: none !important; }`), оставяйки само компактна икона.

### 📱 5. Viewport Meta Tag в `_Host.cshtml`
- Задължителна конфигурация за PWA и мобилни браузъри:
  ```html
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover" />
  ```
  `viewport-fit=cover` активира правилната работа на `env(safe-area-inset-*)`, а `maximum-scale=1.0, user-scalable=no` предотвратява случайно мащабиране на layout viewport в Android.

---

## 7. Тъмна Тема & Модерни CSS Токени (Dark Mode Standards)

За пълна съвместимост между светла и тъмна тема е **абсолютно забранено** използването на твърдо кодирани светли цветове (`bg-white`, `bg-light`, `text-dark`, `#1e293b`, `#2d3436`, `#e2e8f0`) върху карти, таблици, заглавия, модални диалози или баджове.

### 🎨 Основни CSS Променливи (Theme Tokens):
| Променлива | Предназначение | Светла тема | Тъмна тема |
| :--- | :--- | :--- | :--- |
| `var(--card-bg)` | Фон на карти, панели и модални прозорци | `#ffffff` | `#1e293b` / `#0f172a` |
| `var(--text-primary)` | Основен четим текст и заглавия | `#1e293b` | `#f8fafc` |
| `var(--bg-primary)` | Основен фон на страницата | `#f8fafc` | `#0f172a` |
| `var(--bg-secondary)` | Вторичен фон (инпути, баджове) | `#f1f5f9` | `#1e293b` |
| `var(--bg-tertiary)` | Третичен фон (картички, информационни блокове) | `#e2e8f0` | `#334155` |
| `var(--border-color)` | Рамки на карти, таблици и диалози | `#e2e8f0` | `rgba(255,255,255,0.12)` |

### ⚠️ Анти-шаблони и корекции:
- ❌ `<div class="bg-white">` ➔ ✅ `<div style="background-color: var(--card-bg); color: var(--text-primary);">`
- ❌ `<h2 style="color: #1e293b;">` ➔ ✅ `<h2 style="color: var(--text-primary);">`
- ❌ `<span class="badge bg-light text-dark">` ➔ ✅ `<span class="badge border" style="background-color: var(--bg-tertiary); color: var(--text-primary); border-color: var(--border-color) !important;">`
- ❌ `<span class="fw-bold text-dark">` ➔ ✅ `<span class="fw-bold" style="color: var(--text-primary);">`

---

## 8. UX Защита на Данните & Диалози за Потвърждение (Data Protection)

При всяко действие за перманентно изтриване на данни от потребителя (автомобили, сервизни записи, ремонти, горива, лизинги, повтарящи се разходи, депозити, валути), **задължително** се изисква предварително потвърждение чрез `DialogService.Confirm`.

### Стандартен шаблон за потвърждение:
```csharp
[Inject] public DialogService DialogService { get; set; }

private async Task DeleteItem(ItemModel item)
{
    var confirm = await DialogService.Confirm(
        $"Сигурни ли сте, че искате да изтриете '{item.Name}'?",
        "Потвърждение за изтриване",
        new ConfirmOptions 
        { 
            OkButtonText = "Да, изтрий", 
            CancelButtonText = "Отказ" 
        });

    if (confirm != true) return;

    await Service.DeleteAsync(item.Id);
    NotificationService.Notify(NotificationSeverity.Success, "Успех", "Записът е изтрит");
    await Reload();
}
```

---

## 9. Предотвратяване на Безкрайно Зареждане (Infinite Spinners) & Empty States

### 🚫 Анти-шаблон с `Count == 0`:
Никога не използвайте `IsLoading="Model.Count == 0"` или `@if (Model.Count == 0 || exporting)`:
- При легитимно празна колекция (0 записи), спинърът за зареждане се върти безкрайно и блокира потребителския интерфейс.

### ✅ Правилен подход:
Използвайте експлицитен булев флаг `isLoading`, който се управлява в `OnInitializedAsync`:
```csharp
private bool isLoading = true;

protected override async Task OnInitializedAsync()
{
    isLoading = true;
    try
    {
        Model = await Service.GetDataAsync();
    }
    finally
    {
        isLoading = false;
    }
}
```

### 🏷️ Задължителен `EmptyText` за RadzenDataGrid:
Всяка таблица `RadzenDataGrid` трябва да има дефиниран `EmptyText` на български език:
```razor
<RadzenDataGrid Data="@Model" TItem="ItemModel" 
                IsLoading="@isLoading"
                EmptyText="Няма намерени записи."
                Density="Density.Compact" Class="custom-grid shadow-sm">
```

---

## 10. Модални Диалози и Форми (Radzen Dialogs)

Всички диалогови компоненти (напр. `AddCarDialog`, `AddMaintenanceDialog`, `AddFuelDialog`, `AddLeaseContractDialog`, `AddLeaseEntryDialog`, `AddNewTransaction`):
1. **Тематичен контейнер**: Използват `var(--card-bg)` и `var(--text-primary)` без фиксиран `bg-white`.
2. **Контроли за избор**: `RadzenDatePicker`, `RadzenNumeric`, `RadzenDropDown` и `RadzenSelectBar` се оразмеряват с пълен `w-100` на мобилни екрани и отстояния `mb-3`.
3. **Затваряне**: Съдържат бутон за отказ, извикващ `DialogService.Close(null)`.

---

## 11. Навигационни Хлябни Трохи (Breadcrumbs & URL Mapping)

Всеки маршрут в Blazor приложението се визуализира в навигационната пътека (Breadcrumbs) в горната част на екрана през [`MainLayout.razor`](file:///c:/Users/papun/RiderProjects/PersonalManager/PersonalManager/Web.ManagerV2/Shared/MainLayout.razor).

### 🏷️ Задължителен Мапинг в `_mapUrl`:
При добавяне на всяка нова страница (`@page "/segment"` или `@page "/category/subpage"`), съответният URL сегмент **задължително** трябва да се регистрира в речника `_mapUrl` в `MainLayout.razor` с неговия български превод:
```csharp
private readonly Dictionary<string, string> _mapUrl = new Dictionary<string, string>() {
    // ...
    {"cars", "Автомобили"},
    {"documents", "Документи"},
    {"maintenance", "Сервиз"},
    {"fuel-records", "Зареждане с гориво"},
    {"leasing", "Лизинг"},
    {"statistics", "Статистика"},
    {"cloud-upload", "GCP Качване"}
};
```
> **⚠️ Внимание**: Ако сегментът не е деклариран в `_mapUrl`, навигационната лента ще изпише суровия английски URL път (напр. `» documents` вместо `» Документи`).

---

## 12. Чеклист за Нов или Рефакториран UI Компонент

1. ✅ **Разделение на кода**: `Page.razor` за разпис и `Page.cs` (или `.razor.cs`) за C# логика.
2. ✅ **Регистрация в Breadcrumbs (`_mapUrl`)**: Задължително добавяне на URL сегмента в `_mapUrl` в `MainLayout.razor` с българско име.
3. ✅ **Тъмна тема**: Без твърдо кодирани `bg-white`, `bg-light`, `text-dark`, `#1e293b`. Използване на `var(--card-bg)`, `var(--text-primary)`, `var(--border-color)`.
4. ✅ **Защита при изтриване**: `DialogService.Confirm` с персонализиран въпрос и бутони на български език.
5. ✅ **Зареждане без безкрайни спинъри**: Булев флаг `isLoading` с `finally { isLoading = false; }`.
6. ✅ **Празни състояния**: Дефиниран `EmptyText` за всяка таблица `RadzenDataGrid`.
7. ✅ **Адаптивност & Екрани (Android & iOS)**:
   - Без хоризонтален скрол на страницата (`overflow-x: hidden !important` на `app`, `.main`).
   - Таблиците имат вътрешен touch скрол (`overflow-x: auto`), без `overflow: hidden` върху грида.
   - Основните колони се събират в `<= 350px`, за да са видими на телефони (360px–384px) веднага.
   - Плаващите бутони (FAB) имат `border-radius: 50% !important;` и отстояния със `safe-area-inset`.
   - Хедърът крие дълги потребителски имейли под 576px с `display: none !important;`.
8. ✅ **Reconnection Modal при заспиване**: Дефиниран потребителски `<div id="components-reconnect-modal">` в `_Host.cshtml` с поддръжка на всички състояния (`show`, `retrying`, `failed`, `rejected`) и тъмна тема.

---

## 13. Прекъсната Връзка и Заспиване на Сайта (Reconnection UI & Sleep Mode)

Когато устройството (телефон, таблет или лаптоп) заспи или мрежата прекъсне, SignalR веригата (Circuit) на Blazor Server се разпада. В този момент Blazor активира `#components-reconnect-modal`.

### ⚠️ Проблемът с динамичния fallback:
Ако елементът `#components-reconnect-modal` липсва в HTML кода на `_Host.cshtml`, Blazor динамично създава fallback елемент със служебен бял фон. При стилизиране на текста в бяло за тъмна тема се получава **бял текст върху бял фон**, който е напълно нечетим.

### 🛡️ Архитектурен стандарт в PersonalManager:
1. **Експлицитен HTML в `_Host.cshtml`**:
   Елементът `<div id="components-reconnect-modal" class="components-reconnect-hide">` се поставя задължително в `<body>` преди `<script src="_framework/blazor.server.js">`.
2. **Четири състояния на връзката**:
   - `components-reconnect-show`: Първоначален опит за бързо свързване (със спинър).
   - `components-reconnect-retrying`: Повторен опит с по-дълъг backoff (със спинър).
   - `components-reconnect-failed`: Неуспешно свързване при липса на мрежа (с бутон „Опитай отново“, извикващ `window.Blazor.reconnect()`).
   - `components-reconnect-rejected`: Прекратена сесия при продължително заспиване или рестарт на сървъра (с бутон „Презареди страницата“, извикващ `location.reload()`).
3. **Визуален стил**:
   - Тъмен замъглен бекдроп: `background: rgba(11, 15, 25, 0.75) !important; backdrop-filter: blur(10px) !important;`.
   - Тъмна карта: `background-color: #182234 !important; border: 1px solid rgba(255, 255, 255, 0.1) !important; border-radius: 20px;`.
   - Градиентен акцентен бутон: `background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%)`.
   - Автоматичен овъррайд за `html.theme-light` (бяла карта с тъмен текст `#0f172a`).




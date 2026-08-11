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

За поддръжка на смартфони и таблети се прилагат следните адаптивни правила:

```css
@media (max-width: 900px) {
    .salary-card, .calculator-card {
        padding: 12px 4px 10px 4px;
        margin: 14px 0;
    }
    .salary-title, .calculator-title {
        font-size: 1.2rem;
        padding: 14px 8px;
    }
    .salary-table th, .salary-table td {
        font-size: 0.95rem;
        padding: 6px 2px;
    }
}

@media (max-width: 767.98px) {
    .main .top-row:not(.auth) {
        display: none;
    }
    .sidebar {
        width: 100%;
    }
}
```

---

## 7. Чеклист за Нов UI Компонент

Когато създавате нов екран или рефакторирате съществуващ компонент:
1. ✅ Разделете кода в два файла: `Page.razor` и `Page.cs`.
2. ✅ Опаковайте съдържанието в `.salary-card` или `.calculator-card`.
3. ✅ Използвайте градиентния `sidebar` за навигация и `--rz-primary` (`#1b6ec2`) за заглавия/бутони.
4. ✅ Форматирайте финансовите суми: зелено за приходи (`.text-success`), червено за разходи (`.text-danger`).
5. ✅ Тествайте мобилния изглед при ширина `< 900px`.

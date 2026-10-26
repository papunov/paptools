---
name: pm-investments
description: >-
  Правила за управление на инвестиционния портфейл (акции, взаимни фондове, дивиденти, депозити, валутни курсове от БНБ) в PersonalManager.
---

# PersonalManager Investments Guide

## 1. Акции и ETF (`Stock.cs`, `InvestStockOrder.cs`)

Моделът `Stock` управлява позициите в акции и ETF-и:

```csharp
public class Stock
{
    public ObjectId Id { get; set; }
    public string BrokerName { get; set; }
    public string Ticker { get; set; }
    public bool Is_ETF { get; set; }
    public decimal Shares { get; set; }
    public decimal AverageSharePrice { get; set; }
    public string Currency { get; set; } // EUR, USD, BGN и др.
    public decimal SumInEUR { get; set; }
    public decimal SumInBGN { get; set; }
    public decimal DividendsInBGN { get; set; }
    public decimal DividendTaxesInBGN { get; set; }
    public decimal FeeInBGN { get; set; }
    public decimal Delta => DividendsInBGN - DividendTaxesInBGN - FeeInBGN;
}
```

### Формули за преизчисление:
- **Чист доход (`Delta`)**: `DividendsInBGN - DividendTaxesInBGN - FeeInBGN`
- При придобиване или покупка на акции се изчислява **средно претеглена цена (`AverageSharePrice`)**.

---

## 2. Фондове, Депозити и Кешови Наличности (`Fund.cs`, `Deposit.cs`, `Dividend.cs`, `CashCurrencies.cs`)

- **`Fund`**: Взаимни и инвестиционни фондове с изчисление на дял и текуща стойност.
- **`Deposit`**: Спестовни депозити с изчисление на лихва (`CashInterest`) и срочност.
- **`Dividend`**: Записи за получени дивиденти по акции с държавен данък и чиста сума.
- **`CashCurrencies`**: Кешови наличности по валути (EUR, USD, BGN) и сметки в брокери или банки.

---

## 3. Валутни курсове от БНБ (`SysFunctions.BNBDataQuery`)

За автоматично конвертиране на чуждестранни валути (USD, PLN, EUR) към BGN се използва класът `BNBDataQuery`:

- **Източник**: Страницата за валутни курсове на БНБ (`https://bnb.bg/Statistics/...`).
- **Метод**: `CurrencyToBGNForDate(currency, year, month, day)` парсва HTML таблицата на БНБ чрез Regular Expressions и връща курса за дадената дата.

```csharp
var bnbQuery = new BNBDataQuery();
decimal rateBGN = await bnbQuery.CurrencyToBGNForDate("USD", 2026, 8, 11);
```

---

## 4. UI Архитектура и Инвестиционни Модули (`Web.ManagerV2/Components/Investments`)

Инвестиционният хъб се управлява от `Investments.razor` и набор от специализирани компоненти:

1. **`Investments.razor` (Главен екран)**:
   - Организиран в табове (`RadzenTabs`): Акции, Дивиденти, Фондове, Депозити, Кеш, Ордери, Логове, Обновяване на цени.
   - Използва общи KPI карти с обобщени данни за портфолиото.
2. **`DividendsDashboard.razor`**:
   - Табло за получени дивиденти по месеци и години с изчисление на бруто, данък и нетна сума.
   - Стилизирани карти и таблици, съвместими с тъмна и светла тема.
3. **`DepositManagement.razor`**:
   - Управление на банкови/спестовни депозити, лихвени проценти, падежи и изплатени лихви.
   - **Защита на данните**: Изтриването на депозит изисква потвърждение с `DialogService.Confirm`.
   - **Зареждане**: Използва булева променлива `isLoading` и `EmptyText="Няма намерени депозити"`.
4. **`CashManagement.razor`**:
   - Следене на кешови средства по брокери и валути.
   - **Защита на данните**: Потвърждение с `DialogService.Confirm` преди изтриване.
   - Зареждане с `isLoading` и дефиниран `EmptyText`.
5. **`FundManagement.razor`**:
   - Управление на взаимни фондове, брой дялове, цена на дял и печалба/загуба.
6. **`StockDetails.razor` & `StockDailyPrice.razor`**:
   - Подробен профил на избрана позиция/тикер с исторически цени, графики и изпълнени поръчки.
7. **`MonthlyIncome.razor`**:
   - Прогноза и статистика за пасивен доход от дивиденти и депозити по месеци.
8. **`UpdateCurrentStockPrices.razor`**:
   - Модул за ръчно стартиране на обновяването на котировки от борсите и интеграция с AI прогнозите.
9. **`Logs.razor`**:
   - Таблица със системни логове за фонови процеси и борсови транзакции.
10. **`ManualOrders.razor` / `UploadInvestOrders.razor`**:
    - Ръчно въвеждане и парсване на CSV отчети за борсови ордери.

---

## 5. UI, UX и Dark Theme Стандарти за Инвестициите

- **CSS Токени**: Всички инвестиционни таблици, форми и карти **задължително** ползват `var(--card-bg)`, `var(--text-primary)`, `var(--bg-primary)`, `var(--border-color)` и избягват `bg-white`, `bg-light` или `#1e293b`.
- **Защита срещу безкрайно зареждане**: Забранено е ползването на `IsLoading="Model.Count == 0"`. Всички гридове ползват изричен булев флаг `isLoading = true` по време на заявки и `isLoading = false` в `finally` блок.
- **Празни състояния**: Всеки `RadzenDataGrid` съдържа локализиран текст `EmptyText="Няма намерени записи"`.
- **Потвърждение при изтриване**: Всички деструктивни действия ползват асинхронен `DialogService.Confirm("Сигурни ли сте, че искате да изтриете...?", "Потвърждение", ...)` с бутони "Да" / "Не".

---

## 6. Rebalancing Калкулатор & Дивидентен Календар (PADI) (`IRebalancingService.cs`, `RebalancingService.cs`, `InvestmentRebalancing.razor`)

Модулът `/rebalancing` предоставя Buy-Only оптимизатор на портфолиото и прогнозен 12-месечен календар на пасивния дивидентен доход (PADI):

### 1. Buy-Only Ребалансиране (Без Продажби)
- **Принцип**: Ребалансирането се извършва **единствено** чрез алокация на нов входящ капитал (`cashToInvest`), без генериране на данъчни събития или брокерски такси за изход/продажба.
- **Формула за целева стойност**:
  $$\text{Target EUR}_i = (\text{Current Portfolio Value} + \text{Cash To Invest}) \times \frac{\text{Target Weight}_i}{100}$$
- **Дефицит (Shortfall)**:
  $$\text{Shortfall}_i = \max(0, \text{Target EUR}_i - \text{Current Value}_i)$$
- **Разпределение на кеша**: Капиталът се насочва пропорционално към позициите с дефицит ($\text{Shortfall}_i > 0$).
- **Препоръчани цели акции**: $\lfloor \text{Allocated Cash}_i / \text{Current Share Price}_i \rfloor$. Позициите с тегло над целта се маркират със статус "Запази (Над целта) — без продажба".

### 2. Дивидентен Календар & PADI (Projected Annual Dividend Income)
- **PADI на акция**: На база последно изплатен дивидент от `Dividends` и фундаментални данни от `StockFundaments`.
- **12-месечен график**: Календар от януари до декември с очакван приход в EUR и брой плащащи компании за всеки месец.

### 3. Google Gemini AI Стратегически Анализ & Персистиране в MongoDB
- **Персистиране (`AIRebalancingAnalyses`)**: Резултатът от AI анализа се записва в MongoDB Atlas (`AIRebalancingAnalyses`), осигурявайки незабавно зареждане без излишни API повиквания.
- **Целеви тегла (`RebalancingTargetSettings`)**: Потребителски настройки за желаното процентно тегло на всяка акция с модален диалогов прозорец за редакция.

---

## 7. Карол Мобилен Скенер за Скрийншоти (Gemini AI Vision & Дедупликация)

Модулът за импортиране от мобилното приложение на Карол (`KarollScreenshotImportDialog.razor`, `IGeminiAiService.cs`, `IInvestmentService.cs`) позволява качване на скрийншоти от екраните **"Сделки"** (Trades) и **"Отчет"** (Statements/Dividends):

### 1. Vision Анализ (`GeminiAiService.ParseKarollScreenshotAsync`)
- Използва `gemini-2.5-flash` с мултимодален промпт, обучен за специфичния български UI на приложението на Карол.
- Автоматично разпознава дали изображението е "Сделки", "Отчет" или комбиниран екран.
- **Екстракция на поръчки**: Тикер/Инструмент (напр. `VGWD`, `VGVF`, `TSLA`), дата и час, брой акции, единична цена, валута (EUR/USD/BGN), брокерска такса/комисиона и обща стойност.
- **Екстракция на дивиденти**: Тикер, дата на плащане, чиста нетна сума, брутна сума, удържан данък и референция.

### 2. Интелигентна Дедупликация (`InvestmentService.DeduplicateKarollItemsAsync`)
- **Поръчки (`InvestStockOrders`)**: 
  - Проверява съществуващи записи за брокер `"Karoll"` по `Ticker` и брой закупени акции (`Shares`).
  - **Времева толерантност**: Сравнява както UTC, така и локална дата (`ToLocalTime().Date`) с прозорец $\pm 1$ ден / $\pm 36$ часа (за преодоляване на часовата разлика между полунощ в "Отчет" и точното време на сделката в "Сделки").
  - Проверява съвпадение по сума ($\pm 0.50$ €), единична цена ($\pm 0.05$ €) или съвпадение в `OrderID`/`OrderRef`.
  - **Двойна защита**: Дори при директен импорт в `ImportKarollItemsAsync` се извършва вторична верификация срещу базата за предотвратяване на дублирани записи.
- **Дивиденти (`Dividends`)**: Проверява за съществуващ дивидент с допустим прозорец на датата ($\pm 2$ дни) и съвпадение на чистата сума (`SumAfterTaxInEUR`) или брутната сума (`GrossSumInEUR`).
- Всички дублирани позиции се маркират с бейдж **"Вече в базата"** и се изключват от селекцията за импорт по подразбиране.

### 3. Запис и Автоматично Преизчисление (`InvestmentService.ImportKarollItemsAsync`)
- Записва маркираните нови поръчки в `InvestStockOrders` и дивиденти в `Dividends`.
- Извиква автоматично `UpdateStocks()`, за да преизчисли среднопретеглените цени, брой акции, инвестираните суми в EUR/BGN и делтата на цялото портфолио.

### 4. Поставяне директно от клипборда (Clipboard Paste & Ctrl+V)
- **Клавиатурна комбинация `Ctrl+V`**: Глобален JS listener (`window.clipboardPasteBridge`) при отворен диалогов прозорец прихваща `paste` събития и извлича изображението от `clipboardData.items` или `clipboardData.files`.
- **Бутон "Постави от клипборда"**: Използва браузерното Clipboard API (`navigator.clipboard.read()`) за директно четене на изображение от системния буфер.
- Поддържа последователно поставяне на няколко снимки с автоматично обединяване и дедупликация.

---

## 8. AI Скенер за Отчети на Инвестиционни Фондове (Gemini Vision, Нови Дялове & Автоматични Такси)

Модулът за импортиране на отчети за взаимни и застрахователно-инвестиционни фондове (`FundScreenshotImportDialog.razor`, `FundScanModels.cs`, `IGeminiAiService.cs`, `IInvestmentService.cs`) позволява сканиране на официални клиентски отчети (напр. **Allianz Bulgaria - Застраховка Живот с Фондове**, ДСК, Елана и др.) чрез файл или клипборд (`Ctrl+V`):

### 1. Vision Анализ (`GeminiAiService.ParseFundStatementScreenshotAsync`)
- Използва мултимоделен промпт с `gemini-2.5-flash` за разчитане на:
  - **Институция**: напр. `Allianz`, `ДСК Управление на активи` и др.
  - **Дати**: Отчетна дата (`reportDate`), Дата на оценка (`valuationDate`), Дата на плащане/вноска (`paymentDate`).
  - **Суми по полица/сметка**: Последна вноска/премия (`lastPaymentAmount`), обща инвестирана сума (`totalInvestedAmount`), стойност на сметката (`accountValue`).
  - **Списък фондове**: Име на стратегията/фонда (напр. `Агресивна стратегия`), цена на дял (`unitPrice`), общ брой дялове (`totalUnits`), стойност в EUR (`totalValue`).

### 2. Изчисление на нови дялове спрямо базата (`InvestmentService.PrepareFundScanItemsAsync`)
- **Мапване към базата данни**: Търси съществуващ фонд в `Funds` по пълно или частично съвпадение на името. Ако липсва, подготвя създаване на нов фонд.
- **Текущи дялове в базата**: Извлича акумулираните дялове от предходни ордери:
  $$\text{ExistingUnitsInDb} = \sum \text{BuyShares} - \sum \text{SellShares}$$
- **Изчисление на нови дялове**:
  $$\text{NewUnitsToAdd} = \max(0, \text{TotalUnitsFromReport} - \text{ExistingUnitsInDb})$$
- Ако $\text{NewUnitsToAdd} = 0$, диалогът информира потребителя, че всички дялове са въведени, и предлага бутон само за обновяване на текущата цена на фонда.

### 3. Автоматично изчисление на таксата спрямо внесената сума
- Потребителят въвежда или преглежда внесената премия (`DepositedAmount`):
  $$\text{CostOfUnits} = \text{NewUnitsToAdd} \times \text{UnitPrice}$$
  $$\text{CalculatedFee} = \max(0, \text{DepositedAmount} - \text{CostOfUnits})$$
- При запис в `FundOrders`:
  - `Shares = NewUnitsToAdd`
  - `SharePrice = UnitPrice`
  - `SumInCurrency = DepositedAmount`
  - `FeeInCurrency = CalculatedFee`
  - Автоматично обновява `CurrentPriceInCurrency` на фонда и извиква `InvestmentService.UpdateFunds()` за преизчисление на целия портфейл.


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

## 2. Фондове и Депозити (`Fund.cs`, `Deposit.cs`, `Dividend.cs`)

- **`Fund`**: Взаимни и инвестиционни фондове с изчисление на дял и текуща стойност.
- **`Deposit`**: Спестовни депозити с изчисление на лихва (`CashInterest`) и срочност.
- **`Dividend`**: Записи за получени дивиденти по акции с държавен данък и чиста сума.

---

## 3. Валутни курсове от БНБ (`SysFunctions.BNBDataQuery`)

За автоматично конвертиране на чуждестранни валути (USD, PLN, EUR) към BGN се използва класът `BNBDataQuery`:

- **Източник**: Страницата за валутни курсове на БНБ (`https://bnb.bg/Statistics/...`).
- **Метод**: `CurrencyToBGNForDate(currency, year, month, day)` парсва HTML таблицата на БНБ чрез Regular Expressions и връща курса за дадената дата.

```csharp
var bnbQuery = new BNBDataQuery();
decimal rateBGN = await bnbQuery.CurrencyToBGNForDate("USD", 2026, 8, 11);
```
